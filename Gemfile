# frozen_string_literal: true

source 'https://rubygems.org'

git_source(:github) do |repo|
  repo = "#{repo}/#{repo}" unless repo.include?('/')
  "https://github.com/#{repo}.git"
end

gem 'jekyll'

group *%i[development test] do
  gem 'webrick'
  gem 'pry-byebug'
end
