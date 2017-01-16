---
layout: post
title:  "Ruby Shoes! 3"
categories: cache
---

After the New Year, I've been working with Ruby Shoes! GUI for roughly 2 weeks. This GUI sucks. Thank God, the work has come to an end. Here, I'd like to share whatever I've learned with you.

### Tutorials

Useful tutorials are scarce online. I've found only two materials which help, one is ***[Shoes Manual](http://shoesrb.com/manual/Hello.html)***, the other is a book ***[Nobody Knows Shoes](http://cloud.github.com/downloads/shoes/shoes/nks.pdf)***.

### Getting Started

* **Hello, World**

A basic Shoes app looks like below:

```ruby
class MyApp < Shoes
  url '/', :home

  def home
    stack width: '100%',
          margin_top: 50 do

      title 'Hello, World!',
            font: '"Helvetica Neue", Helvetica, Arial, sans-serif 24',
            align: 'center'
    end
  end
end

Shoes.app title: "Hello World App", width: 800, height: 600, :resizable => false
```

There are a few things to mention here:

First, the styling looks like CSS. Some style properties are even named the same as CSS properties. However, don't take it as CSS. Believe me, it is a lot simpler than CSS. For instance, inheritance doesn't work in Shoes. That is, a child element doesn't inherit the styles from its parent element. What's more, in Shoes, styles are inline. We all know that in HTML, we always avoid embedding inline styles. How to define class in a separate file? I will show you a way later on.

Second, url method generates a routing hash table. It associates a URL path with a method. Each method represents a page view. To visit a page, use ***visit '/url'***. url method makes it possible to define each page view in an individual file. Think about it, if an App contains many pages, it would be really horrible to mess all of the views in a single file.

* **Class**

There is no such notion as CSS class in Shoes. The way I've come up with to mimic class is to make use of Ruby hash.

```ruby
# In app.rb
require_relative 'style'

class MyApp < Shoes
  def home
    stack Style.container do
      title 'Hello World!'
    end
  end
end

####################################

# In style.rb
class Style
  def self.container
    {width: '100%',
     margin_top: 50}
  end
end
```

* **Page View**

As I have said before, we are able to define different pages in respective files. The way I found workable is to make use of Open Classes idea in Ruby. In Ruby, classes are never closed. Hence, by opening up the MyApp class in each file, we are able to add multiple pages to it.

```ruby
# In home.rb
class MyApp < Shoes
  url '/', :home

  def home
    ...
    visit '/login'
  end
end

####################################

# In login.rb
class MyApp < Shoes
  url '/login', :login

  def login
    ...
  end
end

####################################

# In app.rb
require_relative 'home'
require_relative 'login'

Shoes.app title: "Hello World App", width: 800, height: 600, :resizable => false
```

Defining different pages in the respective files is the minimal requirement to write maintainable codes.

* **Share data between pages**

How to share data between different pages? Through instance variable?

The answer is **No**. Everytime the application switches to a new page, it creates a new instance of MyApp. Hence, the instance variables are totally reset. Hence, one possible way is to use class variables to share data.

However, if you are familiar with MVC, I think a better approach could be to make either the controller or the model singleton. In that way, the data across pages could be held either by the controller or the model. To create a singleton class:

```ruby
class Model
  @@instance = Model.new

  def self.instance
    return @@instance
  end
end
```

* **Data binding**

Is there any data binding mechanism in Shoes? You have to refresh the view when the model has been changed.

```ruby
def login
  stack margin_left: '50%',
        margin_top: 50,
        left: -250 do
    title 'Login'

    @login_error = para '',
                        stroke: red

    stack margin_top: 15 do
      para 'Username (*):'
      username = edit_line width: 500, text: @username
      username.change do |item|
        @username = item.text
      end
    end

    stack margin_top: 15 do
      para 'Password (*):'
      password = edit_line width: 500, text: @password
      password.change do |item|
        @password = item.text
      end
    end

    flow margin_top: 30,
         margin_right: 0,
         width: 500 do
      button 'Login', right: 0 do
        return @login_error.replace 'Please fill in Username.' if @username.blank?
        return @login_error.replace 'Please fill in Password.' if @password.blank?
      end
    end
  end
end
```

See you have to listen to the change event of the edit input, and populates the new data to the instance variable. Another thing is you have to validate the form data manually.

* **UI**

Layout and styling are hard in Shoes. I've mentioned it before. Shoes looks like HTML and CSS, but it's not. Although, you are able to find some similarities between Shoes and HTML/CSS. For instance, stack is like div, while flow is like span. width, height, font, margin, etc, they are all like their CSS counterparts. However, a lot other are different.

Take a simple example, to draw a div with a background and a radius border, you have to do like this:

```ruby
stack width: 180,
      height: 180 do

  background rgb(0, 141, 255, 200), curve: 20
  border white, strokewidth: 1

end
```

Besides, Shoes doesn't have Table element. Hence, you have draw a table with stack and flow.

```ruby
stack margin_top: 15 do
  # <th>
  flow width: 700 do
    background rgb(82, 146, 247)

    # <td>
    flow width: 350 do
      border white, strokewidth: 1
      subtitle 'Name',
               stroke: white
    end

    # <td>
    flow width: 130 do
      border white, strokewidth: 1
      subtitle 'Age',
               stroke: white
    end

    # <td>
    flow width: 170 do
      border white, strokewidth: 1
      subtitle 'Weight',
               stroke: white
    end

    # <td>
    flow width: 50 do
      border white, strokewidth: 1
      @check_all = check margin_left: 15
      @check_all.click do |item|
        select_all(item.checked?)
      end
    end
  end

  @people.map! do |person|
    # <th>
    flow width: 700 do
      background rgb(82, 146, 247, 50)

      # <td>
      flow width: 350 do
        border white, strokewidth: 1
        para person.name
      end

      # <td>
      flow width: 130 do
        border white, strokewidth: 1
        para person.age
      end

      # <td>
      flow width: 170 do
        border white, strokewidth: 1
        para person.weight
      end

      # <td>
      flow width: 50 do
        border white, strokewidth: 1
        @c = check margin_left: 15
      end
    end

    [@c, person]
  end
end
```

To build up a table, a simple rule is to use flow for a row.

* **Package**

Please refer to wiki [Command line packager](https://github.com/Shoes3/shoes3/wiki/Command-line-packager) on how to package Shoes app to exe by code. A simple example is:

```
require 'shoes/packshoes'

opts = {}
opts['app'] = './my-app.shy'
opts['advopts'] =  false
opts['expandshy'] = true
opts['installer-icon'] = './icon.png'
opts['gempack'] = './gems.tar'              # It doesn't work
opts['arch'] = 'x86_64'                     # win32 armhf x86_64 i686 or osx
opts['dnlhost'] = 'walkabout.mvmanila.com'  # no http://
opts['dnlpath'] = "/public/select/#{opts['arch']}.rb"
opts['shoesdist'] = "./shoes-3.3.1-gtk3-32.exe"
opts['packtmp'] = 'C:/Temp'
opts['relname'] = 'my-app'
PackShoes.repack_exe opts
```

Here, I have to highlight two points. First, **opts['advopts']** must be **false** if you want Shoes to use the shy file you have specified. Otherwise, Shoes will create another shy file, which is most likely not working. Second, gems can't be packed into Shoes exe. You must find other means to install the dependent Gems into Shoes library. It is really absurd.

### Conclusion

To me, Shoes 3 is not a proper GUI for enterprise apps. It's OK to use it building some toy apps. But that's it, toy apps only.