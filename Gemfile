# frozen_string_literal: true

source 'https://rubygems.org'
ruby '~> 3.1.0'

git_source(:github) do |repo|
  repo = "#{repo}/#{repo}" unless repo.include?('/')
  "https://github.com/#{repo}.git"
end

## GitHub pages still using old version
## https://pages.github.com/versions.json
gem 'jekyll', '< 4.0.0'

group :jekyll_plugins do
  gem 'github-pages'
end

group *%i[development test] do
  gem 'webrick'
  gem 'pry-byebug'
end

## `bundle exec github-pages health-check` says
## 'To use retry middleware with Faraday v2.0+, install...'
gem 'faraday-retry'
