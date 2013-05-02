# MotionSupport

This is a port of the parts of ActiveSupport that make sense for RubyMotion.

To see what's there, generate the documentation with the `rdoc` command from the repository root, or look into the lib folder. Almost everything is tested.

# Usage

Install with

    gem install motion-support

or add to your `Gemfile`

    gem 'motion-support'

# Partial loading

It is also possible to only use parts of this library. To do so, change your `Gemfile` so that it reads:

    gem 'motion-support', :require => false

Then add a require statement as shown below to your `Rakefile`.

## all

    require 'motion-support'

Loads everything.

## inflector

    require 'motion-support/inflector'

Loads the `Inflector` module and extensions to the `String` class. See the "Inflector" app in the `examples/` folder for what the inflector can do.

Example usage include:

    "app_delegate".camelize       # => "AppDelegate"
    "HomeController".constantize  # => HomeController
    "thing".pluralize             # => "things"
    "mice".singularize            # => "mouse"

## core_ext

    require 'motion-support/core_ext'

Loads all the extensions to core classes.

## core_ext/array

    require 'motion-support/core_ext/array'

Loads extensions to class `Array`. Example usage include

    %w( a b c d ).from(2)               # => ["c", "d"]
    ['one', 'two', 'three'].to_sentence # => "one, two, and three"
    args.extract_options!               # extracts options hash from variant args array
    [1, 2, 3, 4].in_groups_of(2)        # => [[1, 2], [3, 4]]

## core_ext/class

    require 'motion-support/core_ext/class'

Loads extensions to class `Class`.

    class Foo
      cattr_accessor :bar
      class_attribute :foo
    end

## core_ext/hash

    require 'motion-support/core_ext/hash'

Loads extensions to class `Hash`, including class `HashWithIndifferentAccess`.

    { 'foo' => 'bar', 'baz' => 'bam' }.symbolize_keys # => { :foo => 'bar', :baz => 'bam' }
    { 'foo' => 'bar', 'baz' => 'bam' }.except('foo')  # => { 'baz' => 'bam' }
    { 'foo' => 'bar', 'baz' => 'bam' }.with_indifferent_access
    # => #<HashWithIndifferentAccess>

## core_ext/integer

    require 'motion-support/core_ext/integer'

Loads extensions to class `Integer`.

    1.ordinalize # => "1st"
    3.ordinalize # => "3rd"

## core_ext/module

    require 'motion-support/core_ext/module'

Loads extensions to class `Module`.

    module Mod
      mattr_accessor :foo, :bar
      attr_internal :baz
      delegate :boo, :to => :foo
      
      remove_method :baz
    end

## core_ext/numeric

    require 'motion-support/core_ext/numeric'

Loads extensions to class `Numeric`.

    10.kilobytes  # => 10240
    2.megabytes   # => 2097152
    3.days

## core_ext/object

    require 'motion-support/core_ext/object'

Loads extensions to class `Object`.

    nil.blank?                                      # => true
    Object.new.blank?                               # => false
    { "hello" => "world", "foo" => "bar" }.to_query # => "hello=world&foo=bar"
    1.duplicable?                                   # => false
    1.try(:to_s)                                    # => "1"
    nil.try(:to_s)                                  # => nil

## core_ext/range

    require 'motion-support/core_ext/range'

Loads extensions to class `Range`.

    (1..5).overlaps(3..9) # => true
    (1..5).include?(2..3) # => true

## core_ext/string

    require 'motion-support/core_ext/string'

Loads extensions to class `String`.

    "ruby_motion".camelize    # => "RubyMotion"
    "User".pluralize          # => "Users"
    "mice".singularize        # => "mouse"
    "Foo::Bar".underscore     # => "foo/bar"
    "AppDelegate".underscore  # => "app_delegate"
    "UIView".constantize      # => UIView

## core_ext/time

    require 'motion-support/core_ext/time'

Loads extensions to class `Time`.

    1.week.ago
    17.days.from_now
    Date.yesterday
    Time.beginning_of_week

# Differences to ActiveSupport

In general:

