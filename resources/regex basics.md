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
