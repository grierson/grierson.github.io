source "https://rubygems.org"

gem "jekyll", "~> 4.3.3"

group :jekyll_plugins do
  gem 'pygments.rb', '~> 2.4.1'
  gem "asciidoctor-diagram", "~> 2.2.14"
  gem "jekyll-asciidoc", "~> 3.0.1"
end

# Lock `http_parser.rb` gem to `v0.6.x` on JRuby builds since newer versions of the gem
# do not have a Java counterpart.
gem "http_parser.rb", "~> 0.6.0", :platforms => [:jruby]
