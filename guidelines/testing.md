# Testing Guidelines

Comprehensive testing conventions and best practices for building reliable software.

## Core Testing Philosophy

- **Test-Driven Reliability**: Always include comprehensive tests with any code implementation
- **Fail Fast**: Tests should provide clear, immediate feedback when something breaks
- **Real Interactions**: Tests should use actual database operations for authenticity
- **Mock Sparingly**: Only mock external dependencies (APIs, payment processors, email services)
- **Keep Tests Simple**: Avoid complex branching logic in tests

---

## Test Categories and Scope

### Unit Tests
**Purpose**: Test individual models, services, and utilities in isolation

**Characteristics**:
- Fast execution
- Test single responsibility
- Hit the database (no excessive mocking)
- Focus on business logic

**Example (RSpec)**:
```ruby
# spec/models/user_spec.rb
RSpec.describe User, type: :model do
  describe 'validations' do
    it { should validate_presence_of(:email) }
    it { should validate_uniqueness_of(:email).case_insensitive }
  end

  describe '#full_name' do
    it 'combines first and last name' do
      user = create(:user, first_name: 'John', last_name: 'Doe')
      expect(user.full_name).to eq('John Doe')
    end
  end

  describe '#admin?' do
    it 'returns true for admin role' do
      user = create(:user, role: 'admin')
      expect(user).to be_admin
    end

    it 'returns false for non-admin role' do
      user = create(:user, role: 'member')
      expect(user).not_to be_admin
    end
  end
end
```

### Integration Tests
**Purpose**: Test interactions between components

**Characteristics**:
- Test component collaboration
- Include database operations
- May involve multiple models/services
- Verify data flow between parts

**Example (RSpec)**:
```ruby
# spec/services/order_service_spec.rb
RSpec.describe OrderService do
  describe '.create_order' do
    let(:user) { create(:user) }
    let(:product) { create(:product, stock: 10) }

    it 'creates order and updates inventory' do
      params = {
        user_id: user.id,
        line_items_attributes: [
          { product_id: product.id, quantity: 2 }
        ]
      }

      order = OrderService.create_order(params)

      expect(order).to be_persisted
      expect(order.line_items.count).to eq(1)
      expect(product.reload.stock).to eq(8)
    end

    it 'sends confirmation email' do
      params = { user_id: user.id }

      expect {
        OrderService.create_order(params)
      }.to have_enqueued_job(ActionMailer::MailDeliveryJob)
    end
  end
end
```

### System/Feature Tests
**Purpose**: End-to-end user workflows

**Characteristics**:
- Test from user perspective
- Use browser automation (Capybara/Selenium)
- Focus on happy path scenarios
- Avoid complex branching logic
- More expensive to run

**Example (RSpec + Capybara)**:
```ruby
# spec/system/article_creation_spec.rb
RSpec.describe 'Article creation', type: :system do
  it 'allows user to create and publish article' do
    user = create(:user)
    login_as(user)

    visit new_article_path
    fill_in 'Title', with: 'My Great Article'
    fill_in 'Body', with: 'This is the content'
    click_button 'Create Article'

    expect(page).to have_content('Article was successfully created')
    expect(page).to have_content('My Great Article')
  end
end
```

---

## Rails Testing with RSpec

### Setup and Configuration

**Gemfile**:
```ruby
group :development, :test do
  gem 'rspec-rails'
  gem 'factory_bot_rails'
  gem 'faker'
end

group :test do
  gem 'capybara'
  gem 'selenium-webdriver'
  gem 'shoulda-matchers'
  gem 'database_cleaner-active_record'
end
```

**spec/rails_helper.rb**:
```ruby
require 'spec_helper'
ENV['RAILS_ENV'] ||= 'test'
require_relative '../config/environment'
abort("The Rails environment is running in production mode!") if Rails.env.production?

require 'rspec/rails'
require 'capybara/rspec'

# Configure Database Cleaner
RSpec.configure do |config|
  config.use_transactional_fixtures = true
  config.include FactoryBot::Syntax::Methods

  # Capybara configuration
  config.before(:each, type: :system) do
    driven_by :selenium_chrome_headless
  end
end

# Shoulda Matchers configuration
Shoulda::Matchers.configure do |config|
  config.integrate do |with|
    with.test_framework :rspec
    with.library :rails
  end
end
```

### Model Specs

