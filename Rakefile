require "rubygems"
require "spec/rake/spectask"
require "rake/rdoctask"

task :default => %w[spec:unit spec:integration]

task :dev_console do
  sh "irb -I lib -rubygems -r braintree -r env/development"
end

task :qa_console do
  sh "irb -I lib -rubygems -r braintree -r env/qa"
end

desc "Run units"
Spec::Rake::SpecTask.new("spec:unit") do |t|
  t.spec_files = FileList["spec/unit/**/*_spec.rb"]
end

desc "Run integration"
Spec::Rake::SpecTask.new("spec:integration") do |t|
  t.spec_files = FileList["spec/integration/**/*_spec.rb"]
end

def configure_rdoc_task(t)
  t.main = "README.rdoc"
  t.rdoc_files.include("README.rdoc", "LICENSE", "lib/**/*.rb")
  t.title = "Braintree Ruby Documentation"
end

Rake::RDocTask.new do |t|
  configure_rdoc_task(t)
  t.rdoc_dir = "rdoc"
end

Rake::RDocTask.new(:bt_rdoc) do |t|
  configure_rdoc_task(t)
  t.rdoc_dir = "bt_rdoc"
  t.template = "bt_rdoc_template/braintree"
end

task :bt_rdoc_postprocessing do
  FileUtils.cp "bt_rdoc_template/braintree.gif", "bt_rdoc"
  FileUtils.cp "bt_rdoc_template/ruby.png", "bt_rdoc"
  Dir.glob("bt_rdoc/**/*.html") do |html_file|
    original = File.read(html_file)
    next unless original =~ /STYLE_URL/
    base_url = original[/STYLE_URL = (.*)\/.*/, 1]
    updated = original.gsub("BASE_URL", base_url)
    File.open(html_file, "w") { |f| f.write updated }
  end
end
task(:bt_rdoc) { Rake::Task["bt_rdoc_postprocessing"].invoke }

require File.dirname(__FILE__) + "/lib/braintree/version.rb"
gem_spec = Gem::Specification.new do |s|
  s.name = "braintree"
  s.summary = "Braintree Gateway Ruby Client Library"
  s.description = "Ruby library for integrating with the Braintree Gateway"
  s.version = Braintree::Version::String
  s.author = "Braintree Payment Solutions"
  s.email = "devs@getbraintree.com"
  s.homepage = "http://www.braintreepaymentsolutions.com/gateway"
  s.rubyforge_project = "braintree"
  s.has_rdoc = false
  s.files = FileList["README.rdoc", "LICENSE", "{lib,spec}/**/*.rb", "lib/**/*.crt"]
end

task :gem do
  Gem::Builder.new(gem_spec).build
end
