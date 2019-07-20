---
layout: post
title:  Factory Bot sequence testing
date: 2019-07-19
description: Factory Bot seqeuences are a useful way to generate unique data for your test factories, but testing facilities also should be tested, so how can you reach that?
---

Once I was working on a feature that was a validation for UUID of a certain entity that matched the regular expression of `/^ENT-\d\d\d\.\d\d\d\.\d\d\d$/`. It was a Rails project with RSpec and FactoryBot. I wrote the code and tests for the feature and everything seemed to work fine until I pushed it to GitHub and the CI tests run. CI environment, unlike the local one, was set up to run in parallel(this will be important later). CI seemed to break at about 300 tests that ran just fine on the local environment. After a few hours of investigation, it appeared to me that the sequence for UUID in the factory was giving the wrong results for any element past 999 which was certainly unacceptable. I fixed the problem, but to be sure that everything worked properly I needed to validate the results of the sequence were correct.

# Problem

FactoryBot sequence is a sort of enumerator that passes subsequent integer values starting at one to a block and returns the result of the block back. In case if its block is implemented right, it is a pure function, a reflection from the set of integers n to a set of values. Below you can see an example syntax of a sequence.

```ruby
FactoryBot.define do
  sequence(:uuid) do |n|
    n.to_s
  end
end

FactoryBot.generate(:uuid) # => "0"
FactoryBot.generate(:uuid) # => "1"
```

It is great as a __tool__ for testing, but not as __the code being tested__. We could test Factory Bot sequences from the outside checking the output of `generate` and then rewinding the sequence to its original state with `FactoryBot.sequence_by_name(:UUID).rewind`. This would mean that our sequence tests would need to be run first to avoid uniqueness clashes after rewinding the sequence and creating the same values over again. But to be true to ourselves and to be sure that we test properly we need to ensure the validity of our sequences in all cases possible. How can we do this?

# Solution

The answer is pretty easy and straightforward - we should use plain *Ruby* code to solve the issue. FactoryBot sequence is a mechanism to provide unique values to the factories, it is a part of DSL of the testing domain and it operates in it, so the fact that it is not testable from the outside is a hardly evitable part of the user-friendly design. Instead of trying to outsmart the library interface and test the sequence itself, it is better to think of the body of the design of this function. Instead of trying to outsmart the library interface and test the sequence itself, it is better to think of the body of this function. Practically, it is a function that gets an integer value and returns whatever reflection of it deems appropriate. That means that we can just inject our code for such function inside of the sequence and test it instead, not taking the whole FactoryBot DSL for a ride. There are a few possible ways to do so, some exotic, some more commonplace:

## Module functions

```ruby
module Sequences
  module Uuid
    module_function

    def company_uuid(n)
      "CMP-#{n}"
    end
  end
end

# Then you can use it either casting the method to proc after retrieving it with #method method or just calling it inside of the block.

FactoryBot.define do
  sequence(:company_uuid_1, &Sequences::Uuid.method(:company_uuid))
  sequence(:company_uuid_2) do |n|
    Sequences::Uuid.company_uuid(n)
  end
end
```

Comment: `module_function` in the global scope at the tope basically extends module with itself and makes all method both module-level and object-level(if the module is included inside of some class).

## Proc-like objects
```ruby
module Procable
  def to_proc
    Proc.new(&method(:call))
  end
end

module Sequences
  class CompanyUuid
    include Procable

    def call(n)
      "CMP-#{n}"
    end
  end
end

FactoryBot.define do
  sequence(:company_uuid, &Sequences::CompanyUuid.new)
end
```

Comment: `&something` is basically an alternate syntax for `#to_proc` -> convert to block conversion in that case. If the object is already a Proc it just converts it to block.

## Module with proc/lambda constants

```ruby
module Sequences
  COMPANY_UUID_1 = -> n { "CMP-#{n}" } # lambda
  # or
  COMPANY_UUID_2 = Proc.new do |n|
    "CMP-#{n}"
  end
end

FactoryBot.define do
  sequence(:company_uuid, &Sequences::COMPANY_UUID_1)
end
```

Those look nice and decoupled from their definitions in the test factories, so the factories themselves look more declarative as they should be. But this is not the main point. The main point of that conversion is to make this code testable. And now it is(because it is plain Ruby). You can test it like that:

```ruby
  module Procable
    def to_proc
      Proc.new(&method(:call))
    end
  end

  class Sequences
    class CompanyUuid
      include Procable

      def call(n)
        n ** 2
      end
    end
  end

  describe Sequences::CompanyUuid do
    [
      1, 1,
      2, 4,
      3, 9,
      4, 16
    ].each_slice(2) do |input, output|
      it "is expected to eq #{output} on input #{input}" do
        expect(described_class.new.call(input)).to eq(output)
  end
```

Sidenote: better not to put the test file in the same folder as the sequences code if this folder is a `spec/support` folder. It most project setups this folder is loaded on RSpec startup, so your tests for sequences will run twice. If you don't use RSpec or support folder, you should be fine.

## Afterword

Your testing code is a citizen of the same dignity as the normal code in your system because it grows and changes with it and ensures an important quality of your system - validity and proper functioning. It works under the same pressure as other parts of your system and has to be DRYed, decoupled and abstracted away as much as possible same as the actual code of the system. If this act of hygiene is not performed well, you should be sure to expect trouble on one or another stage of development of your system: flaky tests, a lot of commented out tests and overall sliding down in the mud of bugs and swamp of dead unusable tests that were not updated properly, so they grown useless. One of the parts of your testing infrastructure that can be tested outside of going into the infinite loop of 'Quis custodiet ipsos custodes?'(Who'll watch the watchmen?). It is the generative code that gives you mock data for the tests. This code can and should be tested because it is a link that bounds your testing suite to reality and provides it with real-world looking data. The sequence is a kind of such code and they should be tested to avoid problems with flakiness that I experienced and many others. Happy testing!

