# crchk

Simple tool for checking the integrity of files using CRC-32.

## Features

* Check files using CRC in the filename. The CRC-32 code must be enclosed by brackets (it may be round, square, or curly brackets). Example: "*nice\_video\_[053A143E].mkv*" or "*manual(02468ace).pdf*".

## Usage

`crchk FILENAME...`

Examples:

* `crchk test_[00000000].txt` - check file named "test\_[00000000].txt"
* `crchk *.mkv` - check all '.mkv' files in current directory


### Output:

The output contains 4 columns:

```file_name    detected_crc    calculated_crc    status```

* **file\_name**: obviously, the file name
* **detected\_crc**: the CRC detected from file name; if it is not possible to detect the CRC in file name, `NA` is printed
* **calculated\_crc**: the CRC-32 calculated from file
* **status**: `ok` if detected CRC is equal to calculated CRC; `corrupted` if they are different; or `NA` if detected CRC is `NA`

Examples:

```
$ crchk nice_video_[053A143E].mkv
nice_video_[053A143E].mkv    053a143e    053a143e    ok
```

```
$ crchk nice_video_[053A143F].mkv
nice_video_[053A143F].mkv    053a143f    053a143e    corrupted
```

```
$ crchk nice_video.mkv
nice_video.mkv    NA    053a143e    NA
```

## ToDo

Features to be included:

* append CRC-32 check in file name
* check files listed in a SFV file
* generate SFV file
