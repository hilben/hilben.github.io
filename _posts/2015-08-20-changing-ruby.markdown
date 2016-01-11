---
layout: post
title:  "Adding a function to Ruby"
date:   2015-08-20 12:37:56 +0100
categories: ruby c
---

Out of curiosity I wanted to add a new functionality to the Ruby language to get a deeper understanding of the language. I never compiled Ruby myself so I gave it a try.

## Compiling Ruby on Ubuntu

I cloned the [Ruby Github repository](https://github.com/ruby/ruby). Compiling it required me to install autoconf:

{% highlight ruby %}
sudo apt-get update sudo apt-get install autoconf 
{% endhighlight %}

After that I ran autconf:

{% highlight ruby %}
autoconf 
{% endhighlight %}

This step generated the configure file, which I ran next.

{% highlight ruby %}
./configure 
{% endhighlight %}

This step created the make file which I then used by running make.

{% highlight ruby %}
make 
{% endhighlight %}

This step took quiet some time. Finally I verified my compilation using

{% highlight ruby %}
make check
{% endhighlight %}

Luckily everything worked fine according to the check.

To install Ruby I executed make one more time as a superuser:

{% highlight ruby %}
sudo make install
{% endhighlight %}

Ruby was installed and I was able to run it

{% highlight ruby %}
benni@benni-ThinkPad-X230:/usr/local/bin$ ./ruby –version ruby 2.3.0dev (2015-05-28 trunk 50658)
{% endhighlight %}

Adding some stuff to Ruby I tested my self-compiled Ruby by running it on a small code snippet.

{% highlight ruby %}
str = String.new "Hello World!" puts str puts str.reverse puts str.upcase
{% endhighlight %}

## Adding a new function

To ceate a new function **reverse_upcase**. I opened the string.c file and added a new function to the String class by appending it using the rb_define_method.


{% highlight c %}
/** call-seq: * str.reverse_upcase new_str * * Reverses a string and upcases it */
static VALUE rb_str_reverse_upcase(VALUE str) {
  str = rb_str_reverse(str);
  str = rb_str_upcase(str);
  return str; 
}
{% endhighlight %}

The string.c file also defines a Init_String method which adds all the methods to the String class.


{% highlight c %}
void Init_String(void) {
  rb_define_method(rb_cString, "encoding", rb_obj_encoding, 0);
  rb_define_method(rb_cString, "force_encoding", rb_str_force_encoding, 1);
  rb_define_method(rb_cString, "b", rb_str_b, 0);
  rb_define_method(rb_cString, "valid_encoding?", rb_str_valid_encoding_p, 0);
  rb_define_method(rb_cString, "ascii_only?", rb_str_is_ascii_only_p, 0);
  
  /* added code begin */ 
  rb_define_method(rb_cString, "reverse_upcase", rb_str_reverse_upcase, 0);
  /* added code end */
  }
{% endhighlight %}

After recompiling:

{% highlight ruby %}
make
{% endhighlight %}

And reinstalling:

{% highlight ruby %}
sudo make install
{% endhighlight %}

I was able to run the following code:

{% highlight ruby %}
str = String.new "Hello World!"
puts str
puts str.reverse
puts str.upcase
puts str.reverse_upcase

#=> Hello World!
#=> !dlroW olleH
#=> HELLO WORLD!
#=> !DLROW OLLEH
{% endhighlight %}

[Sourcecode of the fork](https://github.com/hilben/ruby_test)
