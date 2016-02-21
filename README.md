# ember-functional-helpers [![Build Status](https://travis-ci.org/DockYard/ember-functional-helpers.svg?branch=master)](https://travis-ci.org/DockYard/ember-functional-helpers) [![npm version](https://badge.fury.io/js/ember-functional-helpers.svg)](https://badge.fury.io/js/ember-functional-helpers) [![Ember Observer Score](http://emberobserver.com/badges/ember-functional-helpers.svg)](http://emberobserver.com/addons/ember-functional-helpers)

Functional helpers for Ember that enables more declarative templating. These helpers can be _composed_ together to form powerful ideas:

```hbs
{{#each (map-by users "fullName") as |fullName|}}
  <input type="text" value={{fullName}} onchange={{action (mut newName)}}>
  <button {{action (pipe updateFullName saveUser) newName}}>
    Update and save {{fullName}} to {{newName}}
  </button>
{{/each}}
```

To install:

```no-highlight
ember install ember-functional-helpers
```

## Configuration
This addon performs optional tree-shaking – you can specify which helpers to whitelist or blacklist using `only` or `except` within your `config/environment.js`:

```js
module.exports = function(environment) {
  var ENV = {
    'ember-functional-helpers': {
      only: ['inc', 'dec', 'pipe'],
      except: ['pipe', 'filter-by']
    }
  };
```

Both `only` and `except` can be safely used together (the addon computes the diff), although it's best if you only use one for your own sanity.

```js
except: ['pipe'] // imports all helpers except `pipe`
only: ['pipe'] // imports only `pipe`
```

## Available helpers

