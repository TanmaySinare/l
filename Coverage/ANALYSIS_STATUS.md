# Analysis report status

The following local verification artifacts were produced while preparing this ZIP:

- `gcc_analyzer_report.txt`
- `asan_ubsan_thread_smoke.txt`
- `tsan_thread_smoke.txt`
- `app_data_smoke.txt`

Cppcheck, the MISRA addon, GoogleTest, Valgrind Memcheck, and Helgrind were unavailable in the preparation environment, so corresponding reports are not included as fabricated zero-finding reports. Generate them in Codespaces with `make cppcheck`, `make misra`, `make test`, `make valgrind`, and `make helgrind`.
