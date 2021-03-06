[lnk]: https://travis-ci.org/DreamTheater/Backbone.Schema
[img]: https://secure.travis-ci.org/DreamTheater/Backbone.Schema.png

# Backbone.Schema [![Build Status][img]][lnk]
The plugin is for defining model's properties with type specifying.

**Dependencies:**

  - [Backbone](https://github.com/documentcloud/backbone) `>= 1.0.0`
  - [Underscore](https://github.com/documentcloud/underscore) `>= 1.4.4`
  - [Globalize](https://github.com/jquery/globalize) `>= 0.1.1`

## Reference API
### Backbone.Model
#### Static members
  - Object `formatters`
    - Function `string`
    - Function `number`
    - Function `boolean`
    - Function `date`
    - Function `text`
    - Function `currency`
    - Function `percent`
  - Object `converters`
    - Function `string`
    - Function `number`
    - Function `boolean`
    - Function `date`
    - Function `text`
    - Function `currency`
    - Function `percent`

#### Instance members
  - Function `property(attribute, type)`
    - String `attribute`
    - String `type`
  - Function `computed(attribute, options)`
    - String `attribute`
    - Object `options`
      - Function `getter`
      - Function `setter`
  - Function `toJSON([options])`
    - Object `options`
      - Boolean `schema`

## Getting Started
### Create model
```js
var model = new Backbone.Model();
```

### Define properties
#### Type `string`
Converts value to the string. Represents as is.
```js
model.property('stringProperty', 'string');

model.set('stringProperty', 999999.99); // model.attributes.stringProperty -> "999999.99"
model.get('stringProperty'); // "999999.99"
```

#### Type `number`
Converts value to the number. Represents as the string in a format of [current culture](https://github.com/jquery/globalize#culture).
```js
model.property('numberProperty', 'number');

model.set('numberProperty', '999,999.99'); // model.attributes.numberProperty -> 999999.99
model.get('numberProperty'); // "999,999.99"
```

#### Type `boolean`
Converts value to the boolean. Represents as is.
```js
model.property('booleanProperty', 'boolean');

model.set('booleanProperty', 'true'); // model.attributes.booleanProperty -> true
model.get('booleanProperty'); // true
```

#### Type `date`
Converts value to the [Unix time](http://en.wikipedia.org/wiki/Unix_time). Represents as the string in a format of [current culture](https://github.com/jquery/globalize#culture).
```js
model.property('dateProperty', 'date');

model.set('dateProperty', '12/12/2012'); // model.attributes.dateProperty -> 1355263200000
model.get('dateProperty'); // "12/12/2012"
```

#### Type `text`
Converts value to the string, escaping an unsafe characters. Represents an unescaped string.
```js
model.property('textProperty', 'text');

model.set('textProperty', '<b>text</b>'); // model.attributes.textProperty -> "&lt;b&gt;text&lt;&#x2F;b&gt;"
model.get('textProperty'); // "<b>text</b>"
```

#### Type `currency`
Converts value to the number. Represents as the string in a format of [current culture](https://github.com/jquery/globalize#culture).
```js
model.property('currencyProperty', 'currency');

model.set('currencyProperty', '$999,999.99'); // model.attributes.currencyProperty -> 999999.99
model.get('currencyProperty'); // "$999,999.99"
```

#### Type `percent`
Converts value to the hundredths of number. Represents as the string in a format of [current culture](https://github.com/jquery/globalize#culture).
```js
model.property('percentProperty', 'percent');

model.set('percentProperty', '99.99 %'); // model.attributes.percentProperty -> 0.9999
model.get('percentProperty'); // "99.99 %"
```

### Define custom data type
```js
// Define formatter
Backbone.Model.formatters.hex = function (attribute, value) {
    return '0x' + value.toString(16).toUpperCase();
};

// Define converter
Backbone.Model.converters.hex = function (attribute, value) {
    return parseInt(value, 16);
};
```

#### Custom type `hex`
```js
model.property('hexProperty', 'hex');

model.set('hexProperty', '0xFF'); // model.attributes.hexProperty -> 255
model.get('hexProperty'); // "0xFF"
```

### Convert model to JSON
Without options `toJSON` works as [original method](http://backbonejs.org/#Model-toJSON).
```js
model.toJSON();
```

```json
{
    "stringProperty": "string",
    "numberProperty": 999999.99,
    "booleanProperty": true,
    "dateProperty": 1355263200000,
    "textProperty": "&lt;b&gt;text&lt;&#x2F;b&gt;",
    "currencyProperty": 999999.99,
    "percentProperty": 0.9999,
    "hexProperty": 255
}
```

With `{ schema: true }` option method `toJSON` will return a formatted representation.
```js
model.toJSON({ schema: true });
```

```json
{
    "stringProperty": "string",
    "numberProperty": "999,999.99",
    "booleanProperty": true,
    "dateProperty": "12/12/2012",
    "textProperty": "<b>text</b>",
    "currencyProperty": "$999,999.99",
    "percentProperty": "99.99 %",
    "hexProperty": "0xFF"
}
```

### Define computed property
```js
// Create model
var user = new Backbone.Model({
    firstName: 'Dmytro',
    lastName: 'Nemoga'
});

// Define computed property
user.computed('fullName', {
    getter: function (attribute, value) {
        var firstName = this.get('firstName'),
            lastName = this.get('lastName');

        return firstName + ' ' + lastName;
    },

    setter: function (attribute, value) {
        var fullName = value.split(' ');

        return {
            firstName: fullName[0],
            lastName: fullName[1]
        };
    }
});

// Get computed property
user.get('fullName'); // "Dmytro Nemoga"

// Set computed property
user.set('fullName', 'Andriy Serputko'); // user.attributes -> { firstName: "Andriy", lastName: "Serputko" }
```

### Keeping integrity
The plugin prevents setting `undefined` values, instead of this it assigns a default value or `null`.

## Changelog
### 0.2.0
  - Static methods runs in correct context, now they may be used as independent helpers

### 0.1.9
  - Properties `formatters` and `converters` is static

### 0.1.8
  - Removed CommonJS support

### 0.1.7
  - Added CommonJS support

### 0.1.6
  - Integration with project **Backbone.Accessors**
  - Method `defineProperty` renamed to `property`
  - Methods `addGetter`/`addSetter` merged to method `computed`
  - Option `advanced` of `toJSON` method renamed to `schema`

### 0.1.2
  - Removed argument `options` of `defineProperty` method's

### 0.1.1
  - Method `addProperty` renamed to `defineProperty`

### 0.1.0
  - Initial release
