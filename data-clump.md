---
title: Data Clump
author: Shahriyar Nasir
description:
version:
  revision: 0.0.0
  date: 2019-11-27
---

# Data Clump

### Intent

Increase abstraction and cohesion by gathering up data that appears together a lot. This improves readability and creates 
a home to put related behaviours.

### Motivation to Eliminate the Smell

- Grouping together related data that appears together into a higher level concept makes it easier to understand.
- Can reduce number of parameters of methods that use the data clump data which can make code less noisey thereby
simplifying method calls
- Can end up shrinking the objects that started out hosting the data clumps because they can be moved out

### Applicability

- When you see similar pieces of data appearing together frequently
- When one piece of the related data cannot appear without the other (e.g. coordinates in Polar Coordinates)

### Consequences

- Increase abstraction which means less for the reader to maintain in their mind
- Increase cohesion
- Being able to judge "what is in and what is not" when a seemingly related piece of data tries to join the data clump

### Participants

- Surrounding class
- Methods that take the data clump as parameters

## Implementation

Start Date and End Date keep appearing together and appear to represent a "Time Interval" concept:

```ruby
class WageCalculator
  def initialize(activity, start_date, end_date, rate)
    @activity = activity
    @start_date = start_date
    @end_date = end_date
    @rate = rate
  end

  def calculate_wage
    @rate * duration
  end

  def duration
    (end_date - start_date)
  end
end
```

Perform *Extract Class* to introduce this concept as a `TimeInterval` and then use *Introduce Parameter Object* to simplify method parameter lists:

```ruby
class TimeInterval
  attr_reader :start_date, :end_date

  def initialize(start_date, end_date)
    @start_date = start_date
    @end_date = end_date
  end
end

class PaymentCalculator
  def initialize(activity, time_report, rate)
    @activity = activity
    @time_report = time_report
    @rate = rate
  end

  def calculate_wage
    @rate * duration
  end

  def duration
    @time_report.start_date - @time_report.end_date
  end
end
```

Some feature envy may start to show in which case perform a *Move Method*:

```ruby
class TimeInterval
  def initialize(start_date, end_date)
    @start_date = start_date
    @end_date = end_date
  end

  def duration
    @end_date - @start_date
  end
end

class PaymentCalculator
  def initialize(activity, time_report, rate)
    @activity = activity
    @time_report = time_report
    @rate = rate
  end

  def calculate_wage
    @rate * duration
  end

  def duration
    @time_report.duration
  end
end
```

*Inline Method* to simplify further:

```ruby
class TimeInterval
  def initialize(start_date, end_date)
    @start_date = start_date
    @end_date = end_date
  end

  def duration
    @end_date - @start_date
  end
end

class PaymentCalculator
  def initialize(activity, time_report, rate)
    @activity = activity
    @time_report = time_report
    @rate = rate
  end

  def calculate_wage
    @rate * @time_report.duration
  end
end
```
