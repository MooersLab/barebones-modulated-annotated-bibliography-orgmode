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
This enables the reuse of an annotated bibliography entry in multiple annotated bibliographies.
The communal folder is named `~/abibNotes`.
Image files that are called by these notes are stored in `~/abibNotes/images`.

## The note files
The note files start as empty files with the cite key as the base of the file name and 'org' as a file extension.
This file extension is all that Emacs needs to recognize this file as an Org-mode file.
The note file can contain subheadings written in org-mode.
Using the `#+LATEX: \paragraph` environment is simpler to avoid jumping between org-mode and \LaTeX.

The note file can contain org-mode tables formatted according to the booktabs package, which supports using three types of rules: toprule, midrule, and bottomrule.
The note file can also contain images and code listings with syntax highlighting.
An example note file is provided.

This template is agnostic about which Emacs bibliographic entry manager you are using.
However, it does depend on the use of a global.bib file.

## Semi-automated note entry
The Emacs Lisp function `mooeralab-wrap-citar-citekey-and-create-abibnote-org` eases adding entries to the master annotated bibliography file and supports keeping your focus on writing the entry.
The function assumes you utilize the *citar* Emacs package to manage the bibliography.
You could adapt it to org-ref or the built-in org-cite packages.
I will build those variants someday.

The function is found in the `MooersLab/mooerslab-functions-el` repo.
Three simple steps are required as listed below:

1.  Insert a citekey at the cursor in the ab----.org document, the master annotated bibliography file.
2.  Place the cursor inside the square brackets of the citekey.
3.  Enter in the minibuffer: M-x mooerslab-wrap-citar-citekey-and-create-abibnote-org

With the corrected configuration, the last used function will be at the top of the list in the minibuffer, allowing rerunning the command to proceed quickly.
The cite key will be replaced with the bibliographic information injected into a `\subsubsection{}` heading.
At the same time, a new buffer will open with the new note file, allowing you to edit it.
The new note file already has the citekey for its filename, and it is already in the final destination folder: the abibNotes folder in your home directory.
The corresponding BibTeX entry is supposed to be appended to the file `ab----.bib` so that you can have a local copy of BibTeX entries to ease sharing the annotated bibliography.
It may show up in a `sers.bib` file.

## Bulk processing (to be done)

A list of list citekeys extracted from a manuscript can be converted in bulk into subsubsections in the main document with the parallel creation of the Abibnotes.


## Convenience function to wrap the insertion citekey

The description of the function below effectively explains how it works.
This function is also stored in the repository `MooersLab/mooerslab-functions-el`.
This latter repository contains the file `mooerslab.el`.
This file is an Emacs Lisp package.
It will contain the latest version of the function below.
It is unlikely that I will remember to update this webpage every time I make updates to that function.

