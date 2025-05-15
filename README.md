![Version](https://img.shields.io/static/v1?label=barebones-modulated-annotated-bibliography-orgmode&message=0.1&color=brightcolor)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)


#  A bare-bones, modular, and illustrated annotated bibliography written in org-mode

## Problem addressed
This repo is for a bare-bones modular annotated bibliography template with a small LaTeX preamble.
The idea is to reduce LaTeX package conflicts between the preamble and what is pre-configured in org-mode.

## Features

1. Modular design allows the reuse of the annotations.
2. Support for illustrating with tables, figures, code listings, and equations.
3. The entries can be categorized rather than being listed alphabetically.


## Modularity
The modularity comes from storing the annotation inside an external file that is important to the main document.
The standard approaches for storing the annotation in the BibTeX file.
Including tables, equations, figures, and code listings in the BibTeX file is more challenging.

## File organization
The annotated bibliography resides in a subfolder in a writing project.
The subfolder has the address `./ab----`, where `----` is a four-digit project number.
The project number is stored in a database.
The number is used to label folders and files, categorize projects, and navigate between projects.
'ab' stands for annotated bibliography.

The annotated bibliography notes are stored in individual files in the communal folder in the home directory.
This enables the reuse of an annotated bibliography entry in more than one annotated bibliography.
The communal folder has the name `~/abibNotes`.
Image files that are called by these notes are stored in `~/abibNotes/images`.

## The note files
The note files start as empty files with the cite key as the base of the file name and 'org' as a file extension.
This file extension is all that Emacs needs to recognize this file as an Org-mode file.
The note file can contain subheadings written in org-mode.
Using the `#+LATEX: \paragraph` environment is simpler to avoid jumping between org-mode and \LaTeX.

The note file can contain org-mode tables formatted following the booktabs package, which supports using three kinds of rules: toprule, midrule, and bottomrule.
The note file can also contain images and code listings with syntax highlighting.
An example note file is provided.

This template is agnostic about which Emacs bibliographic entry manager you are using.
However, it does depend on the use of a global.bib file.

## Semi-automated note entry
The Elisp function `ml/wrap-citar-citekey-and-create-abibnote-org` eases adding entries to the master annotated bibliography file and supports keeping your focus on writing the entry.
The function assumes you utilize the *citar* Emacs package to manage the bibliography.
You could adapt it to org-ref or the built-in org-cite packages.
I leave that as an exercise for the user.

The function is found in `MooersLab/mooerslab-functions-el`.
Three simple steps are required as listed below:

1.  Insert a citekey at the cursor in the ab----.org document, the master annotated bibliography file.
2.  Place the cursor inside the square brackets of the citekey.
3.  Enter in the minibuffer: M-x ml/wrap-citar-citekey-and-create-abibnote-org

With the corrected configuration, the last used function will be at the top of the list in the minibuffer, so rerunning the command goes quickly.
The cite key will be replaced with the bibliographic information injected into a `\subsubsection{}` heading.
At the same time, a new buffer will open with the new note file so you can edit it.
The new note file already has the citekey for its filename, and it is already in the final destination folder: the abibNotes folder in your home directory.
The corresponding BibTeX entry is supposed to be appended to the file `ab----.bib` so that you can have a local copy of BibTeX entries to ease sharing the annotated bibliography.
It may show up in a `sers.bib` file.

## Bulk processing (to be done)

A list of list citekeys extracted from a manuscript can be converted in bulk into subsubsections in the main document with the parallel creation of the Abibnotes.

## Update history

|Version      | Changes                                                                                                                                 | Date                 |
|:-----------|:------------------------------------------------------------------------------------------------------------------------------------------|:--------------------|
| Version 0.1 |   Added badges, funding, and update table.  Initial commit.                                                                              | 5/14/2025           |

## Sources of funding

- NIH: R01 CA242845
- NIH: R01 AI088011
- NIH: P30 CA225520 (PI: R. Mannel)
- NIH: P20 GM103640 and P30 GM145423 (PI: A. West)
