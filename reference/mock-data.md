# Mock data

{% hint style="info" %}
Mock data is powered by Mock.js.
{% endhint %}

Mock.js syntax specification consists of two parts:

1. Data Template Definition (DTD)
2. Data Placeholder Definition (DPD)

## Data Template Definition (DTD)

**Each property in a data template consists of 3 parts: property name, generation rule, and property value:**

```js
// Property name   name
// Generation rule rule
// Property value  value
'name|rule': value
```

**Notes:**

- The property name and generation rule are separated by a vertical bar `|`.
- The generation rule is optional.
- There are 7 formats for generation rules:
    1. `'name|min-max': value`
    2. `'name|count': value`
    3. `'name|min-max.dmin-dmax': value`
    4. `'name|min-max.dcount': value`
    5. `'name|count.dmin-dmax': value`
    6. `'name|count.dcount': value`
    7. `'name|+step': value`
- **The meaning of a generation rule depends on the type of the property value.**
- Property values can contain `@placeholders`.
- Property values also specify the initial value and type of the final value.

### Generation Rules and Examples:

#### 1. Property Value is String

1. `'name|min-max': string`

   Generates a string by repeating `string`, with the number of repetitions greater than or equal to `min` and less than or equal to `max`.

2. `'name|count': string`

   Generates a string by repeating `string`, with the number of repetitions equal to `count`.

#### 2. Property Value is Number

1. `'name|+1': number`

   The property value automatically increments by 1, starting from `number`.

2. `'name|min-max': number`

   Generates an integer greater than or equal to `min` and less than or equal to `max`. The property value `number` is only used to determine the type.

3. `'name|min-max.dmin-dmax': number`

   Generates a floating-point number where the integer part is greater than or equal to `min` and less than or equal to `max`, and the decimal part has between `dmin` and `dmax` digits.

```js
Mock.mock({
  'number1|1-100.1-10': 1,
  'number2|123.1-10': 1,
  'number3|123.3': 1,
  'number4|123.10': 1.123
})
// =>
{
  "number1": 12.92,
  "number2": 123.51,
  "number3": 123.777,
  "number4": 123.1231091814
}
```

#### 3. Property Value is Boolean

1. `'name|1': boolean`

   Randomly generates a boolean value, with a 1/2 probability of being true and a 1/2 probability of being false.

2. `'name|min-max': value`

   Randomly generates a boolean value, with the probability of being `value` equal to `min / (min + max)`, and the probability of being `!value` equal to `max / (min + max)`.

#### 4. Property Value is Object

1. `'name|count': object`

   Randomly selects `count` properties from the property value `object`.

2. `'name|min-max': object`

   Randomly selects between `min` and `max` properties from the property value `object`.

#### 5. Property Value is Array

1. `'name|1': array`

   Randomly selects 1 element from the property value `array` as the final value.

2. `'name|+1': array`

   Sequentially selects 1 element from the property value `array` as the final value.

3. `'name|min-max': array`

   Generates a new array by repeating the property value `array`, with the number of repetitions greater than or equal to `min` and less than or equal to `max`.

4. `'name|count': array`

   Generates a new array by repeating the property value `array`, with the number of repetitions equal to `count`.

#### 6. Property Value is Function

1. `'name': function`

   Executes the function `function` and uses its return value as the final property value. The function's context is the object where the property `'name'` is located.

#### 7. Property Value is RegExp

1. `'name': regexp`

   Generates a string that matches the regular expression `regexp` in reverse. Used for generating strings in custom formats.

```js
Mock.mock({
  'regexp1': /[a-z][A-Z][0-9]/,
  'regexp2': /\w\W\s\S\d\D/,
  'regexp3': /\d{5,10}/
})
// =>
{
  "regexp1": "pJ7",
  "regexp2": "F)\fp1G",
  "regexp3": "561659409"
}
```

## Data Placeholder Definition (DPD)

Placeholders only occupy a position in the property value string and do not appear in the final property value.

The format of placeholders is:

```
@placeholder
@placeholder(parameter [, parameter])
```

**Notes:**

1. Use `@` to identify that the following string is a placeholder.
2. Placeholders reference methods from `Mock.Random`.
3. Extend custom placeholders through `Mock.Random.extend()`.
4. Placeholders can also reference properties from the data template.
5. Placeholders prioritize referencing properties from the data template.
6. Placeholders support both relative and absolute paths.

```js
Mock.mock({
  name: {
    first: '@FIRST',
    middle: '@FIRST',
    last: '@LAST',
    full: '@first @middle @last'
  }
})
// =>
{
  "name": {
    "first": "Charles",
    "middle": "Brenda",
    "last": "Lopez",
    "full": "Charles Brenda Lopez"
  }
}
```