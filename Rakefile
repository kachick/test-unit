# -*- ruby -*-
#
# Copyright (C) 2008-2017  Kouhei Sutou <kou@clear-code.com>

Encoding.default_internal = "UTF-8" if defined?(Encoding.default_internal)

# TODO: Remove me when we drop Ruby 1.9 support.
unless "".respond_to?(:b)
  class String
    def b
      dup.force_encoding("ASCII-8BIT")
    end
  end
end

require "erb"
require "yaml"
require "rubygems"
require "rake/clean"
require "yard"
require 'kramdown'
require 'kramdown-parser-gfm'
require "bundler/gem_helper"
require "packnga"


# ref:
#  * https://github.com/graphiti-api/graphiti-rails/blob/4a8a065f9c67cf0c3bcedfb7c8ac3da033560e57/.yardext.rb
#  * https://github.com/graphiti-api/graphiti-rails/blob/master/.yardopts
# Make new Kramdown class with Github Formatted Markdown on
class KramdownGFM < Kramdown::Document
  def initialize(text, opts={})
    super(text, opts.merge(input: 'GFM', hard_wrap: false))
  end
end

# Register new KramdownGFM
YARD::Templates::Helpers::MarkupHelper::MARKUP_PROVIDERS[:markdown] <<
  { lib: :"kramdown-parser-gfm", :const => "KramdownGFM" }

task :default => :test

base_dir = File.dirname(__FILE__)

helper = Bundler::GemHelper.new(base_dir)
def helper.version_tag
  version
end

helper.install
spec = helper.gemspec

document_task = Packnga::DocumentTask.new(spec) do |task|
  task.original_language = "en"
  task.translate_languages = ["ja"]
end

Packnga::ReleaseTask.new(spec) do |task|
  test_unit_github_io_dir_candidates = [
    "../../www/test-unit.github.io",
  ]
  test_unit_github_io_dir = test_unit_github_io_dir_candidates.find do |dir|
    File.directory?(dir)
  end
  task.index_html_dir = test_unit_github_io_dir
end

def rake(*arguments)
  ruby($0, *arguments)
end

task :test do
  ruby("test/run-test.rb")
end
