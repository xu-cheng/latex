require "erb"
require "fileutils"
require "pathname"
require "rake"
require "time"

OUTPUT_DIR = Pathname.new("dist")

class LaTeXTemplate
  attr_reader :category, :date, :name, :template

  def initialize(name, category, input_path, date=commit_date)
    @name = name
    @category = category
    @template = File.read(input_path)
    @date = date
  end

  def render
    ERB.new(@template, trim_mode: ">").result(binding)
  end

  def input(filename)
    LaTeXTemplate.new(@name, @category, filename, @date).render
  end

  def commit_date
    Time.parse `git show -s --format=%cD`.strip
  end

  def commit
    `git rev-parse --verify HEAD`.strip
  end

  def license; <<~EOS
    %
    % Template:
    %   - name: #{@name}
    %   - category: #{@category}
    %   - date: #{@date}
    %   - commit: #{commit}
    %
    % Copyright (C) 2014-#{@date.year} by Cheng XU <copyright@xuc.me>
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
    % The Current Maintainer of this work is Cheng XU.
    %
    EOS
  end

  def chapter_defined?
    [:book, :report].include? @category
  end

  def top_level
    case @category
    when :book, :report then "chapter"
    when :article, :beamer, :poster then "section"
    else raise
    end
  end

  def save(ext=".cls")
    FileUtils.mkdir_p OUTPUT_DIR
    file = OUTPUT_DIR/"#{name}#{ext}"
    puts "generate template to #{file}."
    File.open(file, "w") { |f| f.write(render) }
    File.utime(date, date, file)
  end
end

task :default => :all

desc "Build all the templates"
task :all => [:article, :report, :book, :beamer, :poster]

desc "Build article template"
task :article do
  LaTeXTemplate.new("myarticle", :article, "./article/article.tex").save
  LaTeXTemplate.new("myreviewresponse", :article, "./article/reviewresponse.tex").save
end

desc "Build report template"
task :report do
  LaTeXTemplate.new("myreport", :report, "./report/report.tex").save
end

desc "Build book template"
task :book do
  LaTeXTemplate.new("mybook", :book, "./book/book.tex").save
end

desc "Build beamer template"
task :beamer do
  LaTeXTemplate.new("mybeamer", :beamer, "./beamer/beamer.tex").save
  LaTeXTemplate.new("mybeamer-group", :beamer, "./beamer/group.tex").save
end

desc "Build poster template"
task :poster do
  LaTeXTemplate.new("myposter", :poster, "./poster/poster.tex").save
end

desc "Clean up"
task :clean do
  Dir[OUTPUT_DIR/"*"].each { |f| rm f }
end
