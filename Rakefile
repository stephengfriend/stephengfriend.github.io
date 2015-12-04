task :default => [:clean, :build]
task :serve => [:clean, :server]

desc "clean"
task :clean do
	  rm_rf '_site'
	    FileList['**/*.bak'].clear_exclude.each do |f|
		        rm_f f
			  end
end

desc "build the site"
task :build do
	  sh "bundle exec jekyll build"
end


desc "run jekyll locally"
task :server do
	  sh "bundle exec jekyll serve --watch --port $PORT --host $IP"
end
