This repository is intended for preparing the content for DbFit article for M&T magazine.

## Technical specs for the final tools presentation article:
* Length & size: between 3 and 6 pages (A4 size), usage of figures should be limited to obtain a reasonably sized PDF file. M&T is published only in electronic format.
* File format: word 97 readable (.doc or .rtf), additional figures in jpg format.
* Text formatting: minimal is better. M&T use Times new roman 12 font and single spaced paragraphs on one column. Editor will "reformat" your text.
* Google: part of your popularity will depend on how Google will index your article. Try to include important keywords in the title and in your first paragraph. Writing "clever" content doesn't always pay on the web ;O(
* References: use an explicit reference section at the end of the article, no footnotes or embedded links to url.

Content structure:
* short introduction/description of the tool. Give "technical facts":
  * Web Site:
  * Version tested:
  * System requirements:
  * License & Pricing:
  * Support:
* Then you can describe you tool as you want, either with a "usage scenario" story or with features description. You will find previous presentations on http://www.methodsandtools.com/tools/tools.php


## Working draft format

`dbfit-article.md` is in Markdown format compatible with pandoc. To convert to LibreOffice (open document format):

    pandoc -o dbfit-article.odt dbfit-article.md

Initially we focus on the content and structure. The intention is to keep the formatting and references simple - in order to be able to easier review and discuss changes via pull requests, git diff, etc.
