require 'rubygems'
require 'rake'
require 'rake/clean'
require 'rake/testtask'
require 'rake/packagetask'
require 'rake/contrib/rubyforgepublisher'
require 'rake/contrib/sshpublisher'
require 'fileutils'
require './lib/gisty'
include FileUtils

NAME              = "gisty"
AUTHOR            = "swdyh"
EMAIL             = "http://mailhide.recaptcha.net/d?k=01AhB7crgrlHptVaYRD0oPwA==&c=L_iqOZrGmo6hcGpPTFg1QYnjr-WpAStyQ4Y8ShfgOHs="
SUMMARY           = "yet another command line client for gist."
DESCRIPTION       = SUMMARY + " Gisty uses Github API V3 via OAuth2."
RUBYFORGE_PROJECT = "gisty"
HOMEPATH          = "http://github.com/swdyh/gisty/tree/master"
BIN_FILES         = %w( gisty )

VERS              = Gisty::VERSION
REV = File.read(".svn/entries")[/committed-rev="(d+)"/, 1] rescue nil
CLEAN.include ['**/.*.sw?', '*.gem', '.config', '*.gemspec']
RDOC_OPTS = [
             '--title', "#{NAME} documentation",
             "--charset", "utf-8",
             "--line-numbers",
             "--main", "README.rdoc",
             "--inline-source",
            ]

task :default => [:test]
task :package => [:clean]

Rake::TestTask.new("test") do |t|
  t.libs   << "test"
  t.pattern = "test/**/*_test.rb"
  t.verbose = true
end

spec = Gem::Specification.new do |s|
  s.name              = NAME
  s.version           = VERS
  s.platform          = Gem::Platform::RUBY
  # s.has_rdoc          = true
  s.extra_rdoc_files  = ["README.rdoc"]
  s.rdoc_options     += RDOC_OPTS + ['--exclude', '^(examples|extras)/']
  s.summary           = SUMMARY
  s.description       = DESCRIPTION
  s.author            = AUTHOR
  s.email             = EMAIL
  s.homepage          = HOMEPATH
  s.executables       = BIN_FILES
  s.rubyforge_project = RUBYFORGE_PROJECT
  s.bindir            = "bin"
  s.require_path      = "lib"
  #s.autorequire       = ""
  s.test_files        = Dir["test/*_test.rb"]

  #s.add_dependency('activesupport', '>=1.3.1')
  #s.required_ruby_version = '>= 1.8.2'
  s.add_development_dependency "json"
  s.add_development_dependency "rake"
  s.add_development_dependency "rr"
  s.add_development_dependency "fakeweb"

  s.files = %w(README.rdoc Rakefile) +
    Dir.glob("{bin,doc,test,lib,templates,generator,extras,website,script}/**/*") + 
    Dir.glob("ext/**/*.{h,c,rb}") +
    Dir.glob("examples/**/*.rb") +
    Dir.glob("tools/*.rb") +
    Dir.glob("rails/*.rb")

  s.extensions = FileList["ext/**/extconf.rb"].to_a
end

desc 'Update gem spec'
task :gemspec do
  open("#{NAME}.gemspec", 'w') { |f| f.puts spec.to_ruby }
end

desc 'update gem'
task :update => [:gemspec] do
  sh "gem build #{NAME}.gemspec"
end

desc 'refresh fixtures'
task :reflresh_fixtures do
  g = Gisty.new 'tmp'
  re = /page=\d+/
  urls = g.map_pages do |url, page|
    m = url.match re
    if m
      fn = 'mine_' + m.to_a.first.sub('=', '_') + '_login_foo_token_bar'
      path = File.join 'test', 'fixtures', fn
      puts "write #{path}"
      open(path, 'w') { |f| f.write page.gsub(/(&amp;)?(login|token)=\w+(&amp;)?/, '') }
    end
  end
end
