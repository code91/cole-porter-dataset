# Cole Porter dataset

Lyrics aligned to notated melody, at the syllable, for songs where **Cole Porter
wrote both the words and the music**.

Each row is one note, carrying the syllable sung on it, the chord sounding at
its onset, its pitch and its duration, in absolute terms and in key-invariant
ones. Chord corpora are plentiful and lyric corpora are plentiful; lyrics
aligned to *notated* melody at the syllable are not.

```
song,bar,midi,pitch_name,note_type,duration_q,syllable,melisma,chord_root,chord_quality,pc_rel_tonic,chord_root_rel,pc_rel_chord
Night and Day,2,69,A4,quarter,1.0,"beat,",False,D,dim,7,1,6
```

| | |
|---|---|
| songs | 21 |
| note events | 2,894 |
| syllable-to-note attachment points | 2,504 |
| melisma rate | 13.5% |

Every song is by Cole Porter, words and music, bar one that is marked. Transcribed from engraved
editions of each song. The source scores are not
redistributed here.

## Songs

| song | key | verse | bars | note events | attachment points |
|---|---|:-:|---:|---:|---:|
| All of You | Db | none known | 33 | 79 | 76 |
| Anything Goes | C | yes | 47 | 196 | 167 |
| At Long Last Love | Bb | none known | 33 | 97 | 85 |
| Don't Fence Me In \* | F | none known | 33 | 157 | 149 |
| Dream Dancing | Bb | missing | 54 | 127 | 109 |
| Easy To Love | Eb | yes | 48 | 136 | 124 |
| Every Time We Say Goodbye | Eb | none known | 38 | 128 | 110 |
| Get Out Of Town | G minor | missing | 34 | 118 | 87 |
| I Concentrate On You | C | none known | 72 | 163 | 141 |
| I've Got You Under My Skin | Eb | none known | 63 | 200 | 174 |
| In the Still of the Night | F | none known | 77 | 124 | 100 |
| It's All Right With Me | C | none known | 61 | 146 | 124 |
| Just One of Those Things | F | none known | 63 | 146 | 120 |
| Love For Sale | Bb minor | yes | 84 | 213 | 196 |
| Night and Day | D | yes | 64 | 233 | 196 |
| Too Darn Hot | C minor | none known | 22 | 79 | 64 |
| Well, Did You Evah! | F | none known | 34 | 102 | 102 |
| What Is This Thing Called Love | A | yes | 49 | 131 | 117 |
| You Do Something To Me | Eb | missing | 32 | 64 | 60 |
| You'd Be So Nice To Come Home To | F minor | none known | 30 | 76 | 57 |
| You're The Top | F | yes | 51 | 179 | 146 |
| **21 songs** | | | **1022** | **2,894** | **2,504** |

Where a song has a verse it is part of the same song, with bars running
continuously from the verse into the chorus. "missing" means a verse exists in
another edition and is not yet here; "none known" means no edition consulted
prints one.

\* Porter wrote the music; the lyric is adapted from a poem by Robert Fletcher.
It is the one song here not wholly his, and `manifest.csv` carries
`porter_words_and_music` so it can be filtered out.

## The unit of analysis

The atom is the **syllable-to-note attachment point**, with the chord sounding
at onset, not the word.

In sung music one word routinely spans several notes and crosses a chord
change: *love* held four beats through a ii-V. Collapsing that to a single
"word" makes every downstream result an artefact of how multi-note words were
merged. So every note is its own row, and a word held over *k* notes appears as
one attachment plus *k*−1 melisma continuations, flagged in `melisma` rather
than collapsed. Collapsing is a downstream choice and is left downstream.

The 15.5% melisma rate is how much of the data a word-level unit would have had
to invent a rule for.

## Columns

**Identity**

| | |
|---|---|
| `song` | title |
| `bar` | bar number, counting the anacrusis as bar 1 |
| `page`, `system`, `x` | position in the engraved source |

**Pitch**: `midi`, `pitch_name` (e.g. `Bb4`), `dia` (diatonic index, C4 = 0),
`alter` (semitone alteration applied)

**Rhythm**, `note_type` (`whole`…`32nd`), `dots`, `tuplet`, `duration_q`
(duration in quarter notes, dots and tuplets applied)

**Words**: `syllable` (empty on a melisma continuation), `word_start` (false
when this syllable continues the previous word), `hyphen_after`, `melisma`

**Harmony**: `chord_raw` (symbol as printed), `chord_root`, `chord_quality`

**Key-invariant views**: `pc_rel_tonic` (pitch class above the tonic, 0-11),
`chord_root_rel` (chord root above the tonic), `pc_rel_chord` (pitch class above
the chord root)

The absolute and relative columns are both present so an analysis can use
either.

**The key is declared, not inferred.** Each edition states its key and that is
what `manifest.csv` records; `reference_key` names the key of an independent
edition where one was found, which is the target for anyone transposing the set
to a single key. Estimating the key from the music is unreliable in this
repertoire: a chart that closes on a turnaround makes the last chord the
dominant, and ii-V is frequent enough that weighting chords by duration elects
the ii instead of the tonic. Both failure modes occur here.

## Files

| | |
|---|---|
| `dataset/attachments.csv` | one row per note event, all songs |
| `dataset/manifest.csv` | per-song key, mode, bar and event counts |
| `dataset/<song>.json` | the same rows, per song |
| `dataset/features.json` | aggregate distributions over chord quality, scale degree, relative chord root and duration |

## Notes on the data

- **42 distinct chord-quality strings.** The symbol is kept exactly as printed
  rather than normalised to a taxonomy, so that choice stays with whoever makes
  it.
- **Verse 2 is not captured.** Where a chart prints a second stanza under the
  first, only the first is included.
- **Section boundaries are not marked.** Verse and chorus often differ in mode,
  and the tonic is song-level, so a minor-mode verse carries degrees relative to
  a major-mode tonic.
- Hyphen side-assignment occasionally slips by one (`un der-` for `un- der`).
  Syllable boundaries are unaffected; word reconstruction is.
- **Syllables are recorded as printed, typos included.** One edition sets "a
  thing coul be" and "could ec-er care"; the engraving really says that, and
  silently correcting it would make the data disagree with its source.
- A word engraved with no space glyph between it and the next comes through
  joined (`cioustime`, `fireburn ing`). The space is absent from the file, not
  dropped in reading.

## Licence

The MIT licence covers any code here. It does not and cannot grant rights in
Cole Porter's songs, which remain in copyright. These rows are published as
research data: counts, features and alignments for text and data mining. They are not a substitute for the works. If you hold rights in this material and
want something removed, open an issue and it will be taken down.
