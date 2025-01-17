# Instruction

- `<>` means specific object
    - `<Up>` means 'up' key on keyboard
    - `<Ctrl-I>` means 'ctrl' key plus 'i' or 'I' key
    - `<command>` means command
    - `<text>` means text content
- `[]` means optional
    - `[<N>]` means optional count, default is 1
- other characters in `code block` means corresponding keys on keyboard

|Mode Abbreviation  |Mode               |
|:---               |:---               |
|N                  |Normal             |
|C                  |Command            |
|I                  |Insert             |
|VB                 |Block Visual       |
|VL                 |Line Visual        |
|R                  |Replace            |
|X                  |Complete           |
|RR                 |Register Record    |
|PS                 |Pattern Substitute |


# General Commands

|Command                |Mode               |Description                                        |
|:---                   |:---               |:---                                               |
|`:`                    |N $\rightarrow$ C  |begin to enter command                             |
|`help [<stuff>]`       |C $\rightarrow$ N  |open help file of `<stuff>`                        |
|`<Esc>`                |$\rightarrow$ N    |enter normal mode                                  |
|`<command>`            |C $\rightarrow$ N  |command in command line                            |
|`!<command>`           |C $\rightarrow$ N  |execute external command, e.g., compile c file     |
|`<Up>`                 |C $\rightarrow$ N  |last command in command line                       |
|`<Ctrl-]>`             |N                  |jump into link                                     |
|`<Ctrl-O>`             |N                  |jump back                                          |
|`<Ctrl-I>`             |N                  |jump forward                                       |
|`w[q]`                 |C $\rightarrow$ N  |save [and exit vim]                                |
|`sa[veas] <file_path>` |C $\rightarrow$ N  |save as `<file_path>`                              |
|`q!`                   |C $\rightarrow$ N  |force exit without save                            |


# Cursor Moving Commands

|Command        |Mode               |Description                                    |
|:---           |:---               |:---                                           |
|`[<N>]h`       |N                  |left character                                 |
|`[<N>]l`       |N                  |right character                                |
|`[<N>]k`       |N                  |up lines                                       |
|`[<N>]j`       |N                  |down lines                                     |
|`[<N>]w`       |N                  |forward to word start                          |
|`[<N>]W`       |N                  |forward to white spaces separated word start   |
|`[<N>]b`       |N                  |backward to word start                         |
|`[<N>]B`       |N                  |backward to white spaces separated word start  |
|`[<N>]e`       |N                  |forward to word end                            |
|`[<N>]E`       |N                  |forward to white spaces separated word end     |
|`[<N>]f<char>` |N                  |to the `<char>` to the right                   |
|`[<N>]F<char>` |N                  |to the `<char>` to the left                    |
|`^`            |N                  |to first non-blank character in the line       |
|`0`            |N                  |to first character in the line                 |
|`[<N>]$`       |N                  |to last character in the (N-1) below line      |
|`gg`           |N                  |to the first line                              |
|`G`            |N                  |to the last line                               |
|`<N>`          |C $\rightarrow$ N  |to line `<N>`                                  |


# Mark Cursor Commands

|Command                |Mode               |Description                                            |
|:---                   |:---               |:---                                                   |
|`m<mark>`              |N                  |set a named mark                                       |
|`'<mark>`              |N                  |jump to named mark, "'" could be replaced with "\`"    |
|`marks`                |C $\rightarrow$ N  |list jump marks                                        |
|`delm[arks] <mark>`    |C $\rightarrow$ N  |list jump marks                                        |

**Mark "a-z" is valid within one file, while "A-Z" is valid between files.**


# Text Objects

