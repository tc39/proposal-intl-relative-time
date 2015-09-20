## `Intl.RelativeTimeFormat` API Specification [draft]

This proposal is based on Unicode Relative Date Time Format method.

It is also based on the Unicode Language Plural Rules:

 * http://unicode.org/reports/tr35/tr35-numbers.html#Language_Plural_Rules

### Example

```javascript
let a = new Intl.RelativeTimeFormat("en", {
    unit: "best fit" // or "second", "minute", "hour", "day", "week", "month", "quarter" or "year" (defaults to "best fit")
});
console.log(a.format(Date.now() - 1000 * 60 * 60 * 25)); // yields "yesterday"
console.log(a.format(Date.now() - 1000 * 60 * 60 * 50)); // yields "2 days ago"

let b = new Intl.RelativeTimeFormat("en", {
    unit: "hour"
});
console.log(b.format(Date.now() - 1000 * 60 * 60 * 25)); // yields "25 hours ago"
console.log(b.format(Date.now() - 1000 * 60 * 60 * 50)); // yields "50 hours ago"

let c = new Intl.RelativeTimeFormat("en", {
    style: "narrow" // or "short" or "long" (defaults to "long")
});
console.log(c.format(Date.now() + 1000 * 60 * 60 * 24 * 30)); // yields "next mo."
console.log(c.format(Date.now() + 1000 * 60 * 60 * 24 * 60)); // yields "in 2 mo."
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

### Usage

```
npm install
npm run build
open index.html
```

### Details about this proposal

 * https://github.com/tc39/ecma402/issues/35
 * https://groups.google.com/forum/#!topic/javascript-globalization/3nFDf5al5hU
