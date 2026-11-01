# Included application data files

The application uses these active files in its current working directory:

- `books.txt` — book records.
- `member.txt` — member records, including the registration timestamp when present.
- `transaction.txt` — issue/return transaction records.
- `library.log` — application event log.

For compatibility with earlier versions, this package also preserves `members.txt` and `transactions.txt` as copies of the supplied records. The active filenames are selected by `MEMBERS_FILE` and `TRANSACTIONS_FILE` in `include/library.h`; the compatibility copies are not read or updated by this version.

The sample transaction refers to book ID `12344` and member ID `1234`; matching records are included. Make a backup before running the application because normal operations can update the active text files.
