---
title: Long Parameter List 
author: Gerry Power
description: Summary of the intent, motivation and implementation of Martin Fowler's 'Long Parameter List' refactoring
version:
  revision: 0.0.4
  date: 2019-07-06
---
# Long Parameter List

### Intent

Ensure a method signature limits the parameter list

### Motivation to Eliminate the Smell

- Long parameter lists cause bugs: they are hard to understand; it's easy to provide the wrong parameter
- Long parameter lists make a method signature very hard to read
- Long parameter lists coupled with poor variable naming choices obfuscate the method body
- Cognitive load ^

### Applicability

- apply when method parameters exceed four(4)
- consider applying when method parameters exceed two(2)

### Consequences

- Increased readability of code - shorter method parameters allow the maintainer to grasp the method collaborators 
- Easier to use methods - methods signature is easier to understand and implement
- Easier to test - if method now has passed objects, provides additional seams for behaviour verification (when applicable)

### Participants

- the method class
- callers of the method
- new objects resulting from the refactoring

## Implementation

#### Replace Parameter with Query

If you can obtain one parameter by asking another parameter for it, use **Replace Parameter with Query**.

Consider the following code:

```ruby
def	statement_charges(cost, tax_rate, item_name, item_id)
  ...
end
```

Using *Replace Parameter with Query*, we can reduce the method parameters to happy space of two(2):

```ruby
def	statement_charges(cost, item_name, item_id)
  cost = cost.value
  tax_rate = cost.tax_rate
  item_name = item.name
  item_id = item.id
  ...
end
```



#### Preserve Whole Object

Rather than pulling data from an existing object to be passed, you can use **Preserve Whole Object** to pass the original data structure instead.

Consider the following code:

```ruby
def	statement_charges(cost, tax_rate, item_name, item_id)
  ...
end
```

Using *Preserve Whole Object*, we can reduce the method parameters to really happy space of one(1):

```ruby
def	statement_charges(line_item)
  cost = line_item.cost
  tax_rate = line_item.tax_rate
  item_name = line_item.name
  item_id = line_item.id
  ...
end
```



#### Introduce Parameter Object

If several parameters always fit together, combine them with **Introduce Parameter Object**.

Consider the following code: 

```ruby
def	statement_charges_between_inclusive(customer, start_date, end_date)
  ...
end

def amount_overdue_between_inclusive(customer, start_date, end_date)
  ...
end
```

Using *Introduce Parameter Object*, we can reduce the method parameters by one(1) and reduce other method signatures as well:

```ruby
def date_range(start_date, end_date)
  ...
end

def	statement_charges_between_inclusive(customer, date_range)
  ...
end

def amount_overdue_between_inclusive(customer, date_range)
  ...
end
```

#### Remove Flag Argument

If a parameter is used as a flag to dispatch different behaviour, use **Remove Flag Argument**.

Consider the following code:

```ruby
def	statement_charges(cost, tax_rate, item_name, item_id, with_discount)
  ...
end
```

Using *Remove Flag Argument*, we can reduce the method parameters by one(1) and increase the method readability:

```ruby
def	statement_charges(cost, tax_rate, item_name, item_id)
  with_discount = false
  ...
end

def	statement_charges_with_discount(cost, tax_rate, item_name, item_id)
  with_discount = true
  ...
end
```

#### Combine Functions into Class

When multiple functions share several parameter values, use **Combine Functions into Class** to capture those common values as fields.

Consider the following code for creating a statement line item:

```ruby
def cost(line_item)
  ...
end

def tax(line_item)
  ...
end

def total(line_item)
  ...
end
```

Using *Combine Functions into Class*:

```ruby
class LineItem
	def cost
  ...
	end

	def tax
  ...
	end

	def total
  ...
	end
end
```