**Test Structure**:
```ruby
RSpec.describe Article, type: :model do
  # Associations
  describe 'associations' do
    it { should belong_to(:user) }
    it { should have_many(:comments).dependent(:destroy) }
    it { should have_many(:tags).through(:taggings) }
  end

  # Validations
  describe 'validations' do
    it { should validate_presence_of(:title) }
    it { should validate_length_of(:title).is_at_most(200) }
    it { should validate_inclusion_of(:status).in_array(%w[draft published archived]) }
  end

  # Scopes
  describe 'scopes' do
    describe '.published' do
      it 'returns only published articles' do
        published = create_list(:article, 2, status: 'published')
        create(:article, status: 'draft')

        expect(Article.published).to match_array(published)
      end
    end
  end

  # Instance methods
  describe '#publish!' do
    it 'sets status to published and records timestamp' do
      article = create(:article, status: 'draft')

      article.publish!

      expect(article.status).to eq('published')
      expect(article.published_at).to be_present
    end
  end
end
```

### Controller Specs

**Keep Lightweight** - Focus on response codes, redirects, and basic data flow:
```ruby
RSpec.describe ArticlesController, type: :controller do
  let(:user) { create(:user) }
  let(:article) { create(:article, user: user) }

  before { sign_in user }

  describe 'GET #index' do
    it 'returns success response' do
      get :index
      expect(response).to have_http_status(:success)
    end

    it 'assigns @articles' do
      articles = create_list(:article, 3)
      get :index
      expect(assigns(:articles)).to match_array(articles)
    end
  end

  describe 'POST #create' do
    context 'with valid parameters' do
      let(:valid_params) { { article: attributes_for(:article) } }

      it 'creates a new article' do
        expect {
          post :create, params: valid_params
        }.to change(Article, :count).by(1)
      end

      it 'redirects to the article' do
        post :create, params: valid_params
        expect(response).to redirect_to(Article.last)
      end
    end

    context 'with invalid parameters' do
      let(:invalid_params) { { article: { title: '' } } }

      it 'does not create an article' do
        expect {
          post :create, params: invalid_params
        }.not_to change(Article, :count)
      end

      it 'renders new template' do
        post :create, params: invalid_params
        expect(response).to render_template(:new)
      end
    end
  end
end
```

### Request Specs (Preferred over Controller Specs)

```ruby
RSpec.describe 'Articles', type: :request do
  let(:user) { create(:user) }

  before { sign_in user }

  describe 'GET /articles' do
    it 'returns successful response' do
      get articles_path
      expect(response).to have_http_status(:success)
    end
  end

  describe 'POST /articles' do
    it 'creates article with valid params' do
      expect {
        post articles_path, params: { article: attributes_for(:article) }
      }.to change(Article, :count).by(1)

      expect(response).to redirect_to(Article.last)
      follow_redirect!
      expect(response.body).to include('Article was successfully created')
    end
  end
end
```

### System Specs (Feature Tests)

**Single Happy Path Scenarios**:
```ruby
RSpec.describe 'User manages articles', type: :system do
  let(:user) { create(:user) }

  before { login_as(user) }

  it 'creates, edits, and deletes article' do
    # Create
    visit new_article_path
    fill_in 'Title', with: 'My Article'
    fill_in 'Body', with: 'Article content'
    click_button 'Create Article'

    expect(page).to have_content('Article was successfully created')

    # Edit
    click_link 'Edit'
    fill_in 'Title', with: 'Updated Article'
    click_button 'Update Article'

    expect(page).to have_content('Updated Article')

    # Delete
    accept_confirm do
      click_link 'Delete'
    end

    expect(page).to have_content('Article was successfully deleted')
    expect(page).not_to have_content('Updated Article')
  end
end
```

---

## JavaScript Testing

### Testing Frameworks
- **Jest**: Full-featured testing framework (React, Node.js)
- **Vitest**: Fast Vite-native test runner
- **For Hotwire/Stimulus**: Prefer Rails system tests

### Jest Example
```javascript
// src/utils/formatters.test.js
import { formatCurrency, formatDate } from './formatters';

describe('formatCurrency', () => {
  test('formats number as USD currency', () => {
    expect(formatCurrency(1234.56)).toBe('$1,234.56');
  });

  test('handles zero', () => {
    expect(formatCurrency(0)).toBe('$0.00');
  });

  test('rounds to 2 decimal places', () => {
    expect(formatCurrency(10.999)).toBe('$11.00');
  });
});

describe('formatDate', () => {
  test('formats date correctly', () => {
    const date = new Date('2024-01-15');
    expect(formatDate(date)).toBe('January 15, 2024');
  });
});
```

