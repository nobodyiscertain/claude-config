# Ruby on Rails Guidelines

Comprehensive conventions and style guidelines for Ruby on Rails development.

## Core Rails Principles

- **Convention over Configuration**: Follow Rails' established patterns
- **DRY (Don't Repeat Yourself)**: Extract common logic into reusable methods/modules
- **RESTful Design**: Use standard REST conventions for routes and controllers
- **Fat Models, Skinny Controllers**: Business logic belongs in models, not controllers

---

## Code Structure and Conventions

### Controllers

**Follow the 7 Standard RESTful Actions:**
```ruby
class ArticlesController < ApplicationController
  # GET /articles
  def index
  end

  # GET /articles/:id
  def show
  end

  # GET /articles/new
  def new
  end

  # POST /articles
  def create
  end

  # GET /articles/:id/edit
  def edit
  end

  # PATCH/PUT /articles/:id
  def update
  end

  # DELETE /articles/:id
  def destroy
  end
end
```

**Keep Controllers Lightweight:**
```ruby
# Bad - business logic in controller
class OrdersController < ApplicationController
  def create
    @order = Order.new(order_params)
    @order.status = 'pending'
    @order.total = calculate_total(@order.items)

    if @order.save
      # Send confirmation email
      OrderMailer.confirmation(@order).deliver_later
      # Update inventory
      @order.items.each do |item|
        item.product.decrement!(:stock, item.quantity)
      end
      redirect_to @order
    else
      render :new
    end
  end
end

# Good - delegate to model/service
class OrdersController < ApplicationController
  def create
    @order = OrderService.create_order(order_params)

    if @order.persisted?
      redirect_to @order
    else
      render :new
    end
  end
end
```

### Models

**Use ActiveRecord Callbacks Judiciously:**
```ruby
class Order < ApplicationRecord
  # Associations first
  has_many :line_items
  belongs_to :customer

  # Validations second
  validates :customer, presence: true
  validates :total, numericality: { greater_than: 0 }

  # Scopes third
  scope :pending, -> { where(status: 'pending') }
  scope :completed, -> { where(status: 'completed') }

  # Callbacks last (use sparingly)
  after_create :send_confirmation_email

  private

  def send_confirmation_email
    OrderMailer.confirmation(self).deliver_later
  end
end
```

**Extract Complex Logic into Methods:**
```ruby
class User < ApplicationRecord
  def full_name
    "#{first_name} #{last_name}".strip
  end

  def admin?
    role == 'admin'
  end

  def can_edit?(resource)
    admin? || resource.user_id == id
  end
end
```

### Services

**Use Service Objects for Complex Operations:**
```ruby
# app/services/order_service.rb
class OrderService
  def self.create_order(params)
    new.create_order(params)
  end

  def create_order(params)
    order = Order.new(params)

    ActiveRecord::Base.transaction do
      order.calculate_total!
      order.save!
      update_inventory(order)
      send_notifications(order)
    end

    order
  rescue ActiveRecord::RecordInvalid => e
    order.errors.add(:base, e.message)
    order
  end

  private

  def update_inventory(order)
    order.line_items.each do |item|
      item.product.decrement!(:stock, item.quantity)
    end
  end

  def send_notifications(order)
    OrderMailer.confirmation(order).deliver_later
  end
end
```

### Concerns

**Use Concerns for Shared Behavior:**
```ruby
# app/models/concerns/taggable.rb
module Taggable
  extend ActiveSupport::Concern

  included do
    has_many :taggings, as: :taggable
    has_many :tags, through: :taggings
  end

  def tag_names
    tags.pluck(:name)
  end

  def tag_with(tag_names)
    tag_names.each do |name|
      tags << Tag.find_or_create_by(name: name)
    end
  end
end

# Usage in models
class Article < ApplicationRecord
  include Taggable
end

class Post < ApplicationRecord
  include Taggable
end
```

---

## Routing Conventions

### RESTful Routes
```ruby
# config/routes.rb

# Standard resourceful routes
resources :articles

# Nested resources (limit nesting to 1 level)
resources :articles do
  resources :comments, only: [:create, :destroy]
end

# Custom member/collection routes
resources :articles do
  member do
    post :publish
    post :unpublish
  end

  collection do
    get :archived
  end
end

# Namespace for admin
namespace :admin do
  resources :users
  resources :articles
end
```

### Route Constraints
```ruby
# Use constraints for versioned APIs
namespace :api do
  namespace :v1 do
    resources :users
  end
end

# Subdomain constraints
constraints subdomain: 'api' do
  namespace :api do
    resources :users
  end
end
```

---

## Database and Migrations

### Migration Best Practices

**Always Reversible:**
```ruby
class AddPublishedToArticles < ActiveRecord::Migration[7.0]
  def change
    add_column :articles, :published, :boolean, default: false, null: false
    add_index :articles, :published
  end
end
```

**Use Specific Migration Methods:**
```ruby
# Good - specific and clear
class CreateOrders < ActiveRecord::Migration[7.0]
  def change
    create_table :orders do |t|
      t.references :user, null: false, foreign_key: true
      t.decimal :total, precision: 10, scale: 2, null: false
      t.string :status, default: 'pending', null: false

      t.timestamps
    end

    add_index :orders, :status
    add_index :orders, :created_at
  end
end
```

**Data Migrations (Use Carefully):**
```ruby
# For data changes, use a separate migration
class BackfillUserRoles < ActiveRecord::Migration[7.0]
  def up
    User.where(role: nil).update_all(role: 'member')
  end

  def down
    # Usually can't reverse data migrations safely
    raise ActiveRecord::IrreversibleMigration
  end
end
```

### ActiveRecord Queries

**Use Scopes and Query Methods:**
```ruby
# Bad - string conditions
User.where("created_at > ? AND status = ?", 1.week.ago, 'active')

# Good - hash conditions
User.where(status: 'active').where('created_at > ?', 1.week.ago)

# Better - use scopes
class User < ApplicationRecord
  scope :active, -> { where(status: 'active') }
  scope :recent, -> { where('created_at > ?', 1.week.ago) }
end

User.active.recent
```

**Avoid N+1 Queries:**
```ruby
# Bad - N+1 query
@articles = Article.all
@articles.each do |article|
  puts article.author.name  # Triggers query for each article
end

# Good - eager loading
@articles = Article.includes(:author)
@articles.each do |article|
  puts article.author.name  # No additional queries
end
```

---

## Testing with RSpec

See [testing.md](testing.md) for comprehensive testing guidelines.

**Quick Reference:**
- Always write tests for new features
- Use FactoryBot for test data
- Tests should hit the database (no excessive mocking)
- Mock only external services
- Controller specs: test response codes and redirects
- Feature specs: single happy path scenarios

---

## Security Best Practices

### Strong Parameters
```ruby
class ArticlesController < ApplicationController
  private

  def article_params
    params.require(:article).permit(:title, :body, :published)
  end
end
```

### Prevent Mass Assignment
```ruby
# Bad - vulnerable to mass assignment
@user.update(params[:user])

# Good - use strong parameters
@user.update(user_params)
```

### SQL Injection Prevention
```ruby
# Bad - SQL injection vulnerability
User.where("email = '#{params[:email]}'")

# Good - parameterized query
User.where(email: params[:email])

# Good - with placeholder
User.where("email = ?", params[:email])
```

### XSS Prevention
```ruby
# In views, Rails auto-escapes by default
<%= @article.title %>  # Automatically escaped

# To render HTML (be very careful!)
<%= sanitize @article.body %>  # Sanitizes HTML
<%= raw @article.body %>       # DANGEROUS - no escaping
```

---

## Performance Optimization

### Database Indexing
```ruby
# Add indexes for foreign keys and frequently queried columns
add_index :articles, :user_id
add_index :articles, :published_at
add_index :articles, [:user_id, :published_at]  # Composite index
```

### Caching
```ruby
# Fragment caching in views
<% cache @article do %>
  <%= render @article %>
<% end %>

# Russian doll caching
<% cache ['v1', @article] do %>
  <%= @article.title %>
  <% cache ['v1', @article, @article.comments] do %>
    <%= render @article.comments %>
  <% end %>
<% end %>

# Low-level caching
def expensive_calculation
  Rails.cache.fetch("expensive_calc_#{id}", expires_in: 12.hours) do
    # Complex calculation here
  end
end
```

### Background Jobs
```ruby
# Use ActiveJob for async processing
class SendEmailJob < ApplicationJob
  queue_as :default

  def perform(user_id)
    user = User.find(user_id)
    UserMailer.welcome(user).deliver_now
  end
end

# Enqueue job
SendEmailJob.perform_later(user.id)
```

---

## Code Style (RuboCop)

### Naming Conventions
- Use `snake_case` for methods and variables
- Use `CamelCase` for classes and modules
- Use `SCREAMING_SNAKE_CASE` for constants
- Use `?` suffix for predicate methods
- Use `!` suffix for dangerous/mutating methods

### Method Length
- Keep methods under 10 lines when possible
- Extract complex logic into private methods
- Use descriptive method names

### Code Organization
```ruby
class Article < ApplicationRecord
  # Extend and include first
  extend FriendlyId
  include Taggable

  # Constants
  STATUSES = %w[draft published archived].freeze

  # Associations
  belongs_to :user
  has_many :comments

  # Validations
  validates :title, presence: true
  validates :status, inclusion: { in: STATUSES }

  # Scopes
  scope :published, -> { where(status: 'published') }

  # Callbacks
  before_save :generate_slug

  # Class methods
  def self.recent
    order(created_at: :desc).limit(10)
  end

  # Instance methods (public)
  def publish!
    update!(status: 'published', published_at: Time.current)
  end

  # Instance methods (private)
  private

  def generate_slug
    self.slug = title.parameterize
  end
end
```

---

## Dependency Management

### Gemfile Organization
```ruby
source 'https://rubygems.org'

# Core Rails
gem 'rails', '~> 7.0'

# Database
gem 'pg', '~> 1.1'

# Asset pipeline
gem 'sprockets-rails'

# Authentication & Authorization
gem 'devise'
gem 'pundit'

# Background jobs
gem 'sidekiq'

group :development, :test do
  gem 'rspec-rails'
  gem 'factory_bot_rails'
  gem 'faker'
  gem 'pry-rails'
end

group :development do
  gem 'rubocop-rails', require: false
  gem 'spring'
end

group :test do
  gem 'capybara'
  gem 'selenium-webdriver'
end
```

### Before Adding New Gems
1. Check if functionality exists in existing gems
2. Verify gem is actively maintained
3. Check for security vulnerabilities
4. Consider bundle size impact

---

## Common Patterns to Avoid

### Anti-patterns
```ruby
# Bad - Fat controller
class ArticlesController < ApplicationController
  def create
    # 50 lines of business logic here
  end
end

# Bad - Callback hell
class User < ApplicationRecord
  after_create :send_welcome_email
  after_create :create_profile
  after_create :notify_admin
  after_create :setup_default_preferences
  # ... many more callbacks
end

# Bad - God object
class User < ApplicationRecord
  # Handles authentication, authorization, preferences,
  # notifications, billing, etc. all in one model
end

# Bad - Direct SQL when ActiveRecord works
execute("SELECT * FROM users WHERE email = '#{email}'")
```

### Preferred Patterns
- Service objects for complex operations
- Form objects for complex forms
- Query objects for complex queries
- Decorators/presenters for view logic
- Concerns for shared behavior (use sparingly)

---

## Useful Commands

```bash
# Database
rails db:create          # Create database
rails db:migrate         # Run migrations
rails db:rollback        # Rollback last migration
rails db:seed            # Seed database
rails db:reset           # Drop, create, migrate, seed

# Console
rails console            # Start Rails console
rails c                  # Shortcut for console

# Server
rails server             # Start server
rails s                  # Shortcut for server

# Testing
rspec                    # Run all tests
rspec spec/models        # Run model tests
rspec spec/features      # Run feature tests

# Linting
rubocop                  # Run RuboCop
rubocop -a              # Auto-fix issues

# Routes
rails routes            # Show all routes
rails routes | grep article  # Filter routes
```

---

## References

- [Rails Guides](https://guides.rubyonrails.org/)
- [Ruby Style Guide](https://rubystyle.guide/)
- [RuboCop Rails](https://docs.rubocop.org/rubocop-rails/)
- [Better Specs](https://www.betterspecs.org/)
