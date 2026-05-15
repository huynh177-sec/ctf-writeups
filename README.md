# ctf-writeups
My CTF and Linux learning writeups

# Bandit Day 3 — Levels 0 → 3

## Level 0 → 1
**Goal**: Read the file `readme` in home directory.
**Solution**: `cat readme`
**Lesson**: Basic file reading.

## Level 1 → 2
**Goal**: Read the file named `-`.
**Solution**: `cat ./-`
**Why `cat -` doesn't work**: `-` is Unix convention for stdin/stdout, not a filename.
**Alternatives**: `cat -- -`, `cat < -`

## Level 2 → 3
**Goal**: Read a file with spaces in name.
**Solution**: `cat "./--spaces in this filename--"`
**Lesson**: Combine `./` (for filename starting with `-`) and quoting (for spaces). 
Tab completion auto-handles both.