|Command        |Description                                                    |
|:---           |:---                                                           |
|`aw`           |the word from cursor to the end                                |
|`iw`           |the word where cursor is inner                                 |
|`aW`           |the string separated by white space from cursor to the end     |
|`iW`           |the string separated by white space where cursor is inner      |
|`as`           |the sentence from cursor to the end                            |
|`is`           |the sentence where cursor is inner                             |
|`ap`           |the paragraph from cursor to the end                           |
|`ip`           |the paragraph where cursor is inner                            |
|`a[` or `a]`   |the "[]" block from cursor to the end                          |
|`i[` or `i]`   |the "[]" block where cursor is  inner                          |
|`a(` or `a)`   |the "()" block from cursor to the end                          |
|`i(` or `i)`   |the "()" block where cursor is inner                           |
|`a<` or `a>`   |the "<>" block from cursor to the end                          |
|`i<` or `i>`   |the "<>" block where cursor is inner                           |
|`a{` or `a}`   |the "{}" block from cursor to the end                          |
|`i{` or `i}`   |the "{}" block where cursor is inner                           |
|`a"` or `a'`   |the quoted string from cursor to the end                       |
|`i"` or `i'`   |the quoted string block where cursor is inner                  |


# Editing Commands

|Command                |Mode               |Description                                                        |
|:---                   |:---               |:---                                                               |
|`[<N>]i<text>`         |N $\rightarrow$ I  |insert `<text>` before the cursor                                  |
|`[<N>]I<text>`         |N $\rightarrow$ I  |insert `<text>` before the first non-blank character               |
|`[<N>]a<text>`         |N $\rightarrow$ I  |append `<text>` after the cursor                                   |
|`[<N>]A<text>`         |N $\rightarrow$ I  |append `<text>` after the last character in the line               |
|`[<N>]o<text>`         |N $\rightarrow$ I  |open a new line and insert `<text>` below this line                |
|`[<N>]O<text>`         |N $\rightarrow$ I  |open a new line and insert `<text>` above this line                |
|`[<N>]r<char>`         |N                  |replace a character at and after the cursor with `<char>`          |
|`[<N>]R<text>`         |N $\rightarrow$ R  |replace with `<text>`                                              |
|`[<N>]u`               |N                  |undo the last modifications                                        |
|`[<N>]<Ctrl-R>`        |N                  |redo the last undone modifications                                 |
|`[<N>]x`               |N                  |delete characters at and after the cursor                          |
|`[<N>]c<text-object>`  |N $\rightarrow$ I  |change texts selected by `<text-object>`                           |
|`[<N>]c<movement>`     |N $\rightarrow$ I  |change texts selected by `<movement>`                              |
|`[<N>]cc`              |N $\rightarrow$ I  |change current line                                                |
|`[<N>]C`               |N $\rightarrow$ I  |change to the end of the line and (N-1) more lines and             |
|`[<N>]d<text-object>`  |N                  |delete texts selected by `<text-object>`                           |
|`[<N>]d<movement>`     |N                  |delete texts selected by `<movement>`                              |
|`[<N>]dd`              |N                  |delete current line                                                |
|`[<N>]D`               |N                  |delete to the end of the line                                      |
|`[<N>]y<text-object>`  |N                  |copy text selected by `<text-object>` into default register        |
|`[<N>]y<movement>`     |N                  |copy text selected by `<movement>` into default register           |
|`[<N>]yy`              |N                  |copy current line into default register                            |
|`[<N>].`               |N                  |repeat last change                                                 |
|`[<N>]>>`              |N                  |add 1 shiftwidth indent at heads                                   |
|`[<N>]<<`              |N                  |reduce 1 shiftwidth indent at heads                                |
|`[<N>]p`               |N                  |paste text in default register after the cursor                    |
|`[<N>]P`               |N                  |paste text in default register before the cursor                   |
|`[<N>]~`               |N                  |switch case for a character at and after the cursor                |
|`[<N>]<Ctrl-A>`        |N                  |add 1 to the number at or after the cursor                         |
|`[<N>]<Ctrl-X>`        |N                  |reduce 1 to the number at or after the cursor                      |


# Visual Mode and Relevant Commands

