This is a patch release of gratia that is largely motivated by the issues on 
gratia's CRAN Check Results page arising from breaking changes in the scam
package. The new version also addresses the soft deprecation of `case_match()`
in dplyr v 1.2.0 that was recently released.

The package has been checked under:

* r-devel on Windows via winbuilder,
* r-devel on Ubuntu via GitHub Actions, and
* r-release on MacOS X, ubuntu, and Windows

No problems were observed. I note the `INFO` from `R CMD check` regarding the
large number of imports; gratia is designed to work within the tidyverse of
packages, and most of the dependencies in Imports are to those packages which
are considered (relatively) stable and dependable. This is unavoidable when
working within the tidyverse.

Reverse dependency checks were run, with no regressions.

## R CMD check results

0 errors | 0 warnings | 0 notes

## Reverse dependency check results

------- Check results summary ------
Check status summary:
                  OK
  Source packages  1
  Reverse depends  1

Check results summary:
gratia ... OK
rdepends_cgaim ... OK

------- Check for regressions ------
No changes between old and new version