* [Action](#action-helpers)
  + [`pipe`](#pipe)
  + [`compute`](#compute)
  + [`toggle`](#toggle)
* [String](#string-helpers)
  + [`w`](#w)
* [Array](#array-helpers)
  + [`map-by`](#map-by)
  + [`sort-by`](#sort-by)
  + [`filter-by`](#filter-by)
  + [`intersect`](#intersect)
  + [`union`](#union)
  + [`take`](#take)
  + [`drop`](#drop)
  + [`repeat`](#repeat)
  + [`range`](#range)
  + [`join`](#join)
* [Object](#object-helpers)
  + [`group-by`](#group-by)
* [Math](#math-helpers)
  + [`inc`](#inc)
  + [`dec`](#dec)
  + [`lt`](#lt)
  + [`lte`](#lte)
  + [`gt`](#gt)
  + [`gte`](#gte)

## Usage

### Action helpers

#### `pipe`
Pipes the return values of actions in a sequence of actions. This is useful to compose a pipeline of actions, so each action can do only one thing.

```hbs
<button {{action (pipe addToCart purchase redirectToThankYouPage) item}}>
  1-Click Buy
</button>
```

**[⬆️️ back to top](#available-helpers)**

#### `compute`
Calls an action as a template helper.

```hbs
The square of 4 is {{compute (action "square") 4}}
```

**[⬆️ back to top](#available-helpers)**

#### `toggle`
Toggles a boolean value.

```hbs
<button {{action (toggle this "isExpanded")}}>
  {{if isExpanded "I am expanded" "I am not"}}
</button>
```

**[⬆️ back to top](#available-helpers)**

---

### String helpers

#### `w`
Splits a string on whitespace and/or turns multiple words into an array

```hbs
{{#each (words "First" "Second" "Last") as |rank|}}
  Our {{rank}} place winner is ...
{{/each}}
```

or:

```hbs
{{#each (words "First Second Last") as |rank|}}
  Our {{rank}} place winner is ...
{{/each}}
```

See also: [Ember `w` documentation](http://emberjs.com/api/classes/Ember.String.html#method_w)


**[⬆️ back to top](#available-helpers)**

---

### Array helpers

#### `map-by`
Maps an array on a property.

```hbs
{{#each (map-by users "fullName") as |fullName|}}
  {{fullName}}
{{/each}}
```

**[⬆️ back to top](#available-helpers)**

#### `sort-by`
Sort an array by given properties.

```hbs
{{#each (sort-by users "lastName" "firstName") as |user|}}
  {{user.lastName}}, {{user.firstName}}
{{/each}}
```

You can append `:desc` to properties to sort in reverse order.

```hbs
{{#each (sort-by users "age:desc") as |user|}}
  {{user.firstName}} {{user.lastName}} ({{user.age}})
{{/each}}
```

**[⬆️ back to top](#available-helpers)**

#### `filter-by`
Filters an array by a property.

```hbs
{{#each (filter-by users "isActive" true) as |user|}}
  {{user.name}} is active!
{{/each}}
```

If you omit the third argument it will test if the property is truthy.

```hbs
{{#each (filter-by users "address") as |user|}}
  {{user.name}} has an address specified!
{{/each}}
```

You can also pass an action as third argument:

```hbs
{{#each (filter-by users age (action "olderThan" 18)) as |user|}}
  {{user.name}} is older than eighteen!
{{/each}}
```

**[⬆️ back to top](#available-helpers)**

#### `intersect`
Creates an array of unique values that are included in all given arrays.

```hbs
<h1>Matching skills</h1>
{{#each (intersect desiredSkills currentSkills) as |skill|}}
  {{skill.name}}
{{/each}}
```

**[⬆️ back to top](#available-helpers)**

#### `union`
Joins arrays to create an array of unique values.

```hbs
{{#each (union cartA cartB cartC) as |cartItem|}}
  {{cartItem.price}} x {{cartItem.quantity}} for {{cartItem.name}}
{{/each}}
```

**[⬆️ back to top](#available-helpers)**

#### `take`
Returns the first `n` entries of a given array.

```hbs
<h3>Top 3:</h3>
{{#each (take contestants 3) as |contestant|}}
  {{contestant.rank}}. {{contestant.name}}
{{/each}}
```

**[⬆️ back to top](#available-helpers)**

#### `drop`
Returns an array with the first `n` entries omitted.

```hbs
<h3>Other contestants:</h3>
{{#each (drop contestants 3) as |contestant|}}
  {{contestant.rank}}. {{contestant.name}}
{{/each}}
```

**[⬆️ back to top](#available-helpers)**

#### `repeat`
Repeats `n` times. This can be useful for making an n-length arbitrary list for iterating upon (you can think of this form as a times helper, a la Ruby's `5.times { ... }`):

```hbs
{{#each (repeat 3) as |empty|}}
  I will be rendered 3 times
{{/each}}
```

You can also give it a value to repeat:

```hbs
{{#each (repeat 3 "Adam") as |name|}}
  {{name}}
{{/each}}
```

**[⬆️ back to top](#available-helpers)**

#### `range`
Generates a range of numbers between a `min` and `max` value.

```hbs
{{#each (range 10 20) as |number|}}
  {{! `number` will go from 10 to 19}}
{{/each}}
```

It can also be set to `inclusive`:

```hbs
{{#each (range 10 20 true) as |number|}}
  {{! `number` will go from 10 to 20}}
{{/each}}
```

And works with a negative range:

```hbs
{{#each (range 20 10) as |number|}}
  {{! `number` will go from 20 to 11}}
{{/each}}
```

**[⬆️ back to top](#available-helpers)**

#### `join`
Joins the given array with an optional separator into a string.

```hbs
{{join categories ', '}}
```

**[⬆️ back to top](#available-helpers)**

--- 

### Object helpers

#### `group-by`
Returns an object where the keys are the unique values of the given property, and the values are an array with all items of the array that have the same value of that property.

```hbs
{{#each-in (group-by artists "category") as |category artists|}}
  <h3>{{category}}</h3>
  <ul>
    {{#each artists as |artist|}}
      <li>{{artist.name}}</li>
    {{/each}}
  </ul>
{{/each-in}}
```

**[⬆️ back to top](#available-helpers)**

---

## Math helpers

### `inc`
Increments by `1` or `step`.

```hbs
{{inc numberOfPeople}}
{{inc numberOfPeople 2}}
```

**[⬆️ back to top](#available-helpers)**

### dec
Decrements by `1` or `step`.

```hbs
{{dec numberOfPeople}}
{{dec numberOfPeople 2}}
```

**[⬆️ back to top](#available-helpers)**

### lt
Checks if the first argument is less than the second argument.

```hbs
{{lt 4 5}}
```

**[⬆️ back to top](#available-helpers)**

### lte
Checks if the first argument is less than or equal to the second argument.

```hbs
{{lte 4 5}}
```

**[⬆️ back to top](#available-helpers)**

### gt
Checks if the first argument is greater than the second argument.

```hbs
{{gt 4 5}}
```

**[⬆️ back to top](#available-helpers)**

### gte
Checks if the first argument is greater than or equal to the second argument.

```hbs
{{gte 4 5}}
```

**[⬆️ back to top](#available-helpers)**

## Installation

* `git clone` this repository
* `npm install`
* `bower install`

## Running

* `ember server`
* Visit your app at http://localhost:4200.

## Running Tests

* `npm test` (Runs `ember try:testall` to test your addon against multiple Ember versions)
* `ember test`
* `ember test --server`

## Building

* `ember build`

For more information on using ember-cli, visit [http://www.ember-cli.com/](http://www.ember-cli.com/).

## Legal

[DockYard](http://dockyard.com/ember-consulting), Inc &copy; 2016

[@dockyard](http://twitter.com/dockyard)

[Licensed under the MIT license](http://www.opensource.org/licenses/mit-license.php)
