# `Intl.RelativeTimeFormat` API Specification [draft]

## Overview

### Motivation

Due to common use, relative time–formatted values exist in a majority of websites and are available for the majority of frameworks (e.g., React, via [react-intl](https://github.com/yahoo/react-intl) and [react-globalize](https://github.com/globalizejs/react-globalize); Ember, via [ember-intl](https://github.com/ember-intl/ember-intl)). Popular localization libraries like [Moment.js](https://momentjs.com/), [Format.js](https://formatjs.io/), [Globalize](http://globalizejs.com/), and [others](https://github.com/rxaviers/javascript-globalization) have implemented a formatting process for relative time values as well.

It is highly probable that the majority of current relative time formatting implementations require a large portion of CLDR raw or compiled data to format relative time values. Bringing this into the platform will improve performance of the web and developer productivity as they no longer have to bring extra weight to format relative time values.

### Usage examples

The following example shows how to create a relative time formatter using the English language.

> Units : "year", "quarter", "month", "week", "day", "hour", "minute" and "second".

```js
// Create a relative time formatter in your locale
// with default values explicitly passed in.
const rtf = new Intl.RelativeTimeFormat("en", {
    localeMatcher: "best fit", // other values: "lookup"
    numeric: "always", // other values: "auto"
    style: "long", // other values: "short" or "narrow"
});


// Format relative time using negative value (-1).
rtf.format(-1, "day");
// > "1 day ago"

// Format relative time using positive  value (1).
rtf.format(1, "day");
// > "in 1 day"

```

> Note: If `numeric:auto` option is passed, it will produce the string `yesterday` or `tomorrow` instead of `1 day ago` or `in 1 day`, this allows to not always have to use numeric values in the output.

```js
// Create a relative time formatter in your locale
// with numeric: "auto" option value passed in.
const rtf = new Intl.RelativeTimeFormat("en", { numeric: "auto" });

// Format relative time using negative value (-1).
rtf.format(-1, "day");
// > "yesterday"

// Format relative time using positive day unit (1).
rtf.format(1, "day");
// > "tomorrow"
```

### Implementation Status

__Stage 3__

Implementation Progress

- [V8 v7.1.179](https://bugs.chromium.org/p/v8/issues/detail?id=7869), shipped in Chrome 71
- Shipped in Firefox 65
- Polyfills:
    - https://www.npmjs.com/package/@formatjs/intl-relativetimeformat
    - https://www.npmjs.com/package/intl-relative-time-format
    - https://www.npmjs.com/package/relative-time-format
- [Browser compatibility](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RelativeTimeFormat#Browser_compatibility)

Backpointers

- https://github.com/tc39/ecma402/issues/35

#### Authors

- Caridy Patiño (@caridy)
- Eric Ferraiuolo (@ericf)
- Zibi Braniecki (@zbraniecki)
- Rafael Xavier (@rxaviers)
- Daniel Ehrenberg (@littledan)

#### Reviewers

TBD

## Proposal

`Intl.RelativeTimeFormat` is a low level API to facilitate libraries and frameworks to format relative time in a localized fashion by providing internationalized messages for date and time fields, using customary word or phrase when available.

### Spec

You can view the [spec text](spec/relativetimeformat.html) or rendered as [HTML](https://rawgit.com/tc39/proposal-intl-relative-time/master/index.html).

### Technical Design

This proposal is based on the ICU Relative Date Time Formatter and on the Unicode CLDR Calendar Fields Relative values:

- http://icu-project.org/apiref/icu4j/com/ibm/icu/text/RelativeDateTimeFormatter.html
- https://unicode.org/reports/tr35/tr35-dates.html#Calendar_Fields

It is also based on the LDML spec, C.11 Language Plural Rules:

- https://unicode.org/reports/tr35/tr35-numbers.html#Language_Plural_Rules

#### Prior Art

##### Java

- [ICU](http://icu-project.org/apiref/icu4j/com/ibm/icu/text/RelativeDateTimeFormatter.html): `com.ibm.icu.impl.RelativeDateFormat`
- `org.ocpsoft.prettytime.PrettyTime`

##### Ruby

```ruby
include ActionView::Helpers::DateHelper
def index
  @friendly_date = time_ago_in_words(Date.today - 1)
end
```

#### Naming

For consistency with `Intl.NumberFormat` and `Intl.DateTimeFormat`, we have chosen a similar form for this new feature. The creation of an `Intl.RelativeTimeFormat` instance is an expensive operation that requires resolution of locale data, and most likely, libraries will attempt to cache those instances, just like they do for `Intl.NumberFormat` and `Intl.DateTimeFormat`.

We have also chosen `style` as the primary form of switching between different formatting forms for consistency with `Intl.NumberFormat` and `Intl.DateTimeFormat`.

Since this new feature does format a provided value just like instances of `Intl.NumberFormat`, and `Intl.DateTimeFormat`, we have chosen the same form by providing a `format(value)` method of the instance, which returns a formatted string value.

#### Take number instead of date object for input

Relative time is used to display date distances, therefore the natural form of input should intuitively be a date object. Although, in this API we chose to take a number instead due to the following reasons:

1. Basically, taking a number as input for the format method instead of a date object significantly simplifies the scope of this proposal while it still fully addresses the main objective which is to provide i18n building blocks to address this problem realm.
2. Taking a date object means we should implement the comparison logic (relative time is about date distance between target and source dates). The source date is usually *now*, but not always. We would have to address modifying that. See [#4](https://github.com/tc39/proposal-intl-relative-time/issues/4).
3. Taking a date object also means we should allow for different calendar calculations, which implies `Date` should support it. See [#6](https://github.com/tc39/proposal-intl-relative-time/issues/6) and [#13](https://github.com/tc39/proposal-intl-relative-time/issues/13).
4. Taking a date object suggests we should be able to implement a *bestFit* algorithm, which has its own API challenges with respect to standardizing an approach that works for all cases. See [#7](https://github.com/tc39/proposal-intl-relative-time/issues/7), [#14](https://github.com/tc39/proposal-intl-relative-time/issues/14), and [#15](https://github.com/tc39/proposal-intl-relative-time/issues/15). We'd probably need to provide a flag for users to fill, with no default setting, to choose between options for calendar calculation.

#### Take number as input rather than exposing the underlying database

An idea has been floated, in the context of "the extensible web", of just exposing the engine's copy of the CLDR database rather than a higher-level interface would be better. In the case of this specification, there is already a JS object model ready to go--the locale database is represented internally in the spec as a JavaScript object.

However, we opted not to go that route for a couple reasons:
- As described above, the API is already fairly low-level, taking numbers rather than dates.
- Although there are clearly use cases for different policies about rounding dates into units, we haven't come across a use case for seeing the underlying data.
- This new API is analogous to previous APIs, which should be useful for people learning the system.
- CLDR changes schema over time; if the data model improves, implementations can transparently upgrade users to better results with the same API. However, if we freeze things to the current logic, the old data model would need to be emulated.

#### Difference between this and `UnitFormat`

The fundamental difference between `RelativeTimeFormat` and `UnitFormat` is that `RelativeTimeFormat` displays a relative unit (e.g., `5 days ago` or `in 5 days`) while `UnitFormat` displays an absolute unit (e.g., `-5 meters` or `5 meters`). Note that `RelativeTimeFormat` uses different internationalized messages based on the value sign direction, while `UnitFormat` uses the same internationalized message for all values.

##### Countdowns, e.g., 15 days, 0 hours, 27 minutes, and 52 seconds

A countdown for example is a mix of `UnitFormat` and `ListFormat`, and is not a `RelativeTimeFormat`.

#### NumberFormat options (e.g., useGrouping, maximumFractionDigits)

RelativeTimeFormat messages may include number parts (e.g., the `1,000` in `1,000 days ago`), which are formatted using the NumberFormat default options.

In this design, we didn't find any use case that could justify allowing to change/override these NumberFormat default options. Therefore, RelativeTimeFormat doesn't include any NumberFormat option.

### API

#### `Intl.RelativeTimeFormat([locales[, options]])`

The `Intl.RelativeTimeFormat` object is a constructor for objects that enable language-sensitive relative time formatting.

#### `locales`

Optional. A string with a BCP 47 language tag, or an array of such strings. For the general form and interpretation of the `locales` argument, see the [`Intl` page](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_identification_and_negotiation).

#### `options`

Optional. An object with some or all of the following properties:

##### `localeMatcher`

The locale matching algorithm to use. Possible values are `"lookup"` and `"best fit"`; the default is `"best fit"`. For information about this option, see the  [`Intl` page](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_negotiation).

##### `numeric`
The format of output message. Possible values are  `"always"` (default, e.g., `1 day ago`), or `"auto"` (e.g., `yesterday`). `"auto"` allows to not always have to use numeric values in the output.

##### `style`

The length of the internationalized message. Possible values are: `"long"` (default, e.g., `in 1 month`); `"short"` (e.g., `in 1 mo.`), or `"narrow"` (e.g., `in 1 mo.`). The narrow style could be similar to the short style for some locales.

#### Example

```js
// Create a relative time formatter in your locale.
let rtf = new Intl.RelativeTimeFormat("en", {
    localeMatcher: "best fit", // other values: "lookup"
    numeric: "always", // other values: "auto"
    style: "long", // other values: "short" or "narrow"
});
```

### `Intl.RelativeTimeFormat.prototype.format(value, unit)`

The `Intl.RelativeTimeFormat.prototype.format` method formats a `value` and `unit` according to the locale and formatting options of this `Intl.RelativeTimeFormat` object.

While this method automatically provides the correct plural forms, the grammatical form is otherwise as neutral as possible. It is the caller's responsibility to handle cut-off logic such as deciding between displaying "in 7 days" or "in 1 week". This API does not support relative dates involving compound units. e.g "in 5 days and 4 hours".

#### `value`

Numeric value to use in the internationalized relative time message.

#### `unit`

Unit to use in the relative time internationalized message. Possible values are: `"year"`, `"quarter"`, `"month"`, `"week"`, `"day"`, `"hour"`, `"minute"`, `"second"`. Plural forms are also permitted.

#### Example

```js
const rtf = new Intl.RelativeTimeFormat("en", { numeric: "auto" });

// Format relative time using the day unit.
rtf.format(-1, "day");
// > "yesterday"

rtf.format(2.15, "day");
// > "in 2.15 days"

rtf.format(100, "day");
// > "in 100 days"

rtf.format(0, "day");
// > "today"

rtf.format(-0, "day");
// > "today"
```

Additionally, by combining the class option `style` and `unit`, you can achieve any of the following results:

```text
last year
this year
next year
in 1 year
in 2 years
1 year ago
2 years ago
yr.
last yr.
this yr.
next yr.
in 1 yr.
in 2 yr.
1 yr. ago
2 yr. ago
last quarter
this quarter
next quarter
in 1 quarter
in 2 quarters
1 quarter ago
2 quarters ago
last qtr.
this qtr.
next qtr.
in 1 qtr.
in 2 qtrs.
1 qtr. ago
2 qtrs. ago
last month
this month
next month
in 1 month
in 2 months
1 month ago
2 months ago
last mo.
this mo.
next mo.
in 1 mo.
in 2 mo.
1 mo. ago
2 mo. ago
last week
this week
next week
in 1 week
in 2 weeks
1 week ago
2 weeks ago
last wk.
this wk.
next wk.
in 1 wk.
in 2 wk.
1 wk. ago
2 wk. ago
in 1 day
in 2 days
1 day ago
2 days ago
yesterday
today
tomorrow
in 1 hour
in 2 hours
1 hour ago
2 hours ago
in 1 hr.
in 2 hr.
1 hr. ago
2 hr. ago
in 1 minute
in 2 minutes
1 minute ago
2 minutes ago
in 1 min.
in 2 min.
1 min. ago
2 min. ago
in 1 second
in 2 seconds
1 second ago
2 seconds ago
in 1 sec.
in 1 sec.
1 sec. ago
2 sec. ago
now
```

### `Intl.RelativeTimeFormat.prototype.formatToParts(value, unit)`

The `Intl.RelativeTimeFormat.prototype.formatToParts` method is a version of the `format` method which it returns an array of objects which represent "parts" of the object, separating the formatted number into its consituent parts and separating it from other surrounding text. These objects have two properties: `type` a NumberFormat formatToParts type, and `value`, which is the String which is the component of the output. If a "part" came from NumberFormat, it will have a `unit` property which indicates the unit being formatted; literals which are part of the larger frame will not have this property.

#### Example

```js
const rtf = new Intl.RelativeTimeFormat("en", { numeric: "auto" });

// Format relative time using the day unit.
rtf.formatToParts(-1, "day");
// > [{ type: "literal", value: "yesterday"}]

rtf.formatToParts(100, "day");
// > [{ type: "literal", value: "in " }, { type: "integer", value: "100", unit: "day" }, { type: "literal", value: " days" }]
```

## Development

### Render Spec

```
npm install
npm run build
open index.html
```
