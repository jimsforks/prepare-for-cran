# Preparing your package for a CRAN submission 

An open and collaborative list of things you have to check before submitting your package to the CRAN. 

## Why this repo? 

This repo was born after exchanges on Twitter regarding the CRAN submission process, especially [this thread](https://twitter.com/yoniceedee/status/989281686142771203). 

The idea here is to collect ground rules that would help the CRAN doing there work more easily, by listing common (or uncommon) things that they ask maintainers to change in order to be CRAN-proof.    

CRAN submission is strict, the CRAN team is doing its work voluntarily, and there are more than 15K packages to maintain. 

We believe we can help them by giving some good practices about package development and CRAN submission, so that package authors can work on these issues before the CRAN team ask them to do so. Hence, we could save everyone time by preventing the CRAN team from sending you an email because there is "with R" in the title of your DESCRIPTION. Because, as said by [Peter Dalgaard](https://twitter.com/pdalgd/status/989944476398432257): 

> too many people do not realise that the CRAN maintainer group can be counted on one hand, even one finger at times. 

## Package Infrastructure Check

### Be sure that `devtools::check()` returns 0 0 0 

The first thing to do is to run `devtools::check()`, and to be sure that there are no error, no warning, no note.

If you're in RStudio, you can also click on Build > Check. 

> If ever you think these warnings or notes are not justified, leave a comment when submitting where you specify why you think this is not justified.

### Use a spellcheck

You can call `usethis::use_spell_check()` inside your package to add a test for spelling. 
Call `spelling::spell_check_package()` at any time if you need to run the spellcheck.

### Use `{rhub}`

The `{rhub}` package and API allow you to check your package on several platforms, and for CRAN with `rhub::check_for_cran()`.

More about `{rhub}`: <https://github.com/r-hub/rhub>

### Check for windows

Test that your package builds using the [win-builder](https://win-builder.r-project.org/) tool, or with `devtools::check_win_devel()`.

## Submission

These checks might not catch everything the CRAN team will catch, so here is a list of good practices:

### (Re)submission

Note that if this is your first submission, you will automatically have a NOTE, for `New submission`.

#### Temporality

CRAN submission of a new version should not be done too frequently. One time every 30 days seems to be the general rule (unless you're resubmitting after a CRAN team member feedback).

#### Explain what you have changed

When resubmitting after a CRAN feedback, be sure to include that this is a resubmission after a feedback, and describe what you have done.

#### Bump your version number

If you resubmit after a CRAN feedback, add 1 to the patch component of your version number (e.g, if your first submission is 0.3.1, your resubmission should be 0.3.2).

## General Comments

### Spelling

CRAN can reject packages that have grammar errors on the `DESCRIPTION`. 
Some common spelling errors: 
  - Other packages should be between `'` (example: `Lorem-Ipsum Helper Function for 'shiny' Prototyping`)
  - Acronyms should be capitalized (example: `API`, not `Api`)

## About the DESCRIPTION file

### You're the copyright holder

`DESCRIPTION` file should have a `cph` (copyright holder). 
It can either be the first author, or the company where the author(s) work(s). 

### Fill all the slots 

This might not be caught by the CRAN, but be sure you have filled everything in this file. 

### Do not put 'in R' or 'with R' in the package title

It's tempting to write something like ['A friendlier condition handler for R'](https://github.com/ColinFay/attempt) or ['Easy Dockerfile creation for R'](https://github.com/ColinFay/dockerfiler) in the title of your repository on GitHub (and seems appropriate). 
The CRAN team will ask you to remove this, as it is redundant (you're only dealing with R packages on the CRAN).

### Description should be a paragraph

Write an elaborated Description  field. 

### Package title should be in title case

The title should be in title case

> [Capital and Lowercase Letters in Titles (Title Case)](http://www.grammar-monster.com/lessons/capital_letters_title_case.htm)

Note: this should be caught by r-hub.

### Polish the description of your package

A CRAN-proof package should have a long description that explains what the package does,  what are the benefits, what is new and how does it differ from what is already on the CRAN.

### Do not put "This package", package name, title or similar in the Description

Neither the title nor the description should start with "A package..." or with the name of the package.

### Use quote if needed

If you quote another package, an article/book or a website/API, put its name between single quote `'`.

### Use parentheses after function names

If a function name is used in your DESCRIPTION, make sure to follow it with parentheses.  e.g., "provides a drop-in replacement for cat() from the 'base' package."

### Cite a reference for implementation of a method or linking to a service.

If you write an R interface to an API or implement an algorithm from a published article/book, add a reference to the publication as a DOI, ISBN, or similar canonical link, or URL for the API or article in the 'Description' field of your DESCRIPTION file.

### Use angle brackets for auto linking

When linking to an article or website in the DESCRIPTION, use angle brackets to auto-link.

```
API name <http:...> or <https:...>
authors (year) <DOI:...> (see <https://en.wikipedia.org/wiki/Digital_object_identifier> )
authors (year) <arXiv:...>
authors (year, ISBN:...)
```
with no space after `https:`, `doi:`, `arXiv:` and angle brackets for auto-linking. 

## Documentation 

### All exported functions should have a return value

All the exported functions in your package should have a `@return` value. 
If a function does not return a value, document that too.

### About \dontrun{} 

`\dontrun{}` elements in the examples might in fact be run by CRAN. 
If you don't want an example to be run, wrap it between `if (interactive()) {}`.

### Use canonical and https URL 

If there are some URLs in your documentation, be sure to: 

+ use https
+ use the canonical form for CRAN package (i.e.: https://CRAN.R-project.org/package=***)
+ use canonical form for Bioconductor package (i.e.: https://bioconductor.org/packages/***)

### Long running examples

If you have examples that take more than a few seconds each to run, wrap them in `\donttest{}`, don't use `dontrun{}`.

```
#' @example
#' \donttest{x <- foo(y)}
```

### Fill all the values in documentation

There should be no empty tags in the documentation (for the one requiring a value).

## Package structure

### Use temporary files and folder if you write on the disk 

CRAN Repository Policy state : 

> Packages should not write in the user’s home filespace (including clipboards), nor anywhere else on the file system apart from the R session’s temporary directory (or during installation in the location pointed to by TMPDIR: and such usage should be cleaned up). Installing into the system’s R installation (e.g., scripts to its bin directory) is not allowed.

You might not know what temporary directories / files are or how to use them. These temporary files are created for the current R session, and they are deleted when the session is closed. 

You can create them with: 

```
file <- tempfile()
```

Add an extension with 

```
tmp <- tempfile(fileext = ".csv")
tmp
[1] "/var/folders/lz/thnnmbpd1rz0h1tmyzgg0mh00000gn/T//Rtmpnh8kAc/fileae1e28878432.csv"
```

So you can: 

```
write.csv(iris, file = tmp)
```

See: [Create Names for Temporary Files](https://stat.ethz.ch/R-manual/R-devel/library/base/html/tempfile.html)

## About revdep 

If packages depend on your package, you should run a reverse dependencies test with `devtools::revdep()`.

## What to do once your package is ready? 

#### Using devtools::release()

You can run `devtools::release()` to automatically send to CRAN from R.

#### Confirm by following the link 

You'll receive a link in your mailbox. Click on this link to confirm the upload. 

### Wait for the release

Depending on the package, it might take between one hour and several weeks, if it needs manual inspection, it can take some time. 

You can watch the status of your package with `{cransays}`: https://lockedata.github.io/cransays/articles/dashboard.html

## Resources to learn about package development : 

### Books and blogposts

+ [Checklist for CRAN submissions](https://cran.r-project.org/web/packages/submission_checklist.html)

+ [CRAN Repository Policy](https://cran.r-project.org/web/packages/policies.html)

+ [R packages](http://r-pkgs.org) - by Hadley Wickham & Jennifer Bryan

+ [Writing R Extensions](https://cran.r-project.org/doc/manuals/r-release/R-exts.html) - Official Documentation

+ [Mastering Software Development in R](https://bookdown.org/rdpeng/RProgDA/) - by Roger D. Peng, Sean Kross, and Brooke Anderson.

+ [How to develop good R packages (for open science)](http://www.masalmon.eu/2017/12/11/goodrpackages/) - by Maëlle Salmon (including list of tutorials)

+ [Sinew: Simple R Package Documentation](https://metrumresearchgroup.github.io/sinew/) - by Jonathan Sidi

+ [rOpenSci Packages: Development, Maintenance, and Peer Review](https://ropensci.github.io/dev_guide/) - by rOpenSci onboarding editors
