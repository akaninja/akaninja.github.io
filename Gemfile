source "https://rubygems.org"

gem "minima", "~> 2.0"

gem "github-pages", "~> 228", group: :jekyll_plugins
gem "webrick", "~> 1.8.1"

group :jekyll_plugins do
  gem "jekyll-feed", "~> 0.6"
end

platforms :mingw, :x64_mingw, :mswin, :jruby do
  gem "tzinfo", ">= 1", "< 3"
  gem "tzinfo-data"
end

gem "wdm", "~> 0.1.0", :install_if => Gem.win_platform?

gem "kramdown-parser-gfm"

gem "http_parser.rb", "~> 0.6.0", :platforms => [:jruby]
