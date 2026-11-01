# MISRA C:2012 review and deviation register

## Status

This project has received a **MISRA-oriented improvement pass**, but it is **not certified or fully MISRA C:2012 compliant**. The supplied Cppcheck report contains many findings, and some are architectural rather than local style changes. A genuine compliance claim requires a fresh scan with a correctly configured checker, review of every finding, approved deviations where required, and project-level verification.

## Changes made in this pass

| Area | Change | Files |
|---|---|---|
| Cppcheck `constVariablePointer` | Changed the three `struct tm *` declarations to `const struct tm *` | `Src/members.c`, `Src/util.c` |
| Rule 2.5 | Used the `LOG_FILE` macro in the logger rather than repeating the literal path | `Src/util.c` |
| Rule 5.9 | Renamed the transaction module's internal helper to avoid a duplicate internal-linkage identifier across translation units | `Src/transactions.c` |
| Rule 13.3 | Split array assignment and post-increment expressions into separate statements | `Src/books.c`, `Src/members.c`, `Src/storage.c`, `Src/transactions.c` |
| Rule 13.4 | Split chained counter/capacity assignments into individual statements | `Src/util.c` |
| Rule 14.4 | Made several integer/predicate conditions explicit (for example `ferror(file) != 0` and `returned != 0`) | `Src/members.c`, `Src/storage.c`, `Src/transactions.c`, `Src/util.c` |
| Rule 17.7 screening | Explicitly cast intentionally ignored output/memory function return values to `void` | `Src/*.c` |
| Rule 22.8 | Reset `errno` before `strtol()` and handle `ERANGE` | `Src/util.c` |
| MISRA addon type parsing | Replaced `sizeof(Book)`, `sizeof(Member)`, and `sizeof(Transaction)` with pointed-object `sizeof` expressions | `Src/util.c` |
| Integer/character expressions | Made the `getchar()` newline comparison explicit and gave length macros unsigned suffixes | `Src/util.c`, `include/library.h` |
| Sanitizer build | Compile C modules with GCC as C, then link the C++ GoogleTest runner | `Makefile` |
| Coverage generation | Invoke GCov once per `.gcno` file instead of passing a wildcard list in one invocation | `Makefile` |

The three Cppcheck pointer suggestions reported in the supplied baseline are addressed in source. A new Cppcheck run is still required to confirm that the installed checker reports them as resolved.

## Findings that remain and why

The supplied baseline MISRA report included the following significant groups:

| Baseline rule | Baseline count | Review note |
|---|---:|---|
| 15.5 | 112 | The code uses multiple early returns for guard clauses and error handling. A comprehensive single-exit refactor would touch many functions and needs behaviour-focused testing. |
| 17.7 | 95 | This pass explicitly discards many intentionally unused return values. The checker must be rerun to identify any remaining reports. |
| 12.1 | 38 | Operator-precedence findings remain to be reviewed expression by expression. |
| 21.3 | 9 | Dynamic allocation (`realloc`/`free`) is part of the current growable data model. Strict compliance requires a fixed-capacity/static-memory design or an approved deviation. |
| 14.4 | 9 | Several integer-valued conditions were clarified. The remaining findings require a fresh scan and review. |
| 15.6 | 7 | Braces were added to selected single-statement conditionals; remaining locations need a fresh scan. |
| 21.6 | 5 | The console application uses standard I/O (`stdio.h`) for its user interface and file handling. Strict compliance requires an approved I/O abstraction or a documented deviation. |
| 13.3 | 5 | Array insertion expressions with post-increment were split. |
| 21.10 | 2 | The application uses the C time library for dates/timestamps. Strict compliance requires an approved time-service abstraction or a documented deviation. |

The baseline report also includes other rules (for example 10.x, 15.7, 22.10, and 8.7) and configuration diagnostics. Those must be re-evaluated with the current sources. The public `find_books_parallel()` function is exposed in `include/library.h` and called by test programs, so it intentionally has external linkage; do not make it `static` just to silence a scan that omits the test translation units.

### Cppcheck MISRA configuration diagnostics

The baseline report reported `ENOENT`, `Book`, `Member`, and `Transaction` as unknown to the MISRA add-on. The `sizeof` expressions for the three typedefs were changed to `sizeof *lib->...`, but the `ENOENT` diagnostic may be due to the add-on's system-header configuration. The source already includes `<errno.h>`. Do not replace `ENOENT` with a hard-coded numeric value to silence the checker. Re-run on the target toolchain with correct system include/platform configuration and inspect the add-on configuration report.

## Required follow-up for a strict compliance claim

1. Re-run `make cppcheck` and `make misra` on the target Linux/Codespaces environment.
2. Fix remaining mandatory-rule findings and review advisory findings.
3. Decide whether dynamic memory, console I/O, and time-library use are permitted by the project's safety profile. If not, replace them with approved abstractions; otherwise document formal deviations with rationale and risk controls.
4. Run the full GoogleTest suite, coverage, Memcheck, and Helgrind on the target environment.
5. Review the final reports and obtain the project's required independent compliance approval.

**Do not describe this package as “MISRA compliant” yet.** The accurate description is “MISRA-screened and improved; remaining findings/deviations are documented.”
