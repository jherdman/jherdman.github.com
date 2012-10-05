---
layout: post
title: "Open Ended Ruby Date Ranges"
date: 2012-10-05 11:44
comments: true
categories: ruby range dates
---

Have you ever needed an open-ended `Date` range in Ruby? Well, check this out:

```ruby
require 'date'

some_date = Date.today
tomorrow = some_date + 1
yesterday = some_date - 1

open_ended_range = (some_date..1/0.0)
# => ((2456206j,0s,0n),+0s,2299161j)>..Infinity

open_ended_range.include?(tomorrow)
# => true

open_ended_range.include?(yesterday)
# => false
```

Pretty rad stuff.