|Command        |Mode                   |Description                                                        |
|:--            |:---                   |:---                                                               |
|`v`            |N $\rightarrow$ V      |visual mode                                                        |
|`<Ctrl-V>`     |N $\rightarrow$ VB     |visual mode block wise                                             |
|`V`            |N $\rightarrow$ VL     |visual mode line wise                                              |
|`[N]I<text>`   |VB $\rightarrow$ I     |insert `<text>` before each selected line                          |
|`[N]A<text>`   |VB $\rightarrow$ I     |append `<text>` after each selected line                           |
|`x` or `d`     |V, VB, VL              |delete selected contents                                           |
|`r<char>`      |V, VB, VL              |replace selected contents with `<char>`                            |
|`c`            |VB $\rightarrow$ I     |change selected contents with same `<text>` for each selected line |
|`y`            |V, VB, VL              |copy selected contents                                             |


# Commands in Insert Mode

|Command                    |Mode       |Description                                                                            |
|:--                        |:---       |:---                                                                                   |
|`<Up>`                     |I          |move cursor up                                                                         |
|`<Down>`                   |I          |move cursor down                                                                       |
|`<Left>`                   |I          |move cursor left                                                                       |
|`<Right>`                  |I          |move cursor right                                                                      |
|`<Ctrl-R><reg>`            |I          |insert texts in the `<reg>` register, e.g., `<Ctrl-R>%` insert current file path       |
|`<Ctrl-R><Ctrl-R><reg>`    |I          |insert raw texts in the `<reg>` register                                               |
|`<Ctrl-V><key>`            |I          |insert original meanings of keyboard keys, e.g., `<Ctrl-V><Delete>` insert '\<Del\>'   |
|`<Ctrl-Y>`                 |I          |insert character above the cursor                                                      |
|`<Ctrl-E>`                 |I          |insert character below the cursor                                                      |
|`<Ctrl-T>`                 |I          |add 1 shiftwidth indent at head of the line                                            |
|`<Ctrl-D>`                 |I          |reduce 1 shiftwidth indent at head of the line                                         |


# Vim Completion Mode Commands

|Command        |Mode                   |Description                                                    |
|:--            |:---                   |:---                                                           |
|`<Ctrl-P>`     |I, R $\rightarrow$ X   |insert previous match of identifier before the cursor          |
|`<Ctrl-N>`     |I, R $\rightarrow$ X   |insert next match of identifier before the cursor              |
|`<Ctrl-X>`     |I, R $\rightarrow$ X   |open completion selections                                     |
|`<Ctrl-Y>`     |X $\rightarrow$ I, R   |determine match item and return to original mode               |
|`<Ctrl-E>`     |X $\rightarrow$ I, R   |reserve original texts and return to original mode             |
|`<Ctrl-F>`     |X                      |file name completion                                           |
|`<Ctrl-P>`     |X                      |keyword local completion; previous match item while selecting  |
|`<Ctrl-N>`     |X                      |keyword local completion; next match item while selecting      |


# Register Commands

|Command                |Mode               |Description                                                |
|:--                    |:---               |:---                                                       |
|`reg[ister]`           |C $\rightarrow$ N  |show contents in all registers                             |
|`reg[ister]<reg>`      |C $\rightarrow$ N  |show contents in register `<reg>`                          |
|`"<reg>`               |N                  |set register `<reg>` for next delete, copy or paste        |
|`q<a-z>` or `q<A-Z>`   |N                  |start to record keyboard input into register `<a-z>`       |
|`q`                    |N                  |stop register record                                       |
|`[N]@<a-z>`            |N                  |execute contents in register `<a-z>` like keyboard input   |

Use command **`:help registers`** in vim to view descriptions for all registers.


# Regular Expression Pattern

A `<pattern>` consists of one or multiple `<branch>` separated by `\|`:
```
<pattern>   ::= <branch>
            OR <branch> \| <branch> \| <branch>
```
A `<pattern>` matches strings satisfying any one of the `<branch>`.
If a string matches more than one `<branch>`,
the first `<branch>` will be selected.

