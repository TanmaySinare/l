# Verification status

This package contains the full source tree, test sources, build scripts, and the supplied library data files. Results below were collected in the preparation environment on 2026-11-02. A check marked NOT RUN was not executed; no report has been invented.

| Check | Result | Evidence / note |
|---|---|---|
| `make clean && make -j2` | PASS | All seven C modules compiled and linked under C11 with `-Wall -Wextra -Wpedantic -Wconversion -Wshadow` and `-pthread`. |
| `make thread-smoke` | PASS | Standalone parallel-search test scanned 128 records and found the 7 expected matches. |
| App data-load smoke test | PASS | Application loaded the active `books.txt`, `member.txt`, and `transaction.txt` files from a temporary copy, accepted the demo login, and exited normally. |
| GCC `-fanalyzer` | PASS | All production C files were checked with no analyzer diagnostics. See `Coverage/gcc_analyzer_report.txt`. |
| ASan + UBSan thread smoke | PASS | Completed with leak detection enabled. See `Coverage/asan_ubsan_thread_smoke.txt`. |
| ThreadSanitizer thread smoke | PASS | Completed with exit code 0. See `Coverage/tsan_thread_smoke.txt`. |
| GoogleTest suite | NOT RUN | GoogleTest headers/libraries were unavailable in the preparation environment. Run `make test` in Codespaces after installing `libgtest-dev`. The test source currently contains 89 `TEST` declarations; that is not a passed-test count. |
| Cppcheck | NOT RUN | Cppcheck was unavailable in the preparation environment. The three `constVariablePointer` findings from the supplied report are fixed in source, but a fresh complete Cppcheck scan is required to confirm whether any other findings remain. Run `make cppcheck`. |
| MISRA C:2012 addon | NOT RUN | Cppcheck/MISRA addon was unavailable. The supplied baseline report contains multiple rule findings and seven configuration diagnostics. This package is MISRA-oriented and improved, not certified or fully MISRA C:2012 compliant. Run `make misra` and review all findings/deviations. |
| Valgrind Memcheck / Helgrind | NOT RUN | Valgrind was unavailable in the preparation environment. Run `make valgrind`, `make helgrind`, `make valgrind-smoke`, and `make helgrind-smoke` in Codespaces. |

## Cppcheck changes

All three `constVariablePointer` declarations from the supplied report now use pointers to const:

- `Src/members.c`: `const struct tm *time_info`
- `Src/util.c`: both `const struct tm *local` declarations

A source-level check confirms those three declarations are updated. This does not substitute for rerunning Cppcheck over every translation unit.

## Data files included

The active filenames selected by `include/library.h` are `books.txt`, `member.txt`, `transaction.txt`, and `library.log`. Compatibility copies `members.txt` and `transactions.txt` are also included, preserving the files that were supplied with the project.

## MISRA status

Do not describe this package as fully MISRA C:2012 compliant. The current application design uses dynamic memory allocation, standard I/O, and the C time library, and the supplied MISRA screening report includes findings that require architectural review or formally approved deviations. A clean compiler build or a passing smoke test does not establish MISRA compliance.
