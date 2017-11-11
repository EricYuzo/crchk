# crchk

Simple tool for checking the integrity of files using CRC-32.

## Features

* Verify files using CRC in the file name. The CRC-32 code must be enclosed by brackets (it may be _round_, _square_, or _curly_ brackets). Example: "*nice\_video\_[053A143E].mkv*" or "*manual(02468ace).pdf*".
* Verify files inside directories and subdirectories recursively.
* Support for SFV files.
* Append CRC-32 code to file name.

## Dependency

This tool requires the program `crc32`.

On Debian-like systems, install the package `libarchive-zip-perl`.

## Usage

`crchk [OPTION]... FILENAME...`

Check the integrity of file named FILENAME. You can pass multiple file names separated by white spaces.

### Available options

* `-g` or `--generate-sfv`: generate SFV file. The SFV file has the same name of the parent directory and extension ".sfv". If a file with the same name already exists, then the SFV file is not created or modified. There is no option for overwriting existing SFV files, if you want to, you have to delete the files manually.
* `-i` or `--ignore-sfv`: ignore SFV files and only look for CRC code in the file name.
* `-r` or `--recursive`: recursively look for files to check in directories and subdirectories.
* `-u` or `--uppercase`: display uppercase CRC code on output.
* `-h` or `--help`: display a help message.
* `-v` or `--version`: display version information.

### Examples

* `crchk test_[00000000].txt` - check file named "test\_[00000000].txt"
* `crchk *.mkv` - check all '.mkv' files in current directory
* `crchk -r ~/videos` - check all files inside "~/videos" or any one of its subdirectories
* `crchk -g ~/videos/*` - check all files inside "~/videos" and create a SFV file named "videos.sfv"


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

