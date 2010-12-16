task :default => :server

desc 'Build site w/Jekyll'
task :build do
  jekyll
end

desc 'Build and deploy to dreamhost'
task :deploy => :build do
  sh 'rsync -rtzh --progress --delete _site/ mikereedell@mangano.dreamhost.com:~/mikereedell.com/'
end

desc 'Build and run locally'
task :server do
  jekyll '--server --auto'
end

def jekyll(opts = '')
  sh 'rm -rf _site'
  sh 'jekyll ' + opts
end