A `<branch>` consists of one or multiple `<concat>` separated by `\&`:
```
<branch>    ::= <concat>
            OR <concat> \& <concat> \& <concat>
```
A `<branch>` matches strings satisfying all the `<concat>`.

A `<concat>` consists of one or multiple adjacent `<piece>`:
```
<concat>    ::= <piece>
            OR <piece><piece><piece>
```
A `<concat>` matches the `<piece>` sequentially

A `<piece>` consist of an `<atom>` or an `<atom>` and a `<multi>`:
```
<piece> ::= <atom>
        OR <atom><multi>
```
A `<piece>` matches the `<atom>` one time or `<multi>` times.

A `<atom>` could be a `<ordinary-atom>` or a parenthetical `<pattern>`:
```
<atom>  ::= <ordinary-atom>
        OR \%(<pattern>\)
        OR \(<pattern>\)
        OR \z(<pattern>\)
```
A `<ordinary-atom>` matches a character in strings.
A `\%(<pattern>\)` is a normal `<pattern>`,
which could not be referenced.
A `\(<pattern>\)` could be referenced with `\n`,
by current regular expression,
where `n` is from 1 to 9.
A `\z(<pattern>\)` could be *externally* referenced with `\zn`
by future regular expression,
where `n` is from 1 to 9.


# Pattern Search Commands

|Command                        |Mode   |Description                                                    |
|:--                            |:---   |:---                                                           |
|`[<N>]/<pattern>[/<offset>]`   |N      |search forward with `<pattern>` and go \<offset\> lines down   |
|`[<N>]/[/<offset>]`            |N      |search forward with the last `<pattern>` and new \<offset\>    |
|`[<N>]?<pattern>[?<offset>]`   |N      |search backward with `<pattern>` and go \<offset\> lines down  |
|`[<N>]?[?<offset>]`            |N      |search backward with last `<pattern>` and new \<offset\>       |
|`[<N>]*`                       |N      |search forward with the identifier nearest to cursor           |
|`[<N>]#`                       |N      |search backward with the identifier nearest to cursor          |
|`[<N>]n`                       |N      |repeat last search                                             |
|`[<N>]N`                       |N      |repeat last search in opposite direction                       |

Use command **`:help pattern`** in vim to view available pattern expressions.


# Pattern Substitute Commands

```
:<range>s[ubstitute]/<pattern>/<string>/[<flag>]
```

Enter PS mode and substitute strings satisfying `<pattern>`
with `<string>` in `<range>` lines.
Replace operation is controlled by `<flag>`.

`<range>`:
`.`, current line;
`66`, line 66;
`78,+5`, line 78 to line 83;
`%` or `1,$`, whole file;
`<pattern 1>,<pattern 2>`, string satisfying \<pattern 1>\ to string satisfying \<pattern 2>\;
default, selected area or current line.

`<flag>`:
g, all matches in the same line, otherwise only the first match will be substituted;
c, need manual confirmation for each substitute;
i, ignore case for `<pattern>`;
I, don't ignore case for `<pattern>`.

Instead of `/`, other characters cloud be used as delimiter pattern substitute command.
EXCEPT alphanumeric characters, `\`, `"`, `|`, or `#`(for Vim9).

```
:<range>s[ubstitute] [<flag>]
```
or
```
:<range>&[&] [<flag>]
```

Repeat last substitute command with new [flag].

Use command `:help substitute` in vim to view more information.
Use command `:help pattern` in vim to view available pattern expressions.


# Buffer Commands

|Command                    |Mode               |Description                                                                        |
|:--                        |:---               |:---                                                                               |
|`buffers[!] [flags]`       |C $\rightarrow$ N  |list all files in buffer, `!`: include unlisted files                              |
|`<id>b[uffer][!]`          |N                  |move to file `<id>` in buffer, `!`: include unlisted files                         |
|`[<N>]bn[ext][!]`          |N                  |move to the next file, `!`: include unlisted files                                 |
|`[<N>]bp[revious][!]`      |N                  |move to the previous file, `!`: include unlisted files                             |
|`bf[irst]                  |N                  |move to the first file in buffer                                                   |
|`bl[ast]                   |N                  |move to the last file in buffer                                                    |
|`bd[elet][!] <id>`         |N                  |delete file `<id>` in buffer, `!`: delete with discarding unsaved modifications    |


