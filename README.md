# kord
kord is a python framework that provides programmers with a simple api for the creation of music-based applications. While it focuses on theoretical purposes, some of it's more visually oriented features are tuned for the generation of tablature-style notation as such for guitars, basses, ukuleles and other similar plucked-string instruments.

<hr/>

# api reference:
## kord.notes module:

### Note() class
Note instances are the lowest-level objects of the framework and have 3 main attributes:

* (str) chr
* (str) alt
* (int) oct

Only the `chr` argument is required to create an instance. Arguments `alt` and `oct` will default to `''` and `3` respectively.

```
>>> from kord.notes import Note
>>> e3 = Note('e')
>>> e3
E³
>>> Note('B', 'b', 7)
B♭⁷
>>> Note('C', '#', 0)
C♯⁰
```

Notes with double alterations are supported but Notes with triple (or more) alterations raise InvalidAlteration Exceptions:

```
>>> n3 = Note('A', 'bb', 1)
>>> n3
A𝄫¹
>>> n4 = Note('F', '##', 1)
>>> n4
F𝄪¹
>>> Note('G', '###')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  ...
kord.errors.InvalidAlteration: ###
```



Intervals between note objects can be evaluated using the following operators: ``` - < > << >> <= >= != ``` This allows calculations of semitones deltas notes dis as well as study of their enharmonic relationships. Let's take a quick look at each operator separately:

#### <  > operators


```
>>> f3 = Note('F')
>>> f3
F³
>>> f3 > e3
True
```

#### != operator


```
>>> n1 = Note('F', '#', 5)
>>> n2 = Note('G', 'b', 5)
>>> n1, n2
(F♯⁵, G♭⁵)
>>> n1 != n2
False
```


Given that python lacks a `===` operator, Notes can be compared for a "stricter" equality using their `is_a()` method:

```
>>> n3 == n4
True
>>> n3.is_a(n4)
False
```

This method directly compares Note attributes instead of their semitone interval. Set the `ignore_oct` argument appropriately for a less strict comparison:

```
>>> n1
F♯⁵
>>> n1.is_a(Note('F', '#', 3))
False
>>> n1.is_a(Note('F', '#', 3), ignore_oct=False)
True
```

<hr/>

## kord.keys module:


### class TonalKey(object):

The best way to describe a TonalKey object is basically as a Note object generator. You can use this class to create any theoretical arrangement of musical notes (ie. chords, scales).

Let us take a look at 2 examples using the 2 main categories of child class that inherit from TonalKey class and how they are defined:


### class ChromaticKey(TonalKey):

The ChromaticKey class uses the TonalKey class as an interface while implementing it's own structure of intervals.

```
class ChromaticKey(TonalKey):

    root_intervals = (
        UNISON,
        MINOR_SECOND,
        MAJOR_SECOND,
        MINOR_THIRD,
        MAJOR_THIRD,
        PERFECT_FOURTH,
        AUGMENTED_FOURTH,
        PERFECT_FIFTH,
        MINOR_SIXTH,
        MAJOR_SIXTH,
        MINOR_SEVENTH,
        MAJOR_SEVENTH,
    )
```

We can initialize ChromaticKey objects on any given note and use the ```degree()``` method to obtain one of it's degrees. Using list index notation will achieve a similar result:

```
>>> from kord.keys import ChromaticKey
>>> c_chromatic = ChromaticKey('C')
>>> c_chromatic.degree(2)
C♯⁰
>>> c_chromatic[12]
B⁰
```

Perhaps the most interesting aspect of any TonalKey sub-class is it's ability to iterate over Note objects using one of their several generator methods. As an example, let's take a quick look at the ```scale()``` method:

```
>>> for note in c_chromatic.scale()
...   print(note, end=' ')
...
C⁰ C♯⁰ D⁰ D♯⁰ E⁰ F⁰ F♯⁰ G⁰ G♯⁰ A⁰ A♯⁰ B⁰ C¹ 
```

We can use the ```note_count=``` argument to specify to the scale generator the amount of notes to yield:

```
>>> for note in c_chromatic.scale(note_count=4):
...   print(note, end=' ')
...
C⁰ C♯⁰ D⁰ D♯⁰ 
```

The ```start_note=``` argument can be used to to start yielding from a specific note. This can be done even if the note is not part of the scale:

```
>>> from kord.notes import Note
>>> Ab = Note('A', 'b', 0)
>>> for note in c_chromatic.scale(note_count=6, start_note=Ab):
...   print(note, end=' ')
...
G♯⁰ A⁰ A♯⁰ B⁰ C¹ C♯¹ 
```



### class DiatonicKey(TonalKey):

```
class MajorKey(DiatonicKey):

    root_intervals = (
        UNISON,
        MAJOR_SECOND,
        MAJOR_THIRD,
        PERFECT_FOURTH,
        PERFECT_FIFTH,
        MAJOR_SIXTH,
        MAJOR_SEVENTH,
    )
```




### Arguments

* -h --help  

* -f --frets [int] 
* -v --verbose [int] 

