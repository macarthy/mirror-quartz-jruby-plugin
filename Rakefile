require 'rake'
require 'java'

task :default => :build_jar

# compile task from http://blog.foemmel.com/jrake/compiling

CLASSES_DIR = 'tmp/classes'

task :build_jar => [ :compile ] do
  jar CLASSES_DIR, 'lib/quartz-rails.jar'
end

task :compile do
  src_dir = 'src/java'
  dest_dir = CLASSES_DIR
  
  Dir::mkdir(dest_dir) unless File::exist?(dest_dir)

  javac(src_dir, dest_dir)
end

def jar(src_dir, target)
  `jar cf #{target} -C #{src_dir} .`
end

def build_class_path
  %W( 
    dependencies/goldspike-1.4-SNAPSHOT.jar
    dependencies/jruby-complete-1.0.1.jar
    dependencies/servlet-api.jar
    dependencies/quartz.jar
    dependencies/commons-pool-1.3.jar
  ).map{|f| File.expand_path f}.join(':')
end

def javac(src_dir, dest_dir)
  #java_files = get_out_of_date_files(src_dir, dest_dir)
  java_files = FileList["#{src_dir}/**/*.java"]
  
  unless java_files.empty?
    print "compiling #{java_files.size} java file(s)..."

    args = [ '-cp', build_class_path, '-d', dest_dir, *java_files ]

    buf = java.io.StringWriter.new
    if com.sun.tools.javac.Main.compile(to_java_array(java.lang.String, args), 
                                        java.io.PrintWriter.new(buf)) != 0
      print "FAILED\n\n"
      print buf.to_s
      print "\n"
      fail 'Compile failed'
    end
    print "done\n"
  end
end

def get_out_of_date_files(src_dir, dest_dir)
  java_files = []
  FileList["#{src_dir}/**/*.java"].each do |java_file|
    class_file = dest_dir + java_file[src_dir.length, java_file.length - src_dir.length - '.java'.length] + '.class'
    
    # todo: figure out why File.ctime doesn't work
    unless File.exist?(class_file) && java.io.File.new(class_file).lastModified > java.io.File.new(java_file).lastModified
      java_files << java_file
    end
  end
  return java_files
end

def to_java_array(element_type, ruby_array)
  java_array = java.lang.reflect.Array.newInstance(element_type, ruby_array.size)
  ruby_array.each_index { |i| java_array[i] = ruby_array[i] }
  return java_array
end

