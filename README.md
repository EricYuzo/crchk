# crchk

Simple tool that uses CRC-32 check in file name to detect corrupted files.

## Usage

`crchk FILENAME...`

Examples:

* `crchk test_[00000000].txt` - check file named "test\_[00000000].txt"
* `crchk *.mkv` - check all '.mkv' files in current directory

## ToDo

Features to be included:

* append CRC-32 check in file name
* check files listed in a SFV file
* generate SFV file
