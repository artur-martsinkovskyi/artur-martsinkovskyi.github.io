---
layout: post
title: Rubyish flyweight
date: 2019-05-04
description: Ruby is known to be a elegant, so it is no wonder that some patterns look even more natural than in other langugages. Meet flyweight with a single class.
---

Ruby is a nice and elegant language, which is magically appealing in its expressiveness. Thanks to its agility, it allows natural and intuitive solutions where other languages require boilerplate and bloat to reach the same goal. Here is an example of it.

Recently I was hacking through a game design patterns handbook and encountered an interesting pattern used all over the place in game development. This pattern is [*Flyweight*](http://gameprogrammingpatterns.com/flyweight.html).

![Flyweight](/assets/images/flyweight.png)

As Wikipedia says: "In computer programming, flyweight is a software design pattern. A flyweight is an object that minimizes memory usage by sharing as much data as possible with other similar objects; it is a way to use objects in large numbers when a simple repeated representation would use an unacceptable amount of memory. Often some parts of the object state can be shared, and it is common practice to hold them in external data structures and pass them to the objects temporarily when they are used."

Commonly in most languages, this pattern is [implemented](https://sourcemaking.com/design_patterns/flyweight/python/1) using a factory, an abstract class, and inheritance of that class. In Ruby, it may be implemented less noisy. Due to the fact that Object.new is not a special language construct, but just a method we are able to have our own class constructor serve as a factory, just overriding it. Below you can see how it works.



```ruby
module Flyweightable
  def new(*attributes)
    stored_instance = store[attributes]
    return stored_instance if stored_instance

    instance = allocate
    instance.send(:initialize, *attributes)
    store[attributes] = instance
  end

  private

  def store
    @store ||= {}
  end
end

class Point
  attr_reader :x, :y
  extend Flyweightable

  def initialize(x, y)
    @x = x
    @y = y
  end

  def euclidian_distance(point)
    Math.sqrt(((x - point.x) ** 2) + ((y - point.y) ** 2))
  end

  def manhattan_distance(point)
    (x.round - point.x.round).abs + (y.round - point.y.round).abs
  end
end
```

Flyweightable module overrides a new method and creates a memoized storage inside of the class that will hold a registry of all flyweights instantiated. If a flyweight with parameters passed in the constructor exists it just returns it, else it creates a flyweight and writes it into the registry.

Such Flyweight implementation has all the benefits of the original pattern, leaving instantiation untouched, making the external interface as fluent as it was before making class a Flyweight. It is important to remember that this pattern should be used for classes that you deem values like points, textures or other enumerable objects that can be uniquely identified, not references at objects with the changeable state.


Caveats:
- Classes that mix in that module must be read-only as a requirement for Flyweight (else it becomes clumsy and out of sync when objects get their state updated elsewhere)
- The registry is implemented using hash will most likely be live through the whole runtime of your program, so in case of a gazillion of unique objects Flyweight might not be suitable

The same technique(constructor override) may be used in a variety of ways, like dynamic building dispatch or multiple dedicated constructors with different names to break one constructor per class restriction.

Happy hacking!
