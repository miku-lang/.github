# Miku lang

*(the language, and accompanying compiler, are a work in progress, things can and will change, be added, removed, etc.)*

## General syntax

Each file starts with `remix`es (i.e. "imports"), then the declaration of one song.

A song can contains fields, `melody` (i.e. "methods") and `verse` (i.e. "static functions") and the available types are:
- `note`: an integer number
- `pitch`: a floating-point number
- `beat`: a boolean
- `lyric`: a string
- `track`: an array (of a unique unspecified type until compile-tile)
- `harmony`: a bag of fields defined at compile time (similar to C structure)
- `song`: a class (can have fields and methods)

To declare a variable, use the word `let`, followed by its name, an optional colon and type, and an optional equal sign and initial value. (It MUST have one of the type or initializer, you can't omit both).
```rs
let x = 42
let y: note
let z : note = 42
```

Inside a `melody` you can access the current object with the variable `song`, and return a value with `crescendo`.

The main file of a project must contain a `"main" verse` with this signature: `verse main(args: Track) -> note`. (similar to Java)

## Code example

```rs
remix moduleA
remix { funcA, funcB as funcC } from moduleB

song TripleBaka {
  melody miku() -> lyric {
    crescendo "Anata no kokoro wo bakkyunkyun!!"
  }

  melody teto() -> harmony {
    crescendo { "name": "Kasane Teto", "age": 31, "baka": true }
  }

  melody baka() {
    sing song.miku()
    let chimera = song.teto()
    sing chimera.name, " is ", chimera.age, " years old"
  }

  verse main(args: Track) -> note {
    let bakas = play TripleBaka
    bakas.baka()
    crescendo 0
  }
}
```

## Harmony

`harmony` is a *generic* type for struct-like objects. But the real type is inferred at compile-time, therefore it is similar to a template (or `auto`) parameter in C++.

```rs
let x = { "name": "euro", "value": 13 }
let y = { "name": "dollar", "value": 12 }
let z = { "name": "yen", "value": 12.5 }
let w = { "source": "Earth", "value": 16 }
```

`x` and `y` have the same internal type (`lyric` + `note`) while `z` has the type (`lyric` + `pitch`). \
Even though, `w` is also of type (`lyric` + `note`), since it has different field names, it is a different type from `x` and `y`.

```rs
verse get_value(data: harmony) -> note {
    crescendo data.value
}
```
Calling `get_value(x)`, `get_value(y)` or `get_value(w)` will compile since all three have a field named `value` of type `note`. While `has_coins(z)` will not since its `value` field is of type `pitch`.

## Track

Like `harmony`, it's a *generic* type but for arrays, so a unique type for all its elements.

```rs
let x = [1, 2, 3, 4, 5] // array of note
let y = ["Miku", "Teto", "Len", "Rin", "Neru"] // array of lyric
let z = [12, 1.5, "test"] // this won't compile
```

You can get the size of an array with `melody len() -> note` and any elements with `melody [](index: note) -> X`.

```rs
verse get_first_line(lines: track) -> note {
  if lines.len() > 0 drops {
    return lines[0]
  } else {
    return 0
  }
}
```
You can call this function with a `track` of `note`, but not with a `track` of `lyric`.

## Song

A class-like object. You can create one with `play <song title>`, and call its methods with `<instance>.<method>(args...)`.
They are freed at scope exit (maybe a "destructor"-like method and a way to delete them early, à la `delete <instance>`, will be added later)

You can inherit a song with `cover of`. The cover has access to the melodies of its model, but none of its verse nor none of its variables. All melodies can be overridden.

```rs
remix IevanPolkka

song MikuIevanPolkka cover of IevanPolkka {
  // https://www.youtube.com/watch?v=6qmUTclZhUo
}
```

## Printing

Use the keyword `sing` followed by a comma-separated list of values. (adds a newline at the end)

## Loops

You can repeat a loop a number of times:
```rs
repeat <expression> times {
  // body
}
```

You can repeat a loop while a condition is met:
```rs
while <condition> sounds {
  // body
}
```

You can countdown in a loop:
```rs
countdown <variable> from <expression> {
  // "variable" contains the decreasing value
}
```

You can iterate a track:
```rs
for i in [1, 2, 69, 42, 13] {
  sing i // will print each value of the array on a new line
}
```

You can iterate an harmony:
```rs
for i in { "name": "Kagamine", "surname": "Len", "twin": true } {
  // "i" is an harmony that has 2 fields
  // - key: that will contain the name of the key (always a string)
  // - value: that will contains the value for the key
  sing "My ", i.key, " is ", i.value
  // will print (order is unspecified) :
  // My name is Kagamine
  // My surname is Len
  // My twin is true
}
```

## Conditional

It's very similar to other languages, just that it has the `drops` keyword after the condition.

```rs
if y.x > 42 drops {
  y.x = 69
} else if y.x < 45 drops {
  y.x = 0
} else {
  y.x = 1337
}
```

## Incrementation/Decrementation of notes

Instead of doing the usual:
```rs
let x = 42
x += 13
x -= 1337
```
You `tune` them:
```rs
let x = 42
tune x up 13
tune x down 1337
```

## Remixes

At the moment, only `remix <name>` is available and expects a file called `<name>.miku` containing a song called `<name>`.
