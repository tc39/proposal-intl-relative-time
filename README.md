## `Intl.RelativeTimeFormat` API Specification [draft]

### Status

__Stage 1__

Implementation Progress

 * Polyfill (in progress)

Backpointers

 * https://github.com/tc39/ecma402/issues/35

### Authors

 * Caridy Patiño (@caridy)
 * Eric Ferraiuolo (@ericf)
 * Zibi Braniecki (@zbraniecki)

### Reviewers

TBD

### Informative

This proposal is based on the ICU Relative Date Time Formatter and on the Unicode CLDR Calendar Fields Relative values:

* http://icu-project.org/apiref/icu4j/com/ibm/icu/text/RelativeDateTimeFormatter.html
* http://www.unicode.org/reports/tr35/tr35-dates.html#Calendar_Fields 

It is also based on the LDML spec, C.11 Language Plural Rules:

* http://unicode.org/reports/tr35/tr35-numbers.html#Language_Plural_Rules

### Prior Art

TBD

### Usage

```javascript
let rtf = new Intl.RelativeTimeFormat("en", {
    style: "long", // "long" (default), "short", or "narrow"
    type: "text" // "text" (default), or "numeric".
});

rtf.format(
  -1,
  "day" // "second", "minute", "hour", "day", "week", "month", "quarter", or "year".
);
// > "yesterday"
// or "1 day ago" if it was constructed using {type: "numeric"}

rtf.format(2.15, "day");
// > "in 2.15 days"

rtf.format(100, "day");
// > "in 100 days"

rtf.format(0, "day");
// > "today"
// or "in 0 days" if it was constructed using {type: "numeric"}

rtf.format(-0, "day");
// > "today"
// or "0 days ago" if it was constructed using {type: "numeric"}

let a = new Date(2017, 2, 20);
let b = new Date(2017, 2, 21);
let daysInMs = 864e5; // 24 * 60 * 60 * 1000
rtf.format((a - b) / daysInMs, "day");
// > "yesterday"
// or "1 day ago" if it was constructed using {type: "numeric"}

rtf.format((b - a) / daysInMs, "day");
// > "tomorrow"
// or "in 1 day" if it was constructed using {type: "numeric"}
```

Additionally, by combining `style` and `unit`, you can achieve any of the following results:

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
```

### TODO

Add support for relative week day:

```text
last Sunday
this Sunday
next Sunday
last Sun.
this Sun.
next Sun.
last Su
this Su
next Su
last Monday
this Monday
next Monday
last Mon.
this Mon.
next Mon.
last M
this M
next M
last Tuesday
this Tuesday
next Tuesday
last Tue.
this Tue.
next Tue.
last Tu
this Tu
next Tu
last Wednesday
this Wednesday
next Wednesday
last Wed.
this Wed.
next Wed.
last W
this W
next W
last Thursday
this Thursday
next Thursday
last Thu.
this Thu.
next Thu.
last Th
this Th
next Th
last Friday
this Friday
next Friday
last Fri.
this Fri.
next Fri.
last F
this F
next F
last Saturday
this Saturday
next Saturday
last Sat.
this Sat.
next Sat.
last Sa
this Sa
next Sa
```

### Render Spec

```
npm install
npm run build
open index.html
```