* All `I18n` stuff was removed. Maybe it will be readded later.
* No support for the `TimeWithZone` class (iOS apps don't need advanced time zone support)
* No support for the `DateTime` class
* All deprecated methods have been removed
* All `YAML` extensions were removed, since RubyMotion doesn't come with YAML support
* `Kernel#silence_warnings` and stream manipulation methods were removed
* Multibyte string handling methods have been removed
* All Rails-specific stuff was removed
* The dependency resolution code was removed. So was the dynamic code reloading code
* All marshalling code was removed
* All logging code was removed
* All extensions to `Test::Unit` were removed

Specifically:

* `Array#third` .. `Array#fourty_two` were removed
* `Array#to_xml` is missing
* `Array#to_sentence` does not accept a `:locale` parameter
* `Class#subclasses` is missing. It depends on `ObjectSpace.each_object` which is missing in RubyMotion
* `Hash#extractable_options?` is missing
* `BigDecimal` conversions were removed
* `Time.current` an alias for `Time.now`
* `Date.current` an alias for `Date.today`
* `Date#to_time` does not accept a timezone form (`:local`, `:utc`)
* `Date#xmlschema` is missing
* `String#parameterize` is missing because it needs to transliterate the string, which is dependent on the locale
* `String#constantize` is very slow. Cache the result if you use it.
* `String#to_time`, `#to_date`, `#to_datetime` are missing because they depend on `Date#_parse`
* String inquiry methods are missing
* String multibyte methods are missing
* `String#html_safe` and `ERB` extensions are not needed in RubyMotion
* `Object#to_json` and subclasses are missing
* `Range#to_s(:db)` was removed
* The `rfc822` time format was removed, since it relies on time zone support.
* Extensions to `LoadError` and `NameError` were removed
* The `ThreadSafe` versions of `Hash` and `Array` are just aliases to the standard classes

Things to do / to decide:

* RubyMotion lacks a `Date` class. in `_stdlib` there is a stub of a Date class that makes the `Date` extensions work. This stub needs to be completed.
* Implement `Object#to_json`, probably best if implemented on top of Cocoa APIs
* Implement `Object#to_xml`
* Implement `Hash#from_xml`
* Do we need `Hash#extractable_options?`?
* Do we need `Class#superclass_delegating_accessor`?
* Implement all `InfiniteComparable` extensions
* Do we need `File#atomic_write` for thread-safe file writing?
* Do we need `Kernel#class_eval` (it delegates to `class_eval` on this' singleton class)?
* Do we need `Module#qualified_const_defined?`? What is that even for?
* Do we need `Module#synchronize`?
* Implement `Numeric` conversions, especially `NumberHelper`
* Do we need `Object#with_options`?
* Implement `String#to_time`, `String#to_date`. In the original ActiveSupport, they make use of the `Date#_parse` method (which seems like it's supposed to be private). Here, they should be implemented on top of Cocoa APIs
* Do we need `String#parameterize`? If so, we need to find a way to transliterate UTF-8 characters.
* Do we need the `StringInquirer`? It allows things like `Rails.env.development?` instead of `Rails.env == 'development'`
* Do we need multibyte string handling extensions? AFAIK, all strings in RubyMotion are UTF-8. Is that true?
* Do we need `Struct#to_h`?
* Implement extensions to class `Thread` if they make sense.
* Port callbacks.rb
* Port concern.rb
* Do we need the `Configurable` module?
* Do we need the `DescendantsTracker` module?
* Do we need the `OrderedOptions` class?
* Some extensions have to be made available for Cocoa classes (`NSArray`, `NSDictionary` etc.), since we want to effectively handle return values from Objective-C methods (they return Cocoa classes). The way to do this is to write a conversion method from Cocoa class to Ruby class and delegate the extension methods in the Cocoa class to the conversion method. See `motion/core_ext/ns_string.rb` for an example.
* Go through documentation and remove or rewrite things not relevant to RubyMotion, especially examples mentioning Rails components

# Acknowledgements

ActiveSupport was originally written as part of Ruby on Rails by David Heinemeier Hansson. Over the years, countless contributors made many additions. They made this library possible.

# Forking

Feel free to fork and submit pull requests!