# Window Commands

|Command                        |Mode   |Description                                                |
|:--                            |:---   |:---                                                       |
|`<Ctrl-W> (h \| j \| k \| l)`  |N      |move to the left, down, right, up, or right window         |
|`<Ctrl-W> (H \| J \| K \| L)`  |N      |move current window to the left, down, right, up, or right |
|`<Ctrl-W> T`                   |N      |move current window to a new tab page                      |
|`<Ctrl-W> =`                   |N      |resize all windows to as the same size as possible         |
|`<Ctrl-W> _`                   |N      |maximize current window height                             |
|`<Ctrl-W> \|`                  |N      |maximize current window width                              |
|`[<N>]<Ctrl-W> <`              |N      |decrease width with 1 to current window                    |
|`[<N>]<Ctrl-W> >`              |N      |increase width with 1 to current window                    |
|`[<N>]<Ctrl-W> -`              |N      |decrease height with 1 to current window                   |
|`[<N>]<Ctrl-W> +`              |N      |increase height with 1 to current window                   |



# Tab Page Commands

|Command                    |Mode               |Description                                                                    |
|:--                        |:---               |:---                                                                           |
|`tabs`                     |C $\rightarrow$ N  |show existing tab pages                                                        |
|`tabnew <file_path>`       |C $\rightarrow$ N  |open a tab page for an existing file or a new file                             |
|`gT`                       |N                  |switch to the previous tab page                                                |
|`gt`                       |N                  |switch to the next tab page                                                    |
|`tab[close]`               |C $\rightarrow$ N  |close current tab page                                                         |
|`tabo[nly]`                |C $\rightarrow$ N  |close all tab pages except current page                                        |
|`tabm[ove]`                |C $\rightarrow$ N  |move the position of current tab page in page bar, or use **mouse** to drag    |


# Fold Commands

|Command            |Mode       |Description                                |
|:--                |:---       |:---                                       |
|`zf`               |V, VB, VL  |create 1 fold                              |
|`zf<text-object>`  |N          |create 1 fold                              |
|`zd`               |N          |delete 1 fold at the cursor                |
|`zD`               |N          |delete all folds at the cursor             |
|`zE`               |N          |delete all folds in the window             |
|`zo`               |N          |open 1 fold at the cursor                  |
|`zO`               |N          |open all folds at the cursor recursively   |
|`zc`               |N          |close 1 fold at the cursor                 |
|`zC`               |N          |close all folds at the cursor recursively  |
|`zr`               |N          |reduce folding                             |
|`zR`               |N          |open all folds in current window           |
|`zm`               |N          |increase folding                           |
|`zM`               |N          |Close all folds in current window          |


# Spell Checking

Spell checking inside vim should be switched on manually.

The spellfile is the word list file where words are added for the `zg` and `zw` commands.
The internal-wordlist is as spellfile but only for all buffers in current session.

|Command            |Mode       |Description                                                    |
|:--                |:---       |:---                                                           |
|`[s`               |N          |move to next misspelled word                                   |
|`]s`               |N          |move to previous misspelled word                               |
|`zg`               |N, V       |add word as a good word to the spellfile                       |
|`zG`               |N, V       |add word as a good word to the internal-wordlist               |
|`zug`              |N, V       |undo `zg`                                                      |
|`zuG`              |N, V       |undo `zG`                                                      |
|`zw`               |N, V       |add word as a wrong word to the spellfile                      |
|`zW`               |N, V       |add word as a wrong word to the internal-wordlist              |
|`zuw`              |N, V       |undo `zw`                                                      |
|`zuW`              |N, V       |undo `zW`                                                      |
|`z=`               |N          |find the possible correction for the wrong word under cursor   |