### React Component Testing
```javascript
import { render, screen, fireEvent } from '@testing-library/react';
import UserProfile from './UserProfile';

describe('UserProfile', () => {
  const mockUser = {
    id: 1,
    name: 'John Doe',
    email: 'john@example.com'
  };

  test('renders user information', () => {
    render(<UserProfile user={mockUser} />);

    expect(screen.getByText('John Doe')).toBeInTheDocument();
    expect(screen.getByText('john@example.com')).toBeInTheDocument();
  });

  test('calls onEdit when edit button clicked', () => {
    const handleEdit = jest.fn();
    render(<UserProfile user={mockUser} onEdit={handleEdit} />);

    fireEvent.click(screen.getByRole('button', { name: /edit/i }));

    expect(handleEdit).toHaveBeenCalledWith(mockUser);
  });
});
```

### Stimulus Controller Testing (via Rails System Tests)
```ruby
# test/system/comments_test.rb
require 'application_system_test_case'

class CommentsTest < ApplicationSystemTestCase
  test 'adding a comment with Stimulus' do
    article = articles(:one)
    visit article_path(article)

    # Stimulus controller should handle this
    fill_in 'comment_body', with: 'Great article!'
    click_button 'Post Comment'

    # Turbo Stream should update the page
    assert_selector '#comments', text: 'Great article!'
    assert_field 'comment_body', with: ''  # Form should be cleared
  end
end
```

---

## Test Data Management

### FactoryBot

**Defining Factories**:
```ruby
# spec/factories/users.rb
FactoryBot.define do
  factory :user do
    first_name { 'John' }
    last_name { 'Doe' }
    email { Faker::Internet.unique.email }
    role { 'member' }

    trait :admin do
      role { 'admin' }
    end

    trait :with_articles do
      transient do
        articles_count { 3 }
      end

      after(:create) do |user, evaluator|
        create_list(:article, evaluator.articles_count, user: user)
      end
    end
  end

  factory :article do
    title { Faker::Lorem.sentence }
    body { Faker::Lorem.paragraph }
    status { 'draft' }
    user

    trait :published do
      status { 'published' }
      published_at { Time.current }
    end
  end
end
```

**Using Factories**:
```ruby
# Create (persisted to database)
user = create(:user)
admin = create(:user, :admin)
user_with_articles = create(:user, :with_articles, articles_count: 5)

# Build (not persisted)
user = build(:user)

# Build stubbed (not persisted, attributes stubbed)
user = build_stubbed(:user)

# Attributes hash
attrs = attributes_for(:user)
```

### Fixtures vs Factories
**Prefer FactoryBot over fixtures** for:
- More flexible test data
- Easier to understand and modify
- Better handling of associations
- Ability to customize per test

---

## Mocking and Stubbing

### When to Mock
**DO mock**:
- External APIs (Stripe, Twilio, etc.)
- Email services
- Payment processors
- Third-party services
- Time-dependent operations

**DON'T mock**:
- Database operations
- Your own models/services
- ActiveRecord associations
- Simple Ruby objects

### RSpec Mocking Examples

**Stubbing External APIs**:
```ruby
RSpec.describe PaymentService do
  describe '.process_payment' do
    let(:user) { create(:user) }

    before do
      # Stub Stripe API
      allow(Stripe::Charge).to receive(:create).and_return(
        double(id: 'ch_123', status: 'succeeded')
      )
    end

    it 'processes payment via Stripe' do
      result = PaymentService.process_payment(user, amount: 1000)

      expect(result).to be_success
      expect(Stripe::Charge).to have_received(:create)
    end
  end
end
```

**Stubbing Time**:
```ruby
it 'calculates expiry correctly' do
  freeze_time = Time.zone.parse('2024-01-15 10:00:00')

  travel_to(freeze_time) do
    subscription = create(:subscription)
    expect(subscription.expires_at).to eq(freeze_time + 1.month)
  end
end
```

**Stubbing Email Delivery**:
```ruby
it 'sends welcome email' do
  expect {
    UserService.create_user(email: 'test@example.com')
  }.to have_enqueued_job(ActionMailer::MailDeliveryJob)
    .with('UserMailer', 'welcome_email', 'deliver_now', anything)
end
```

---

## Test Organization

### Directory Structure
```
spec/
├── factories/
│   ├── users.rb
│   ├── articles.rb
│   └── comments.rb
├── models/
│   ├── user_spec.rb
│   └── article_spec.rb
├── requests/
│   └── articles_spec.rb
├── services/
│   └── order_service_spec.rb
├── system/
│   └── article_management_spec.rb
├── support/
│   ├── auth_helpers.rb
│   └── capybara.rb
└── rails_helper.rb
```

