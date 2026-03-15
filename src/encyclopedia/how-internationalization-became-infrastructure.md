
Most web applications are built in English first. Adding another language seems like a straightforward task: replace the strings with translated strings. Create a JSON file with translations. Done.

The reality is that internationalization — i18n, for the 18 characters between the i and the n — is significantly more complex than string replacement. It touches character encoding, text direction, date and number formatting, plural rules, string interpolation, sorting, and more. Doing it correctly requires infrastructure that many teams don't put in place until they've already built the wrong assumptions into their application.

---

## Character encoding — the foundation

Before translations, there's character encoding. Most English text fits in ASCII — 128 characters that cover the English alphabet, digits, and punctuation. The rest of the world does not.

Unicode assigns a code point to every character in every writing system. UTF-8 is the encoding that makes Unicode practical for the web: ASCII characters use one byte (backwards compatible), other characters use two to four bytes. UTF-8 has been the standard encoding for HTML since HTML5 mandated it, and for most text storage and transmission since around 2008-2010.

The problem was legacy. Databases and applications built before UTF-8 was standard stored text in encodings like Latin-1, Windows-1252, or Shift-JIS. Converting to Unicode required careful data migration. Mixing encodings in a pipeline — database returns Latin-1, application expects UTF-8, output is garbled — was a common source of bugs in the 2000s.

In 2024, UTF-8 is universal enough that character encoding is rarely a problem for new applications. Legacy applications are another matter.

---

## Right-to-left text

Arabic, Hebrew, Persian, and Urdu are written right to left. Supporting these languages in a web application requires more than displaying the characters correctly.

The `dir="rtl"` attribute on the `<html>` element flips the base text direction. Browser layout handles most of the adjustment automatically: text aligns right, inline elements flow right to left, the start of a text run is on the right.

What doesn't adjust automatically: your CSS. Properties like `margin-left` and `padding-right` are physical — they refer to the left and right sides of the screen. Logical properties like `margin-inline-start` and `padding-inline-end` are directional — they refer to the start and end of the text flow, which is left in LTR and right in RTL.

A fully internationalized application uses logical CSS properties. A component that has `margin-left: 16px` to create space between an icon and text looks wrong in RTL layouts because the icon is now on the right side and the space is on the wrong side. `margin-inline-start: 16px` adjusts automatically.

Icons and UI elements that are directional — arrows, chevrons, progress indicators — may need to be mirrored in RTL. A chevron pointing right in LTR should point left in RTL when used as a "back" indicator.

---

## Plural rules

English has two plural forms: one (1 item) and many (2+ items). Many languages have more. Russian has four plural forms. Arabic has six. Polish has rules based on the last digit and last two digits of the number.

A naive i18n implementation provides two strings: `"1 item"` and `"{count} items"`. This breaks for languages with more plural categories.

The CLDR (Common Locale Data Repository) defines the plural rules for hundreds of locales. Internationalization libraries like `i18next`, `react-intl`, and the browser-native `Intl.PluralRules` implement these rules so you can provide translations for each plural category and get the right string for any count.

---

## Date and number formatting

January 3, 2024 is written as 01/03/2024 in the US, 03/01/2024 in most of Europe, and 2024/01/03 in Japan. If you format a date as a string and display it directly, you're probably displaying a format that at least some of your users find confusing or ambiguous.

Numbers have similar variation. The number 1,234.56 uses a period as the decimal separator and a comma as the thousands separator in the US. In Germany, it's 1.234,56. In Switzerland, 1 234,56.

The browser's `Intl` API handles these correctly:

```javascript
// 2024-01-03
const date = new Date(2024, 0, 3);

new Intl.DateTimeFormat('en-US').format(date);  // "1/3/2024"
new Intl.DateTimeFormat('de-DE').format(date);  // "3.1.2024"
new Intl.DateTimeFormat('ja-JP').format(date);  // "2024/1/3"

// 1234.56
new Intl.NumberFormat('en-US').format(1234.56);  // "1,234.56"
new Intl.NumberFormat('de-DE').format(1234.56);  // "1.234,56"
```

Using `Intl` instead of manual date and number formatting eliminates an entire category of locale-specific bugs.

---

## Translation infrastructure

The workflow for translations in a real application involves more than a JSON file. Translators need context — not just the string, but where it appears and what it refers to. Strings that include variables need to be handled with message format syntax, not string concatenation:

```
// Bad — untranslatable because the grammar might be different
"Hello " + name + "!"

// Good — the full message is the translation unit
t('greeting', { name: 'Renan' })
// Translation: "Hello, {name}!" in English
// Translation: "Olá, {name}!" in Portuguese
```

Translation management platforms like Crowdin, Phrase, and Lokalise integrate with code repositories, extract translation keys, provide workflows for translators, and push updated translations back. For any application with non-trivial translation needs, this infrastructure is worth setting up before the translation work begins, not after.

---

## The cost of ignoring it

Applications built without i18n in mind have specific failure modes when you try to add it later: hardcoded strings embedded in component logic, dates formatted as strings before passing to display, number formatting assumptions baked into business logic, CSS with physical properties that need to be audited.

Retrofitting i18n onto a large application is expensive. The cost of building it in from the start — using translation functions from day one, using `Intl` for dates and numbers, using logical CSS properties — is small compared to the retrofitting cost, especially if you ever expect to support a language with different text direction.

---

*Next: How Teams Scaled Frontend. When one developer worked on the frontend, coordination was trivial. When ten did, process became necessary. The evolution of monorepos, design systems, and micro-frontends reflects teams learning what breaks at scale.*
