# Regular Expressions

## Learning Objectives

- What are Regular Expressions?
- Why are they incredibly useful?
- How does RegEx matching work?
- How does RegEx replacement work?

## What are Regular Expressions?

Regular Expressions provide text searching and replacement capabilities, similar to using the "Find & Replace" (F&R) operation in a word processor. However, most common F&Rs only search for a literal sequence...

**Find literal: `"eat"`**

> I will `eat` with you on Tuesday at the local `eat`ery, Joe's Eats.


Regular Expressions (RegEx) are considerably more powerful because they search for _patterns_ in text, which allows for contextual awareness...

**Find pattern: `/eats?\b/ig`**
- Find `"eat"` as a standalone word (surrounded by spaces and/or punctuation).
- Allow the standalone word to be plural (may end in "s").
- Allow the standalone word to have both upper and lowercase letters.

> I will `eat` with you on Tuesday at the local eatery, Joe's `Eats`.


## Why are Regular Expressions awesome?

Regular Expressions allow for an unprecidented level of text manipulation that would be extremely difficult (or virtually impossible) to achieve with literal sequences alone. Regexs are particularily useful in the realm of programming, where we frequently need to read specially-formatted text strings as data.

**Find the text of the header tag:**
```html
<div id="widget">
  <h1 class="tricky">Get this headline text!</h1>
  <p>Not this paragraph</p>
</div>
```

**Okay, here's a RegEx:**
```javascript
/<h1[^>]*>(.*?)<\/h1>/g
```

Modern regular expressions were introduced with the Perl language during the 1980's, and are notorious for their daunting illegibility.

```javascript
/(\d{1,3}[\.-]?)?\d{3}[\.-]?\d{3}[\.-]?\d{4}/g
```

Don't panic! With time and practice, your eyes will train to read regular expressions... and you'll discover that they are an invaluable tool.

--

## Let's Get Started!

