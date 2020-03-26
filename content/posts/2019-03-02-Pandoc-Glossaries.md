+++
title = "Using glossaries with Pandoc Markdown"
date = "2019-03-02"
+++

## Pandoc Markdown

I have used [Latex](https://www.latex-project.org/) to write documents for many years
including writing my MSc dissertation. It is a hell of a lot better than using LibreOffice
or Google Docs for large pieces of work, not least because it means I can write everything
using [the one true editor](https://www.vim.org/). Watching my partner trying to open her
60k word PhD thesis in MS Word gave me nightmares for days! 

But much like my journey from Java to Python, I have moved from Latex to Pandoc Markdown,
because frankly life is too short for all those "\" and "{ }"! 

Most people will be familiar with
[Markdown](https://daringfireball.net/projects/markdown/) as a simple mark up language
that converts to html. But [Pandoc
Markdown](https://pandoc.org/MANUAL.html#pandocs-markdown) is a turbo charged superset of
Markdown that adds pretty much everything from Latex that you might need and few extra
things as well. 

[Pandoc](https://pandoc.org/index.html) is a magical program written by wizards in a
language that is, as I understand it, not too dissimilar from
[Elvish](https://www.haskell.org/). This fantastical program can convert my Markdown files
into pretty much any other format, so I can write a report once and be able to output it
as a beautifully typeset PDF or a webpage without changing the source code (much).

I am writing my thesis in Pandoc Markdown, which has so far been a great experience,
[pandoc-citeproc](https://github.com/jgm/pandoc-citeproc) and
[pandoc-crossref](https://github.com/lierdakil/pandoc-crossref) have made the transition
from Latex pretty seamless. No more `.bib` files, just [CSL](https://citationstyles.org/)
yaml files output by the wonderful [Zotero](https://www.zotero.org/)! 

I have, so far, not needed to stray from using the default Latex template to convert the
Markdown docs into a PDF. I may need to start faffing with my own template as I approach
submission time and need to get everything in the correct place but I am going to avoid
doing that for as long as possible.

## Glossaries 

My thesis is chock full of acronyms and techno-jargon, so in order to make life easier for
my readers I wanted to include a glossary for easy reference. Now there is no way I am
going to keep track of what acronyms have been defined in the text e.g. "Big Friendly
Giant (BFG)" and where. So I want something to do this for me. Sadly, As Far As I Know
(AFAIK), there is no native Pandoc filter to do this.

One of the great things about Latex is the amazing breath of packages available on
[CTAN](https://www.ctan.org/) and therein lies the wonderful [glossaries
package](https://www.ctan.org/pkg/glossaries) which pretty much Does What It Says On The
Tin (DWISOTT). It lets you define acronyms and terms along with their definitions,
descriptions and a unique label. You then reference them in you source code using the
label like so: `\gls{DWISOTT}` and glossaries will replace the first instance of the
acronym with the long-form (short-form) and every instance after with the short form. For
terms it always puts the long-form. It can also create the Glossary section for you and if
you use the hyperref package as well will add links from every usage to the Glossary (and
back to the source page).

Pandoc is fully aware of the glossaries package (and the simpler [acronym
package](https://www.ctan.org/pkg/acronym)) so will pass any instance of `\gls{label}`
straight through to the Latex code and therefore the resulting PDF. But I didn't start
writing Pandoc Markdown so I could write a load of backslashes and curly brackets in my
source code. I also find that the `\gls` makes it harder to me to read what the acronyms
are. There had to be a better way!

## pandoc-gls

Pandoc provides the option of applying [filters](https://pandoc.org/filters.html) to your
source code which gives a way for users to alter the source code after it is read and
before it is written to a new format. Filters can be written in any language thanks to a
process that involves reading from standard in and writing to standard out.
Pandoc-crossref and pandoc-citeproc are example of advanced filters.

I realised I could use my own syntax (`(+label)`) for glossary entries and use a filter to
replace that syntax with the `\gls{label}` form. The easiest way to write your own filters
for Pandoc is via the [lua-filters](https://pandoc.org/lua-filters.html) interface. I
don't really know Lua that well but the docs are great and I was able to put together a
simple filter to do what I want and I called it
[pandoc-gls](https://github.com/tomncooper/pandoc-gls). Instructions for using it are all
in the repo and I hope others might find it useful. My next job is to write a filter, or
adapt pandoc-gls, so that it will replace glossary entries with tooltips for html output
so that I can convert my thesis into a website.

The inspiration for pandoc-gls and its syntax come from
[pandoc-ac](https://github.com/Enet4/pandoc-ac) by Eduardo Pinho. 

