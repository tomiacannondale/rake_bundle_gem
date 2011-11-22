require 'rake'
require 'tempfile'

def gem_name_split(full_gem_name)
  full_gem_name.split('-').join('/')
end

def gem_lib_file(full_gem_name)
  "lib/#{gem_name_split(full_gem_name)}.rb"
end

def gem_lib_dir(full_gem_name)
  "lib/#{gem_name_split(full_gem_name)}"
end

def gem_version_file(full_gem_name)
  "lib#{gem_name_split(full_gem_name)}/version.rb"
end

def ch_require_path(line, full_gem_name)
  line =~ /^require.*#{full_gem_name}/ && line.gsub!('-', '/')
end

def ch_shebang(line)
  line =~ /^# -\*- encoding:/ && line.gsub!('encoding', 'coding')
end

def ch_files(file, full_gem_name)
  tmp = Tempfile.new("working")
  File.foreach(file) do |line|
    tmp << (ch_shebang(line) || ch_require_path(line, full_gem_name) || line)
  end
  tmp.close
  FileUtils.mv(tmp.path, file)
end

desc "Create gem using bundler with a nested module."
task :bundle_gem do
  full_gem_name = ENV['gem']
  sh "bundle gem #{full_gem_name}"
  FileUtils.cd(full_gem_name, :verbose => true)
  FileUtils.mkdir_p("#{File.dirname(gem_lib_dir(full_gem_name))}", :verbose => true)
  sh "git mv lib/#{full_gem_name} #{gem_lib_dir(full_gem_name)}"
  sh "git mv lib/#{full_gem_name}.rb #{gem_lib_file(full_gem_name)}"

  # FileUtils.chmod(0644, ['.gitignore', "#{full_gem_name}.gemspec", "Gemfile", "Rakefile", gem_lib_file(full_gem_name), gem_version_file(full_gem_name)], :verbose => true)

  ch_files("#{full_gem_name}.gemspec", full_gem_name)
  ch_files(gem_lib_file(full_gem_name), full_gem_name)
end
