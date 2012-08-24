---
layout: post
title: A Quick Cucumber Tip
---

<abbr title="Don't Repeat Yourself">DRY</abbr> code makes me a happy camper, so when I see patterns in my regular expressions in my Cucumber stories, I get a little agitated. These patterns come up quick frequently if you are using the explicit record retrieval pattern in your stories. Here are some examples of the steps we want to refine:

```ruby
Given /^I am a logged in user with the email address "([^"]+)"$/ do |email_address|
  user = User.find_by_email_address!(email_address)
  # etc
end

When /^I view the (\d+)(?:st|nd|rd|th) item$/ do |pos|
  within "#items .item:nth-child(#{pos})" do
    # etc
  end
end
```

Note the <code>"([^"]+)"</code> and <code>(?:st|nd|rd|th)</code> patterns. Let's extract those patterns so that we never have to type them out again.

The key to this is to recognize that Cucumber automatically loads all Ruby files in the "support" and "step_definitions" directories. Let's create a file called "constants.rb" in "support". Here's mine:

<script src="http://gist.github.com/576424.js"> </script>

We can now rewrite our step definitions above to use these constants:

```ruby
Given /^I am a logged in user with the email address #{PHRASE}$/ do |email_address|
  user = User.find_by_email_address!(email_address)
  # etc
end

When /^I view the (\d+)#{POSITION} item$/ do |pos|
  within "#items .item:nth-child(#{pos})" do
    # etc
  end
end
```

Now we have cleaner steps, with less repetition, and they should take us a little less time to write out in the future. Use, abuse and fork my module above.