### Test Helpers
```ruby
# spec/support/auth_helpers.rb
module AuthHelpers
  def sign_in(user)
    # Implementation depends on auth system
    post login_path, params: { email: user.email, password: 'password' }
  end

  def sign_out
    delete logout_path
  end
end

RSpec.configure do |config|
  config.include AuthHelpers, type: :request
  config.include AuthHelpers, type: :system
end
```

---

## Test Performance

### Strategies for Fast Tests

**1. Use build_stubbed when possible**:
```ruby
# Slow - hits database
user = create(:user)

# Fast - no database
user = build_stubbed(:user)
```

**2. Limit factory associations**:
```ruby
# Slow - creates user, category, and tags
article = create(:article, :with_tags)

# Fast - only creates article
article = build_stubbed(:article)
article.user = build_stubbed(:user)
```

**3. Use let_it_be for shared setup**:
```ruby
RSpec.describe Article do
  # Created once for all tests in this describe block
  let_it_be(:user) { create(:user) }

  it 'test 1' do
    article = create(:article, user: user)
    # ...
  end

  it 'test 2' do
    article = create(:article, user: user)
    # ...
  end
end
```

**4. Parallelize test suite**:
```ruby
# spec/rails_helper.rb
RSpec.configure do |config|
  config.before(:suite) do
    DatabaseCleaner.clean_with(:truncation)
  end

  # Or use test-prof for detailed performance analysis
end
```

---

## Coverage and Quality

### SimpleCov Setup
```ruby
# spec/rails_helper.rb (at the top)
require 'simplecov'
SimpleCov.start 'rails' do
  add_filter '/spec/'
  add_filter '/config/'
  add_group 'Services', 'app/services'
  add_group 'Policies', 'app/policies'
end
```

### Coverage Goals
- **Aim for 80%+ coverage** on critical paths
- **100% coverage is not the goal** - focus on meaningful tests
- Cover edge cases and error conditions
- Prioritize testing business logic

---

## Common Testing Patterns

### Testing Validations
```ruby
it { should validate_presence_of(:email) }
it { should validate_uniqueness_of(:email).case_insensitive }
it { should validate_length_of(:password).is_at_least(8) }
it { should validate_inclusion_of(:role).in_array(%w[admin member guest]) }
```

### Testing Associations
```ruby
it { should belong_to(:user) }
it { should have_many(:comments).dependent(:destroy) }
it { should have_one(:profile) }
it { should have_and_belong_to_many(:tags) }
```

### Testing Callbacks
```ruby
describe 'callbacks' do
  it 'downcases email before save' do
    user = create(:user, email: 'TEST@EXAMPLE.COM')
    expect(user.email).to eq('test@example.com')
  end

  it 'generates slug before create' do
    article = create(:article, title: 'My Great Article')
    expect(article.slug).to eq('my-great-article')
  end
end
```

### Testing Scopes
```ruby
describe 'scopes' do
  describe '.active' do
    it 'returns only active records' do
      active = create_list(:user, 2, status: 'active')
      create(:user, status: 'inactive')

      expect(User.active).to match_array(active)
    end
  end
end
```

---

## Debugging Tests

### Useful Debugging Tools
```ruby
# Print to console
puts user.inspect

# Use pry for debugging
require 'pry'; binding.pry

# Save and open page (system tests)
save_and_open_page

# Screenshot (system tests)
save_and_open_screenshot
```

### Common Issues

**Flaky Tests**:
```ruby
# Bad - dependent on order
let(:users) { User.all }  # Order may vary

# Good - explicit ordering
let(:users) { User.order(:created_at) }

# Bad - time-dependent
expect(article.created_at).to eq(Time.current)

# Good - use time ranges
expect(article.created_at).to be_within(1.second).of(Time.current)
```

---

## Useful Commands

```bash
# Run all tests
rspec

# Run specific file
rspec spec/models/user_spec.rb

# Run specific test
rspec spec/models/user_spec.rb:10

# Run by pattern
rspec spec/models

# Run with coverage
COVERAGE=true rspec

# Run in parallel
parallel_rspec spec/

# JavaScript tests
npm test                    # Run all tests
npm test -- --watch        # Watch mode
npm test -- UserService    # Specific test
```

---

## References

- [RSpec Documentation](https://rspec.info/)
- [Better Specs](https://www.betterspecs.org/)
- [FactoryBot Guide](https://github.com/thoughtbot/factory_bot/blob/master/GETTING_STARTED.md)
- [Capybara Documentation](https://github.com/teamcapybara/capybara)
- [Testing Library](https://testing-library.com/)
