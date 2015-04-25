require "rubygems"
require "tmpdir"
require 'webrick'
require "bundler/setup"
require "jekyll"


def say_what? message
  print message
  STDIN.gets.chomp
end


def sluggize str
  str.downcase.gsub(/[^a-z0-9]+/, '-').gsub(/^-|-$/, '');
end


desc "Generate blog files"
task :generate do
  Jekyll::Site.new(Jekyll.configuration({
    "source"      => ".",
    "destination" => "_site"
  })).process
end


desc "Generate and publish blog to gh-pages"
task :publish => [:generate] do
  Dir.mktmpdir do |tmp|
    cp_r "_site/.", tmp
    Dir.chdir tmp
    system "git init"
    system "git add ."
    message = "Site updated at #{Time.now.utc}"
    system "git commit -m #{message.inspect}"
    system "git remote add origin git@github.com:shurtado/Quantics"
    system "git push origin master:gh-pages --force"
  end
end

desc "Generate site and host with static file server"
task :serve => [:generate] do
  root = File.expand_path '_site'
  server = WEBrick::HTTPServer.new :Port => 8080, :DocumentRoot => root
  jekyllPid = Process.spawn "jekyll build --watch"
  trap 'INT' do
    Process.kill(9, jekyllPid) rescue Errno::ESRCH
    server.shutdown
  end
  server.start
end
