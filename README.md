# crchk

Simple tool for checking the integrity of files using CRC-32.

## Features

* Check files using CRC in the filename. The CRC-32 code must be enclosed by brackets (it may be round, square, or curly brackets). Example: "*nice\_video\_[053A143E].mkv*" or "*manual(02468ace).pdf*".

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
