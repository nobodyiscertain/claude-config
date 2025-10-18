# frozen_string_literal: true

require 'fileutils'
require 'pathname'

# Paths
REPO_ROOT = Pathname.new(__dir__)
CLAUDE_HOME = Pathname.new(Dir.home) / '.claude'
CLAUDE_MD_SOURCE = REPO_ROOT / 'AGENTS.md'
CLAUDE_MD_TARGET = CLAUDE_HOME / 'CLAUDE.md'

# Directories to symlink contents from
DIRS_TO_LINK = %w[agents commands guidelines].freeze

desc 'Set up Claude configuration by symlinking files to ~/.claude/'
task :setup do
  puts "Setting up Claude configuration from #{REPO_ROOT}..."
  puts

  # Create ~/.claude directory if it doesn't exist
  unless CLAUDE_HOME.exist?
    puts "Creating #{CLAUDE_HOME}..."
    FileUtils.mkdir_p(CLAUDE_HOME)
  end

  # Symlink AGENTS.md -> ~/.claude/CLAUDE.md
  setup_main_config

  # Symlink directory contents
  DIRS_TO_LINK.each do |dir|
    setup_directory_contents(dir)
  end

  puts
  puts "✓ Setup complete!"
  puts
  puts "Your Claude configuration is now linked to this repository."
  puts "Any changes to files in #{REPO_ROOT} will be reflected immediately."
end

desc 'Remove symlinks created by setup task'
task :uninstall do
  puts "Removing Claude configuration symlinks..."
  puts

  removed_count = 0

  # Remove main CLAUDE.md symlink
  if CLAUDE_MD_TARGET.symlink? && points_to_repo?(CLAUDE_MD_TARGET)
    CLAUDE_MD_TARGET.delete
    puts "✓ Removed #{CLAUDE_MD_TARGET}"
    removed_count += 1
  elsif CLAUDE_MD_TARGET.symlink?
    puts "⊘ Skipped #{CLAUDE_MD_TARGET} (points to different location)"
  elsif CLAUDE_MD_TARGET.exist?
    puts "⊘ Skipped #{CLAUDE_MD_TARGET} (not a symlink)"
  end

  # Remove symlinks in each directory
  DIRS_TO_LINK.each do |dir|
    target_dir = CLAUDE_HOME / dir
    next unless target_dir.exist?

    target_dir.children.each do |file|
      next unless file.symlink? && points_to_repo?(file)

      file.delete
      puts "✓ Removed #{file}"
      removed_count += 1
    end
  end

  puts
  if removed_count.zero?
    puts "No symlinks found to remove."
  else
    puts "✓ Removed #{removed_count} symlink#{'s' unless removed_count == 1}"
  end
end

# Helper methods

def setup_main_config
  if CLAUDE_MD_TARGET.exist? && !CLAUDE_MD_TARGET.symlink?
    puts "⚠ Skipped #{CLAUDE_MD_TARGET} (file already exists, not a symlink)"
    puts "  To use the repository version, manually backup and remove this file."
  elsif CLAUDE_MD_TARGET.symlink?
    if should_overwrite?(CLAUDE_MD_TARGET)
      CLAUDE_MD_TARGET.delete
      create_symlink(CLAUDE_MD_SOURCE, CLAUDE_MD_TARGET)
    else
      puts "⊘ Skipped #{CLAUDE_MD_TARGET} (already exists)"
    end
  else
    create_symlink(CLAUDE_MD_SOURCE, CLAUDE_MD_TARGET)
  end
end

def setup_directory_contents(dir_name)
  source_dir = REPO_ROOT / dir_name
  target_dir = CLAUDE_HOME / dir_name

  unless source_dir.exist?
    puts "⚠ Warning: Source directory #{source_dir} not found, skipping..."
    return
  end

  # Create target directory if it doesn't exist
  FileUtils.mkdir_p(target_dir) unless target_dir.exist?

  # Symlink each file in the source directory
  source_dir.children.select(&:file?).each do |source_file|
    target_file = target_dir / source_file.basename

    if target_file.exist? && !target_file.symlink?
      puts "⚠ Skipped #{target_file} (file already exists, not a symlink)"
    elsif target_file.symlink?
      if should_overwrite?(target_file)
        target_file.delete
        create_symlink(source_file, target_file)
      else
        puts "⊘ Skipped #{target_file} (already exists)"
      end
    else
      create_symlink(source_file, target_file)
    end
  end
end

def create_symlink(source, target)
  target.make_symlink(source.expand_path)
  puts "✓ Created #{target} -> #{source}"
end

def should_overwrite?(target)
  print "  #{target} already exists. Overwrite? [y/N] "
  response = $stdin.gets&.chomp&.downcase
  response == 'y' || response == 'yes'
end

def points_to_repo?(symlink)
  return false unless symlink.symlink?

  # Resolve the symlink and check if it points to a file in this repo
  begin
    real_path = symlink.realpath
    real_path.to_s.start_with?(REPO_ROOT.to_s)
  rescue StandardError
    # If we can't resolve the symlink (broken link), treat it as not ours
    false
  end
end
