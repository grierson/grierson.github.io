source "https://rubygems.org"

gem "jekyll", "~> 4.3.3"

group :jekyll_plugins do
  gem "jekyll-feed", "~> 0.12"
  gem 'pygments.rb', '~> 2.4.0'
  gem "asciidoctor-diagram", "~> 1.5.4"
  gem "jekyll-asciidoc"
end

# Lock `http_parser.rb` gem to `v0.6.x` on JRuby builds since newer versions of the gem
# do not have a Java counterpart.
gem "http_parser.rb", "~> 0.6.0", :platforms => [:jruby]