```elisp
(defun mooerslab-wrap-citar-citekey-and-create-abibnote-org ()
    "Replace the citekey under the cursor with LaTeX-wrapped text and create a 
    corresponding empty citekey.org file in abibNotes folder in the home directory.
    Will work with citekeys in citar style or in LaTeX style or plain naked citekeys.
    The LaTeX code uses the bibentry package to inject a bibliographic entry into 
    a section heading that is added in the table of contents. The function also adds
    file links to the PDF and org note files in a comment block for quick access."

    (interactive)
    (let* ((bounds (or (save-excursion
                      ;; Check if we're inside a citation
                      (let ((start (re-search-backward "\\[cite:@" (line-beginning-position) t))
                            (end (re-search-forward "\\]" (line-end-position) t)))
                        (when (and start end)
                          (cons start end))))
                    ;; Otherwise just get the word at point
                    (bounds-of-thing-at-point 'word)))
         (citation-text (when bounds 
                          (buffer-substring-no-properties (car bounds) (cdr bounds))))
         (citekey (when citation-text
                    (if (string-match "\\[cite:@\\([^]]+\\)\\]" citation-text)
                        (match-string 1 citation-text)
                      citation-text))) ;; Plain word if not a citation
  
         ;; Extract directory from current buffer filename
         (current-file (buffer-file-name))
         (current-dir (when current-file (file-name-directory current-file)))
  
         ;; Try to determine default project number from filename
         (default-project-number 
          (cond 
           ;; First try to find "ab2156" pattern in the buffer file name
           ((and current-file 
                 (string-match "ab\\([0-9]+\\).org" (file-name-nondirectory current-file)))
            (match-string 1 current-file))
     
           ;; Look for "2156" in the buffer file name
           ((and current-file 
                 (string-match "\\([0-9]+\\)" (file-name-nondirectory current-file)))
            (match-string 1 current-file))
     
           ;; Default to empty string
           (t "")))
  
         ;; Prompt the user for project number with default from filename
         (project-number (read-string (format "Project number for BibTeX file [%s]: " 
                                             default-project-number)
                                     nil nil default-project-number))
  
         ;; Construct file paths
         (bib-file-name (concat "ab" project-number ".bib"))
         (bib-file-path (and current-dir (concat current-dir bib-file-name)))
         (org-file-dir "/Users/blaine/abibNotes/") ;; Directory for the .org file
         (org-file-path (and citekey (concat org-file-dir citekey ".org"))) ;; Full path for the .org file
         
         ;; Updated wrapped text with file links inside a comment block
         (wrapped-text (and citekey 
                           (format "#+LATEX: \\subsubsection*{\\bibentry{%s}}\n#+LATEX: \\addcontentsline{toc}{subsubsection}{%s}\n#+INCLUDE: %s\n#+BEGIN_COMMENT\nfile:~/abibNotes/%s.org\nfile:~/0papersLabeled/%s.pdf\n#+END_COMMENT"
                                  citekey citekey org-file-path citekey citekey))))

    ;; Debug message to check file paths
    (message "Using bibfile: %s" bib-file-path)

    (if (not citekey)
        (message "No citekey found under the cursor.")
      (progn
        ;; Delete the citation or word at cursor
        (when bounds
          (delete-region (car bounds) (cdr bounds)))
        ;; Insert the wrapped text in its place
        (insert wrapped-text)
 
        ;; Create a minimal .org file if it doesn't already exist - no header, no headline
        (if (file-exists-p org-file-path)
            (message "File %s already exists." org-file-path)
          (with-temp-file org-file-path
            (insert ""))) ;; Empty file
 
        ;; Append the BibTeX entry to the project-specific .bib file in current directory
        (require 'bibtex)
        (when (and (featurep 'citar) current-dir bib-file-path)  ;; Ensure we have all required paths
          ;; Get the bibtex entry using search in citar bibliography files
          (let* ((bib-files (citar--bibliography-files))
                 (bibtex-entry nil))
     
            ;; Look through each bibliography file for the entry
            (when bib-files
              (catch 'found
                (dolist (bib-file bib-files)
                  (with-temp-buffer
                    (insert-file-contents bib-file)
                    (bibtex-mode)
                    (bibtex-set-dialect 'BibTeX t)
                    (goto-char (point-min))
                    (when (re-search-forward (format "@[^{]+{%s," citekey) nil t)
                      (let ((beg (save-excursion 
                                   (bibtex-beginning-of-entry)
                                   (point)))
                            (end (save-excursion
                                   (bibtex-end-of-entry)
                                   (point))))
                        (setq bibtex-entry (buffer-substring-no-properties beg end))
                        (throw 'found t)))))))
     
            (if bibtex-entry
                (progn
                  ;; Now append to the bib file
                  (with-temp-file bib-file-path
                    (when (file-exists-p bib-file-path)
                      (insert-file-contents bib-file-path))
                    (goto-char (point-max))
                    (unless (or (bobp) (bolp)) (insert "\n"))
                    (insert bibtex-entry "\n\n"))
                  (message "Added BibTeX entry to %s" bib-file-path))
              (message "Could not retrieve BibTeX entry for %s" citekey))))
 
        ;; Open the .org file in a new buffer
        (find-file org-file-path)
        (message "Replaced citekey, created .org file, and opened it: %s" org-file-path)))))
```

This function replaces the citekey (e.g., [cite@Schaefer2021SEQCROW]) with the following:

```elisp
#+LATEX: \subsection*{\bibentry{Schaefer2021SEQCROW}}
#+LATEX: \addcontentsline{toc}{subsubsection}{Schaefer2021SEQCROW}
#+INCLUDE: /Users/blaine/abibNotes/Schaefer2021SEQCROW.org
#+BEGIN_COMMENT
file:~/abibNotes/Schaefer2021SEQCROW.org
file:~/0papersLabeled/Schaefer2021SEQCROW.pdf
#+END_COMMENT
```

The links allow direct access to the annotated bibliography note file after you have initially created it and closed it.
This eases the process of making further edits to the annotated bibliography note file.
The link to the PDF also makes it easier to open, as you don't have to spend time searching for it.
These two links greatly facilitate making additions and further edits to the note files.

The links in the org-mode comment are not exported to the PDF.
If the links are not included in the comment, the first page of the PDF in the source files will be displayed within the exported PDF file of the annotated bibliography.
Some might find that to be advantageous.

## Update history

|Version      | Changes                                                                                                                                 | Date                 |
|:-----------|:------------------------------------------------------------------------------------------------------------------------------------------|:--------------------|
| Version 0.1 |   Added badges, funding, and update table.  Initial commit.                                                                              | 5/14/2025           |

## Sources of funding

- NIH: R01 CA242845
- NIH: R01 AI088011
- NIH: P30 CA225520 (PI: R. Mannel)
- NIH: P20 GM103640 and P30 GM145423 (PI: A. West)
