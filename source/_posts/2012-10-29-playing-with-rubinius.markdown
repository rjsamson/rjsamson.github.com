---
layout: post
title: "Playing with Rubinius"
date: 2012-10-29 00:50
comments: true
categories: [Ruby, Rails, Rubinius]
---
I recently started hacking around with Rubinius. Way cool stuff. I haven't been able to get Rails up and running under Rubinius yet, but the only area where I'm seeing issues so far is with Haml. I've managed to track down 2 encoding related bugs that are stopping Haml from rendering properly.

### ascii_only?

The first has to do with String#ascii_only? not working properly when a differently encoded string is embedded in another using string interpolation.

```ruby
str = "a utf-8 string (\u20AC) with utf-8 chars"
str2 = "This string is #{str} but looks like ascii-only"

# In Rubinius
str.ascii_only? # => false
str2.encoding # => UTF-8
str2.ascii_only? # => true

# In Ruby 1.9.3
str.ascii_only? # => false
str2.encoding # => UTF-8
str2.ascii_only? # => false
```

### Non-Ascii Substring Ranges

The second issue is in ranges not working properly to select substrings when a string contains non-ascii characters.

```ruby
str = "a utf-8 string (\u20AC) with utf-8 chars"
str2 = "A normal ASCII-ONLY String"

# In Rubinius
str2[1...-1] # => normal ASCII-ONLY String
str[1...-1] # => utf-8 string (€) with utf-8 chars

# In Ruby 1.9.3
str2[1...-1] # => normal ASCII-ONLY String
str[1...-1] # => utf-8 string (€) with utf-8 char
```

I'll open up a couple issues on GitHub and hopefully these can get fixed - I'm looking forward to being able to get a couple Rails projects up and running on Rubinius.