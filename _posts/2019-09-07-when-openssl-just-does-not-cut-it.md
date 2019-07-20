---
layout: post
title: Encryption cooked wrong or why not all top search result gems are equally helpful
date: 2019-09-07
description: Gems are awesome and they speed you up tremendously, but sometimes they fall short and hit you in the weakest spot. How not to trick yourself into insecurity?
---
# Disclaimer
 I don't blame aes gem creator for doing anything wrong consciously. It was written 8 years ago, it never gained enough traction to be actually reviewed by someone who knew how such a gem should look like, it is just accidentally happened that his gem is on the top of 'aes gem' search at Google. Although, this gem has a few pain points and should not be used in production in the modern days at all. That is why.

# TL; DR

Don't use aes gem as your first choice in the google search, it does not properly work with ruby >= 2.4 and does not validate your key. Also, remember that Ruby 2.4 forward openssl gem does not cut the key and iv values, so any key/iv longer than 16/32/64/whatever_size bytes will fail with `ArgumentError(#{iv or key} must be #{n} bytes)`. Cut it/generate with the proper size before passing it to the openssl or a library using it.

# Long story

We are the rubyists. We all know and love gems. Gems are the foundation of our community and the ease of their incorporation into our shiny new projects is one of the main reasons of sharp growth in Ruby popularity and the speed of prototyping that has made our language the king of startups. Gems are the way to share our common knowledge and experience with everyone and make others happier than before. Alas, not all gems are equally mature and helpful. Some of them may be treacherous.

# Encryption is a funny word for obfuscation

Some time ago I came across an interesting dependency in one of my projects. Gem [aes](https://github.com/chicks/aes) was used for symmetric encryption and it seemed fine until I realized(with the help of [some Medium articles](https://blog.elpassion.com/simple-and-terrifying-encryption-story-c1f1d6707c07)) that from a point it was a bit bonkers for a developer that relies on the wisdom of gem creators. I kid you not, the gem allowed you to do this:

```ruby
encrypted = AES.encrypt("Sic transit gloria mundi.", "password", iv: SecureRandom.hex(22))
#  => "1c3c084a8f2384689aa2c440b8d24a3129104fb2dfef$HQVAxvfq1405HV0poYwC3zdEebWC05qhUlMkPbG+kLw="
decrypted = AES.decrypt(encrypted, "password")
# => "Sic transit gloria mundi."
# Seems fine.
2.3.3 :009 > decrypted = AES.decrypt(encrypted, "gassword")
# => "Sic transit gloria mundi."
# Not fine.
2.3.3 :010 > decrypted = AES.decrypt(encrypted, "gassworp")
# => "Sic transit gloria mundi."
# WTF, does it even encrypt?
```

The devil is in this detail of the gem:
```ruby
      # Create a new cipher using the cipher type specified
      def _setup(action)
        @cipher ||= OpenSSL::Cipher::Cipher.new(@options[:cipher])
        # Toggles encryption mode
        @cipher.send(action)
        @cipher.padding = @options[:padding]
        @cipher.key = @key.unpack('a2'*32).map{|x| x.hex}.pack('c'*32)
      end
```

Last line of this method [unpacks](https://apidock.com/ruby/String/unpack) any string as a hex string even though it does not assertion as for if it is actually one. What does it produce? Any string without a valid two-letter hex sequence will end up just the same, so our locked encrypted data will end up with a pretty vast set of possible keys that match and decipher the string.

I know, I know that you are not supposed to use non-hex value as a key for AES, but the library gotta tell me that I am wrong. It certainly should not do this without a blink of an eye, just letting me fail my cryptography like I was using rot13 instead of US government approved AES. This is the exact opposite of what we all strive to with cryptography - transparency and fail fast strategies, so we end up in the insecure mess that is hard to detect. Remember to check your key for being a valid hex, because your encryption may be way worse than you thought.

# Update - fix - repeat

Secondly, this library does not properly work with Ruby 2.4 and newer. By not working properly I mean your old code will fail with no changes after updating your Ruby up from 2.3 and you will need to patch to get it working. Why? Because it relies on OpenSSL and OpenSSL did [this](https://github.com/ruby/ruby/commit/ce635262f53b760284d56bb1027baebaaec175d1) in 2016(they year aes gem master branch was last updated). It stopped truncating too long values for iv/key, so the code that was working just fine before the Ruby updated simply started failing because it did not have the valid key/iv length and no one bothered to fix it until the breaking change.

 You would say that you just pass the truncated key/iv to the library and you're good to go with the gem... You are unless you want to properly decrypt those nasty string you've encrypted with the wrong length iv in the past. 'aes' gem does not give you a normal encrypted string, it prepends the iv right next to it using $ sign as a separator. I would not say it is totally a bad thing, iv should be public after all, but it influences your future choices, bounding you to the gem format of ciphered data that would require a transformation for all encrypted strings you have.

To fix failures due to iv length mismatch on encryption, you would also need to preprocess, split and truncate the iv before feeding it to the library. It works fine until the next breaking change and you've bound yourself with your encrypted data to the legacy gem that has a few vulnerabilities and was not updated for 3 years. Congratulations, you played yourself.

# What should I do?

 Why should you patch around to get the gem working after you updated the Ruby if the gem is basically just 160 lines long? It is better to just read [OpenSSL::Cipher](https://ruby-doc.org/stdlib-2.0.0/libdoc/openssl/rdoc/OpenSSL/Cipher.html) and use AES yourself. The docs are wholesome and warn you of possible caveats, the whole encryption process is explained in code, so instead of using the gem that goes first in the search, stay calm and go more distance with standard library, that will stay here for a much longer period of time with maintainers and updates unlike the gem. This works if you did not yet incorporate the aes gem into your application. If you did, then, well, secure the risks and plan migration, buddy.

# Better to be safe than sorry

Gems are useful and they immensely speed up our pace of development. However, they are just pieces of code that were written by other developers. The fact that the code is open source does not make it better than yours(unless it is a big library with 1000 stars and contributors from the whole world, in that case, hivemind polishes the code pretty well). You should not just blindly trust every piece of code you found on the Rubygems, especially if it is related to security. Read the gem code and docs, check its popularity, common issues, and PRs. This way you can ensure that the gem you bring into your application is really a gem and not a malicious piece of glass. aes gem is not *that* bad, but still, it may cause troubles even though it is just a thin layer on top of OpenSSL that you could write yourself in less than an hour. Choose wisely. Good luck and have fun with Ruby, friend.
