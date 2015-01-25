require "erb"
require "fileutils"
require "pathname"
require "rake"

OUTPUT_DIR = Pathname.new("dist")

class String
  def undent
    gsub(/^.{#{(slice(/^ +/) || '').length}}/, "")
  end
end

class LaTeXTemplate
  attr_accessor :name, :category
  attr_reader :template, :date

  def initialize(name, category, input_path, date=Time.now)
    @name = name
    @category = category
    @template = File.read(input_path)
    @date = date
  end

  def render
    ERB.new(@template).result(binding)
  end

  def input(filename)
    LaTeXTemplate.new(@name, @category, filename, @date).render
  end

  def license; <<-EOS.undent
    %
    % Template:
    %   - name: #{@name}
    %   - category: #{@category}
    %   - date: #{@date}
    %
    % Copyright (C) 2014-#{@date.year} by Xu Cheng <xucheng@me.com>
    %
    % This work may be distributed and/or modified under the
    % conditions of the LaTeX Project Public License, either version 1.3
    % of this license or (at your option) any later version.
    % The latest version of this license is in
    %    http://www.latex-project.org/lppl.txt
    % and version 1.3 or later is part of all distributions of LaTeX
    % version 2005/12/01 or later.
    %
    % This work has the LPPL maintenance status `maintained'.
    %
    % The Current Maintainer of this work is Xu Cheng.
    %
    EOS
  end

  def save(ext=".cls")
    file = OUTPUT_DIR/(@name+ext)
    puts "generate template to #{file}."
    File.open(file, "w") { |f| f.write(render) }
  end
end

task :default => :all

desc "Build all the templates"
task :all => [:article, :beamer]

desc "Build article template"
task :article do
  LaTeXTemplate.new("myarticle", :article, "./article/article.tex").save
end

desc "Build beamer template"
task :beamer do
  LaTeXTemplate.new("mybeamer", :beamer, "./beamer/beamer.tex").save
end

desc "Clean the buildfile"
task :clean do
  Dir[OUTPUT_DIR/"*"].each { |f| rm f }
end