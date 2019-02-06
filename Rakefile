=begin

This file automates the creation of multiple book formats from your AsciiDoc source.

For this build process to work, you must edit book_name below and change it from 'my-book-name'
to match the file name of your main "spine" book file. If your main book file is called 
eat-more-asparagus.asc then book_name would look like this:

book_name = 'eat-more-asparagus'

This file determines what happens when you run the commands:

bundle exec rake book:clean

-- and --

bundle exec rake book:build

The code is based on work in the progit2 project: https://github.com/progit/progit2/blob/master/Rakefile

=end

namespace :book do
  ### change line below!
  book_name = 'booksquisher-users-guide' # must match your book source file name without the .asc extension
  ### change line above!

  desc 'build basic book formats'
  task :build do

    begin

      # Before the version number will work you need to tag it in git like this:
      # git tag -a v0.1 -m "Hydrogen"
      # Until you add a git tag, you will just get a 0 in the book for the version number.
      version_string = `git describe --tags`.chomp
      if version_string.empty?
        version_string = '0'
      end

      date_string = Time.now.strftime("%Y-%m-%d")
      params = "--attribute revnumber='#{version_string}' --attribute revdate='#{date_string}'"

      puts "Converting to HTML..."
      `bundle exec asciidoctor -D generated-output #{params} #{book_name}.asc`
      cp_r "images", "generated-output/images/"
      puts " -- HTML output at #{book_name}.html"

      puts "Converting to EPub..."
      `bundle exec asciidoctor-epub3 -D generated-output #{params} #{book_name}.asc`
      puts " -- Epub output at #{book_name}.epub"

      puts "Converting to Mobi (kf8)..."
      `bundle exec asciidoctor-epub3 -D generated-output #{params} -a ebook-format=kf8 #{book_name}.asc`
      puts " -- Mobi output at #{book_name}.mobi"

      puts "Converting to PDF... (this one takes a while)"
      `bundle exec asciidoctor-pdf -D generated-output #{params} #{book_name}.asc 2>/dev/null`
      puts " -- PDF output at #{book_name}.pdf"

    end
  end

  desc 'remove all generated book files'
  task :clean do
    rm "generated-output/#{book_name}-kf8.epub", :force => true
    rm "generated-output/#{book_name}.epub", :force => true
    rm "generated-output/#{book_name}.html", :force => true
    rm "generated-output/#{book_name}.mobi", :force => true
    rm "generated-output/#{book_name}.mobi8", :force => true
    rm "generated-output/#{book_name}.pdf", :force => true
    rm_rf "generated-output/images/"
  end

  desc 'copy finished book files from generated-output to wwwroot for deployment'
  task :deploy do
    rm_rf "wwwroot/images"
    rm_rf "wwwroot/*"
    cp "generated-output/#{book_name}.epub", "wwwroot/#{book_name}.epub"
    cp "generated-output/#{book_name}.html", "wwwroot/#{book_name}.html"
    cp "generated-output/#{book_name}.mobi", "wwwroot/#{book_name}.azw3"
    puts "Note: .mobi file was copied with .azw3 extension to wwwroot"
    cp "generated-output/#{book_name}.pdf",  "wwwroot/#{book_name}.pdf"
    cp_r "generated-output/images/", "wwwroot/images/"
  end

end

task :default => "book:build"