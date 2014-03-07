# ox-linguistics
**ox-linguistics** is a package to make is easy to write linguistics-style
examples in [Org mode](https://orgmode.org) documents.  It supports
writing example sentences using ordinary Org mode lists. Examples are
exported to LaTeX using a linguistics example package such as
[linguex](http://www.ctan.org/pkg/linguex) or
[gb4e](http://www.ctan.org/pkg/gb4e). For example:

```Org
#+ATTR_LATEX: :environment linguex-ex
1) ? A questionable example am I. <<s:questionable>>
2) Is this a question? <<q:question>>
   - Yes. <<a:yes>>
   - I don't think so. <<a:no>>

Here I refer back to sentence [[s:questionable]].
```

will be exported to LaTeX as:

```TeX
\ex.?\label{s:questionable}A questionable example am I.
\par
\ex.\label{q:question}Is this a question?
\a.\label{a:yes}Yes.
\b.\label{a:no}I don't think so.
\z.
\par

Here I refer back to sentence \ref{s:questionable}.
```

This has a few advantages:
 - you can write examples with Org's more pleasant syntax
 - you can easily switch from one backend package to another
 - you can use Org's internal links to reference examples

This package is in active development. It currently supports examples
using `\ex. ...\par` in linguex and `\begin{exe}...\end{exe}` in gb4e,
but it does not support gloss versions of these commands. I am
considering adding support for the
[philex](http://www.ctan.org/pkg/philex) package, as well as an HTML
backend.  Feedback on how you would like to use this package would be
appreciated!

# Installation
There are no releases of ox-linguistics (yet); to install it, do:

    $ cd $WHEREVER
    $ git clone https://github.com/wyleyr/ox-linguistics.git
  
Then, somewhere in your .emacs, add:

```elisp
(add-to-list 'load-path $WHEREVER/ox-linguistics/lisp)
(require 'ox-linguistics)
```

# Use
Once you've loaded the package, using it from Org mode is simple.  To
turn a regular list into a list of example sentences, use Org's
`#+ATTR_LATEX:` syntax for that list, and set the `:environment`
keyword to indicate which package you want to use to generate the
examples in your exported document.  The environment keywords that
ox-linguistics recognizes are:
  - `linguex-ex` for linguex examples using the `\ex.` command
  - `gb4e-exe` for gb4e examples using the `exe` environment
  
So, for example:
```Org
#+ATTR_LATEX: :environment gb4e-exe
1) Example 1
2) * Example 2
3) ?? Example 3
```
will be exported as a gb4e example.  ox-linguistics takes care of
properly placing labels and judgments for whatever package you select. 

Don't forget to load the appropriate package in your document
preamble.  Here you would need:
```Org
#+EXPORT_LATEX_HEADER: \usepackage{gb4e}
```
or similar in your Org file.

## Labels
To label an example, use Org's target syntax:

```Org
#+ATTR_LATEX: :environment linguex-ex
1) This is my first example sentence. <<s:first>>
```
Here, the target will be exported as a `\label` with key `s:first`:

```Tex
\ex.\label{s:first}This is my first example sentence.
\par
```

You can place the target anywhere in the Org list item; ox-linguistics
will take care of placing the `\label` command in the correct place in
the exported LaTeX.

Using the target syntax allows you to refer back to example sentences
using Org's link syntax, which will be exported as `\ref` commands:

```Org
Here, I refer back to sentence [[s:first]], which was not very interesting.
```

## Judgments
To add a judgment to an example, simply place it at the beginning of
the example, with whitespace afterward.

```Org
#+ATTR_LATEX: :environment gb4e-exe
1) * This my second example sentence. <<s:second>>
2) ?? My third example this is. <<s:yoda>>
```

As with labels, ox-linguistics will place the judgment appropriately
in the exported LaTeX code.  Here, for example, the judgments will be
passed as optional arguments to gb4e's `\ex` command:

```TeX
\begin{exe}
\ex[*]{\label{s:second}This my second example sentence.}
\ex[??]{\label{s:yoda}My third example this is.}
\end{exe}
```

(Don't forget to escape judgment characters that have special meaning
to LaTeX, such as `%' and `#', with a backslash.)

## Exporting
ox-linguistics works by providing an export backend that derives from
Org's own LaTeX export backend.  To export an Org document using
ox-linguistics, invoke Org's export dispatcher via `C-c C-e`.  The
ox-linguistics commands are the same as the LaTeX exporter commands,
but they fall under the `d` prefix.  For example, `dp` generates a PDF file.
