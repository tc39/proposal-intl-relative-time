# Intl.RelativeTimeFormat([locales[, options]])

## Rationale

Due to common use, relative time formatted values exist in a majority of websites and are available in the majority of the frameworks (e.g.: React - via react-intl -; Ember - via ember-intl -). Popular localization libraries like momentjs and formatjs had implemented a formatting process for relative time values as well.

It is highly probable that the majority of current relative time formatting implementations require a large portion of CLDR raw or compiled data to format relative time values. Bringing this into the platform will improve performance of the web, and developer productivity as they no longer have to bring extra libraries to format relative time values.

## Proposal

`Intl.RelativeTimeFormat` is a low level API to facilitate libraries and frameworks to format ordinals and cardinals in a localized fashion. It is also the first API under `Intl` that is not focused on formatting, but enables access to localization data that is already available in all browsers.

## Spec
You can view the [spec text](spec/relativetimeformat.html) or rendered as [HTML](http://caridy.github.io/intl-relative-time-spec/index.html).

## Naming

For consistency with `Intl.NumberFormat` and `Intl.DateTimeFormat`, we have chosen a similar form for this new feature. The creation of `Intl.RelativeTimeFormat` instance is an expensive operation that requires resolution of locale data, and most likely, libraries will attempt to cache those instances, just like they do for `Intl.NumberFormat`, and `Intl.DateTimeFormat`.

We have also chosen `style` as the primary form of switching between different formatting rules for consistency with `Intl.NumberFormat`, and `Intl.DateTimeFormat`.

Since this new feature does format a provided value just like instances of `Intl.NumberFormat`, and `Intl.DateTimeFormat`, we have chosen the same form by provided a `format(value)` method of the instance, which returns a formatted string value.

### Libraries supporting relative time formatting today

Few libraries that would likely get benefited by `Intl.PluralForm` since they will not have to fetch CLDR data to format relative time values:

* momentjs: relative time format (e.g.: `"1 hour ago"` vs `"2 hours ago"`).
* formatjs: relative time format.

### Relative time in other languages

Java:

```java
TBD
```

PHP:

```php
TBD
```
