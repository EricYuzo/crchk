# crchk

Simple tool for checking the integrity of files using CRC-32.

## Features

* Verify files with CRC code in the file name.
* Verify files inside directories and subdirectories recursively.
* Support for SFV files.
* Append calculated CRC-32 code to file name.

## Dependency

This tool requires the program `crc32`.

On Debian-like systems, install the package `libarchive-zip-perl`.

## General information

### Basic usage

```
crchk [OPTION]... FILENAME...
```

Verify file named FILENAME. You can pass multiple files separated by whitespaces.
You can also change the default behavior specififying some options (these options are explained later in this text)

### Output

The output contains 4 columns:

```file_name    detected_crc    calculated_crc    status```

* **file\_name**: obviously, the file name
* **detected\_crc**: the CRC detected from file name; if it is not possible to detect the CRC in file name, `NA` is printed
* **calculated\_crc**: the CRC-32 calculated from file
* **status**: `ok` if detected CRC is equal to calculated CRC; `corrupted` if they are different; or `NA` if detected CRC is `NA`

Example:

```
$ crchk nice_video_[053A143E].mkv nice_video_(copy)_[053A143F].mkv nice_video_(copy2).mkv
nice_video_[053A143E].mkv    053a143e    053a143e    ok
nice_video_(copy)_[053A143F].mkv    053a143f    053a143e    corrupted
nice_video_(copy2).mkv    NA    053a143e    NA
```

### Naming convention

The CRC code must be in the end of file name (before extension), and enclosed by brackets (it may be round, square, or curly brackets).

Here you have some examples of accepted CRC specification:

* "nice\_video\_[053A143E].mkv"
* "manual-v1.0.0-(02468ace).pdf"
* "readme{8089e087}.txt"

The CRC code will only be detected in the file name, if it follows this naming convention.

