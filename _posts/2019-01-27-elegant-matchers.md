---
layout: post
title: Elegant matcher for multiple method call expectations
date:   2019-01-27
---

![Boilerplate and his team](/assets/images/boilerplate.jpg)
Recently, I was digging through some test code and found an annoying recurring pattern that is somehow clumsy and unsatisfying. I talk about such a case:
```ruby
  expect(user.uid).to eq('hpqeqa12asdq1')
  expect(user.country).to eq('Germany')
  expect(user.street_number).to eq('9')
  expect(user.street_address).to eq('Somestrasse')
  expect(user.balance(from: 1.week.ago)).to eq(42000)
```
When you look at that wall of code you see some recurring pattern that is blazing before your eyes blocking you from seeing the essence. It is `expect(user.***).to eq(...` every time. This block does not bring much value into the expression and simply is boilerplate. Although, one might say that such test cases do not comply to the meaning of well-written specs because they do test multiple things at once, but this is not a case of bad code. It checks values that were most likely atomically inserted in one step, so having them tested separately is a waste of time and more boilerplate code that does not make understanding presumed assertions easier to grasp. I was thinking over the way to make such an expression more data-driven and less cluttered with noise. This is what I came up with as a result:
```ruby
### Implementation ###

def expect_responses(object, **fields)
  fields.reduce(true) do |acc, (field, expectation)|
    acc && if expectation.is_a?(WithArguments)
              object.send(
                field,
                *expectation.arguments
              ) == expectation.expected_result
           else
             object.send(field) == expectation
           end
  end
end

WithArguments = Struct.new(:arguments, :expected_result)

def with_arguments(*args, to_have_result:)
  WithArguments.new(args, to_have_result)
end

### Example ###

expect_responses(
  user,
  uid: "hpqeqa12asdq1",
  country: "Germany",
  street_number: '9',
  street_address: 'Somestrasse',
  balance: with_arguments({from: 1.week_ago}, to_have_result: 42000)
) # => true
```

This one seems cleaner, and also I've added a way of passing arguments into the method. To make is easier to grasp for this post, this code returns a boolean value and uses simple comparison, in the real RSpec code it would run on expectations and use matchers to properly compare composite data structures. I tried to mimic common style of RSpec DSL, but I am not really sure on naming of this composite assertion `expect_responses` seems to be alright, but if you may suggest something better on this topic or any part of this post - leave a comment below. Would you find such an addition to RSpec worthy? Would you use it? Why? Why not?
