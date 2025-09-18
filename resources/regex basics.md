---
tags:
  - regex
  - programming
---
📝 Regex Cheatsheet (Easy AF)

Regex looks scary, but here’s the **bare minimum you need**.

---

## Anchors
- ^ → Start of string
  Example: ^Hello
    ✅ "Hello world"
    ❌ "Say Hello"

- $ → End of string
  Example: world$
    ✅ "Hello world"
    ❌ "world peace"

---

## Character Classes
- \d → digit (0-9)
  Example: \d\d
    ✅ "42" in "Room 42"

- \w → word character (letters, numbers, underscore)
  Example: \w+
    ✅ "hello_123"

- . → any single character (except newline)
  Example: a.c
    ✅ "abc", "axc", "a9c"

---

## Quantifiers
- + → one or more
  Example: \d+
    ✅ "12345"

- * → zero or more
  Example: ba*
    ✅ "b", "ba", "baaa"

- ? → zero or one (optional)
  Example: colou?r
    ✅ "color"
    ✅ "colour"

- {n} → exactly n times
  Example: \d{3}
    ✅ "123"

---

## Sets
- [abc] → one of a, b, or c
  Example: b[aeiou]t
    ✅ "bat", "bet", "bit"

- [0-9] → any digit
  Same as \d

- [^abc] → NOT a, b, or c
  Example: [^0-9] → not a digit

---

## Groups
- (abc) → group
  Example: (ha)+
    ✅ "ha", "hahaha"

- (a|b) → a OR b
  Example: cat|dog
    ✅ "cat"
    ✅ "dog"

---

## Quick Combos
- ^\d{3}$ → exactly 3 digits
    ✅ "123"
    ❌ "12a"
    ❌ "1234"

- ^\w+$ → only letters/numbers/underscore, no spaces
    ✅ "hello_world42"
    ❌ "hello world!"

- ^.+$ → any non-empty string
    ✅ "Hello"
    ❌ ""

---

🎯 Real-World Templates

## Phone Number
^\(\+\d{2}\) \d{3}-\d{3}-\d{3}$
Format: (+34) 659-771-594
  ✅ (+34) 659-771-594
  ❌ 659-771-594

## Email
^[\w.-]+@[\w.-]+\.\w{2,}$
  ✅ test@example.com
  ✅ first.last@domain.co.uk
  ❌ test@domain

## URL
https?:\/\/[^\s]+
Matches http or https URLs
  ✅ http://example.com
  ✅ https://google.com/search?q=test
  ❌ ftp://example.com

---

⚡ Tips
- Always read regex like a sentence: "^ start, then digits, then $ end".
- Use online testers like regex101.com to practice.
- Don’t try to memorize everything. Just keep this cheatsheet handy 😉.
## Flags (Modifiers)

Flags change **how** the regex works. They go **after the last `/`**.

- `g` → global  
  - Find **all matches**, not just the first  
  - Example: `/\d/g` on `"Room 42 and 99"`  
    - ✅ matches "4", "2", "9", "9"  

- `i` → ignore case  
  - Makes regex **case-insensitive**  
  - Example: `/hello/i`  
    - ✅ matches "Hello", "HELLO", "hElLo"  

- `m` → multiline  
  - `^` and `$` match **start/end of each line**, not just the whole string  
  - Example: `/^foo/m` on `"foo\nbar\nfoo"`  
    - ✅ matches both "foo" lines  

- `s` → dotall  
  - `.` also matches **newline characters**  
  - Example: `/a.b/s` on `"a\nb"`  
    - ✅ matches across lines  

- `u` → unicode  
  - Treat pattern as full Unicode (needed for emojis, foreign characters)  
  - Example: `/\p{L}+/u` → matches any Unicode letters  

- `y` → sticky  
  - Matches **from the last index only** (rarely needed, advanced usage)  

---

⚡ Quick memory trick:  
- `g` = global (all)  
- `i` = ignore case  
- `m` = multiline  
- `s` = single-line (dotall)  
- `u` = unicode  
- `y` = "you stay here" (sticky)  
