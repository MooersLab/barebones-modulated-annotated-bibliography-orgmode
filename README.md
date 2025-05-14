![Version](https://img.shields.io/static/v1?label=barebones-modulated-annotated-bibliography-orgmode&message=0.0&color=brightcolor)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)


#  A bare-bones modular annotated bibliography for org-mode

This repo is for a template for a bare-bones modular annotated bibliography that uses a small Latex preamble.
The idea is to reduce LaTeX package conflicts between what is in the preamble and what is pre-configured in org-mode.

The annotated bibliography resides in a subfolder in a writing project.
The subfolder has the address ./ab----, where ---- is a four digit project number.
The project number is stored in a database.
The number is used to label folders and files, categorize projects, and navigate between projects.
'ab' stands for annotated bibliography.

The annotated bibliography notes are stored in individual files in the communal folder in the home directory.
This enables the reuse of an annotated bibliography entry in more than one annotated bibliography.
The communal folder has the name ~/abibNotes.
Image files that are called by these notes are stored in ~/abibNotes/images.

The note files start out as empty files with the cite key serving as the base of the file name and 'org' serving as a file extension.
This file extension is all that is needed for Emacs to recognize this file as a orgmode file.
The note file can contain subheadings written in org-mode.
It is simpler to use the  `#+LATEX: \paragraph` environment to avoid jumping between org-mode and \LaTeX.

The note file can contain org-mode tables that are formatted following the book tabs package, which supports the use of three kinds of rules: toprule, midrule, and bottomrule.
The note file can also contain images and code listings that have syntax highlighting.

This template is agnostic with regard to which Emacs bibliographic entry manager that you are using.
However, it does depend on the use of a global.bib file.

This function ml/wrap-citar-citekey-and-create-abibnote-org assumes that you are utilizing the citar Emacs package for managing the bibliography.
The function is found in MooersLab/mooerslab-functions-el.
You first insert a citekey.
Then you place the cursor inside of the citekey.
You execute the above function in the mini buffer and the cite key will be replaced with the bibliographic information injected into a \subsubsection{} heading.
At the same time, a new buffer will open with the new note file so that you can go about editing it.
This file will have been created in the abibNotes folder in your home directory.
The corresponding did tech entry is supposed to be appended to the file ab----.bib so that you can have a local copy of BibTeX entries to ease sharing the annotated bibliography.


## Update history

|Version      | Changes                                                                                                                                                                         | Date                 |
|:-----------|:------------------------------------------------------------------------------------------------------------------------------------------|:--------------------|
| Version 0.1 |   Added badges, funding, and update table.  Initial commit.                                                                                                                | 5/14/2025  |

## Sources of funding

- NIH: R01 CA242845
- NIH: R01 AI088011
- NIH: P30 CA225520 (PI: R. Mannel)
- NIH: P20 GM103640 and P30 GM145423 (PI: A. West)