---
title: Duplicate Code
author:  Alex Aitken
description:
version:
  revision: 0.0.0
  date: 2019-09-23
---
# Duplicate code

### Intent

Ensure that the same concept is not repeated needless across a file or the system.

### Motivation to Eliminate the Smell

- Reduce the searching to find all the places to make a change.
- Reduce the need to read very similar code to make sure there are no differences.
  - Hard pattern matching.
- Consolidate concept towards a single source of truth.
- Add names to things that you do mulitple times.

### Applicability

- When you see that there are similar code that you would have to always change together.
- When you have to make similar changes across many files when you are making a change.


### Consequences

- Reduce work on the reader to detect the repetition.
- More highly cohesive objects
- Misclassification incidental duplication as actual duplication will couple independant concepts.

### Participants

- the method, class
- new objects resulting from the refactoring
- the many classes that contain the duplication

## Implementation

### Duplication in 1 class

Find identical or nearly identical code
```ruby
class GuildedRose
  def tick
    if @name != "Aged Brie" and @name != "Backstage passes to a TAFKAL80ETC concert"
      if @quality > 0
        if @name != "Sulfuras, Hand of Ragnaros"
          @quality = @quality - 1  # similar code
        end
      end
    else
      # not needed for the example
    end

    # some more things happen

    if @days_remaining < 0
      if @name != "Aged Brie"
        if @name != "Backstage passes to a TAFKAL80ETC concert"
          if @quality > 0
            if @name != "Sulfuras, Hand of Ragnaros"
              @quality = @quality - 1 # similar code
            end
          end
        else
          @quality = @quality - @quality # similar code but a bit different.
        end
      else
        # not needed for the example
      end
    end
  end
end
```

Make the duplication identical
```ruby
class GuildedRose
  def tick
    if @name != "Aged Brie" and @name != "Backstage passes to a TAFKAL80ETC concert"
      if @quality > 0
        if @name != "Sulfuras, Hand of Ragnaros"
          amount_to_reduce = 1
          @quality = @quality - amount_to_reduce  # similar code
        end
      end
    else
      # not needed for the example
    end

    # some more things happen

    if @days_remaining < 0
      if @name != "Aged Brie"
        if @name != "Backstage passes to a TAFKAL80ETC concert"
          if @quality > 0
            if @name != "Sulfuras, Hand of Ragnaros"
              amount_to_reduce = 1
              @quality = @quality - amount_to_reduce # similar code
            end
          end
        else
          amount_to_reduce = @quality
          @quality = @quality - amount_to_reduce # similar code but a bit different.
        end
      else
        # not needed for the example
      end
    end
  end
end
```

Extract method
```ruby
class GuildedRose
  def tick
    if @name != "Aged Brie" and @name != "Backstage passes to a TAFKAL80ETC concert"
      if @quality > 0
        if @name != "Sulfuras, Hand of Ragnaros"
          reduce_quality(1)
        end
      end
    else
      # not needed for the example
    end

    # some more things happen

    if @days_remaining < 0
      if @name != "Aged Brie"
        if @name != "Backstage passes to a TAFKAL80ETC concert"
          if @quality > 0
            if @name != "Sulfuras, Hand of Ragnaros"
              reduce_quality(1)
            end
          end
        else
          reduce_quality(@quality)
        end
      else
        # not needed for the example
      end
    end
  end

  private

  def reduce_quality(amount_to_reduce)
    @quality = @quality - amount_to_reduce
  end
end
```

### Duplicaiton across many classes

This is similar to duplication in one class, the change of the duplication being Incidental is much higher.

Consider looking for other smells in the duplication for more direction on how to resolve it.


### Identifying incidental duplication

Incidental duplication is valuable.  It keeps thing that should not be coupled, not coupled.
This is especially true when two thing happen to be similar to but will change for different reasons in the future.


This example bring up questions is `active: true` incidental duplication or actual dupliaction
```ruby
class ActiveFileFinder
  def find(ids)
    File.where(id: ids).where(active: true)
  end
end


class RecentCreatedFileFinder
  def find(ids)
    File.where(id: ids).where(active: true).where("created_at >= ?", 3.days.ago)
  end
end
```


String formating.

```ruby
class Person
  def display_name
    names.join(' ').lower_case
  end

  def full_name
    names.join(' ').lower_case
  end
end
```

In this case the 2 name methods are similar, but are distinct concepts and duplication has value in this case.

There are opportunities here to refactor here, though this following refactor couples the concepts together to remove similar code.
```ruby
class Person
  def display_name
    names.join(' ').lower_case
  end

  def full_name
    display_name
  end
end
```

Where as this refactor removes the duplication by creating a 3rd concept instead of coupling the 2 name methods together.

```ruby
class Person
  def display_name
    joined_name
  end

  def full_name
    joined_name
  end

  private

  def joined_name
    names.join(' ').lower_case
  end
end
```

Once you see the 3rd concept you can now make a choice if the `joined_name` concept or the duplication is more costly.