Go to [http://www.regexr.com](http://www.regexr.com). This is a handy online Regular Expressions workbench, perfect for learning, testing, and refining RegEx patterns. Workbench tools are your friends!

Copy and paste the following text corpus into RegExr:

```
The Battle of the Bottles

99 bottles of beer on the wall, 99 bottles of beer.
Take a few down, pass them around... 1 bottle of beer on the wall.
```

## Basic Matchers

A basic RegEx is formatted as a text pattern wrapped in `/` delimiters:

```javascript
/bottle/
```

### Search Flags

A RegEx may include one or more _search flags_ at the end to govern how the pattern is searched:

```javascript
// Find all occurances (globally) of the word "bottle":
/bottle/g
```

* `g` Global search (matches all occurances of the pattern).
* `i` Case-Insensitive (matches upper and lowercase letters with indifference).
* `m` Multiline (matches the pattern across line breaks in the text).

### Alternation

A RegEx may match against two or more alternative patterns:

```javascript
// Find all occurances of the words "bottle", "battle", and/or "beer"
/bottle|battle|beer/g
```

* `a|b` Match "a" and/or "b".

### Reserved Characters

RegExs reserve several characters for their own operations. The following characters are reserved by RegEx:

```
^ $ * + . ( ) [ ] { } | /
```

To include a reserved character in a search pattern, you must escape it using `\` (forward slash):

```javascript
// "Costs $100 (USD)"
// escaped for RegEx:
/Costs \$100 \(USD\)/
```

### Character Sets

RegExs provide several built-in character classifications for matching common text patterns.

* `.`  Any single character (wild card).
* `\s` Any space character.
* `\S` Any non-space character.
* `\d` Any digit (numbers 0-9).
* `\D` Any non-digit (anything BUT numbers 0-9).
* `\w` Any word character (matches letters, numbers, and underscore).
* `\W` Any non-word character (anything BUT letters, numbers, and underscore).
* `\b` Any word boundary (separators including spaces, commas, and periods).

### Classes

Custom classes allow you to build your own character sets.

* `[abc]` Positive character class (matches "a", "b", or "c").
* `[^abc]` Negative character class (matches anything _except_ "a", "b", or "c").
* `[a-z]` Character range (matches all lowercase letters from A to Z).
* `[a-zA-Z]` Multiple ranges (matches all uppercase and lowercase letters from A to Z).

### Anchors

These flags anchor a search pattern to the start or end of a line of text. Extremely useful!

* `^` Start of line (ex: `/^hello/` matches "hello world" but not "say hello").
* `$` End of line (ex: `/hello$/` matches "say hello" but not "hello world").

### Repetitions

These flags match a pattern repeatedly, turning a single character match into many.

* `?` Match preceding pattern zero or one times (ex: `/cars?/` will match `"car"` or `"cars"`).
* `*` Match preceding pattern zero or more times (ex: `/sou*p/` will match `"sop"`, `"soup"`, or `"souuuup"`).
* `+` Match preceding pattern one or more times (ex: `/zo+m/` will match `"zom"`, `"zoom"`, or `"zoooooooom"`).
* `{3}` Matches preceding pattern exactly N times (ex: `/bo{3}m/` will match `"booom"`).
* `{1,3}` Matches preceding between N and M times (ex: `/bo{1,3}m/` will match `"bom"`, `"boom"`, or `"booom"`).

One of the most common repitions you'll see used in RegEx is the universal matcher pattern: `.*`. The dot-star pattern matches any character zero or more times... thus it will match literally _anything_!

### Greedy vs. Lazy Repetition

All repetitions are _greedy_ by default, meaning they will match as many characters as possible before stopping. For example, let's try to match all texts in parenthesis in the following example:

**Find _greedy_ pattern: `/\(.+\)/g`**
> Greater DC includes Maryland `(MD) and Virginia (NOVA)`."

The results are unexpected. Our RegEx matches from the first opening parenthesis all the way through to the final closing parenthesis. This is because the RegEx has greedily matched as many characters as possible while fulfilling the repetition pattern. Actually though, we'd like the pattern to match as _few_ characters as possible so that we capture each set of open/close parenthesis individually. To do that, we need _lazy repetition_. These flags match a pattern repeatedly as few times as possible:

* `*?` Match zero or more of the preceding pattern, as few times as possible.
* `+?` Match one or more of the preceding pattern, as few times as possible.

**Find _lazy_ pattern: `/\(.+?\)/g`**
> Greater DC includes Maryland `(MD)` and Virginia `(NOVA)`."

Now for a fancy trick... Lazy repetition is slower than greediness because the RegEx algorithm must perform redundant work. However, we can frequently achieve the same results as a lazy search by using greedy repetition with a negative matcher. This trick works by greedily searching for anything that is NOT our endpoint:

* `[^x]*` Greedy repetition with a negative matcher (repeatedly find anything that is NOT `x`).

**Find _greedy-negative_ pattern: `/\([^\)]+\)/g`**
> Greater DC includes Maryland `(MD)` and Virginia `(NOVA)`."

## Groups

Groups are where RegEx starts getting REALLY powerful. With groupings, we can write patterns inside of patterns. This makes individual RegEx operators even more powerful when they can operate on larger patterns.

* `(...)` Parenthesis establishes a new group.

**Match both URLs: `/(http:)?\/\/regexr\.com/`** 
```
http://regexr.com
//regex.com
```

## Matching Exercises

Copy and paste the each of the following texts into RegExr, and enable the `global` and `multiline` flags. Then write a regular expression that matches all lines of the text.

**Match phone numbers:**
```
1-555-555-5555
555-555-5555
555.555.5555
5555555555
```

**Match URLs:**
```
http://regexr.com
https://regexr.com
//regexr.com
```

**Match HTML Tags, capturing their text content in a group:**
```
<p>hello world</p>
<p id="tricky">hello world</p>
<p id="tricky" class="tricky">hello world</p>
```

## Replacement

So far, we've only done finding. RegEx becomes far more powerful when applied to dynamic replacement. Let's write a RegEx that finds all standalone instances of the word "wood", and swaps them for "ham":

```
How much wood would a woodchuck chuck if a woodchuck could chuck wood?
```
