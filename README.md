# crchk

Simple tool for checking the integrity of files using CRC-32.

## Features

* Verify files with CRC code in the file name.
* Verify files inside directories and subdirectories recursively.
* Support for SFV files.
* Append calculated CRC-32 code to file name.

## Dependencies

This tool requires `bash` shell and the program `crc32`.

On Debian-like systems, to obtain `crc32`, install the package `libarchive-zip-perl`.

## General information

### Basic usage

```
crchk [OPTION]... FILENAME...
```

Verify file named FILENAME. You can pass multiple files separated by whitespaces.
You can also change the default behaviour specififying some options (these options are explained in the "Tutorial" section)

### Output

The output contains 5 columns:

```
file_name    crc_source    detected_crc    calculated_crc    status
```

* **file\_name**: obviously, the file name
* **crc\_source**: the source of detected CRC; it can be `filename` or `sfv`
* **detected\_crc**: the CRC detected from file name or from SFV file; if it is not possible to detect the CRC, `NA` is printed
* **calculated\_crc**: the CRC-32 calculated from file
* **status**: `ok` if detected CRC is equal to calculated CRC; `corrupted` if they are different; or `NA` if detected CRC is `NA`

Example:

```
$ crchk nice_video_[053A143E].mkv nice_video_(copy)_[053A143F].mkv nice_video_(copy2).mkv
nice_video_[053A143E].mkv    filename    053a143e    053a143e    ok
nice_video_(copy)_[053A143F].mkv    filename    053a143f    053a143e    corrupted
nice_video_(copy2).mkv    filename    NA    053a143e    NA
```

### Naming convention

The CRC-32 code is an 8-digit hexadecimal number that must be present in the file name enclosed by brackets (it may be round, square, or curly brackets).

Here you have some examples of accepted CRC specification:

* "nice\_video\_[053A143E].mkv"
* "manual-v1.0.0-(02468ace)-final.pdf"
* "readme{8089e087}.txt"

The CRC code will only be detected in the file name, if it follows this naming convention.


## Tutorial

### Verify files with CRC code in the file name

Run `crchk` passing the file you want to verify:

```
$ crchk 'docs/report1_[bd3f9af6].txt'
docs/report1_[bd3f9af6].txt    filename    bd3f9af6    bd3f9af6    ok
```

You can also pass multiple files:

```
crchk 'docs/report1_[bd3f9af6].txt' 'docs/report2_[7d5e06c3].txt'
docs/report1_[bd3f9af6].txt    filename    bd3f9af6    bd3f9af6    ok
docs/report2_[7d5e06c3].txt    filename    7d5e06c3    7d5e06c3    ok
```

```
crchk docs/*.txt
docs/report1_[bd3f9af6].txt    filename    bd3f9af6    bd3f9af6    ok
docs/report2_[7d5e06c3].txt    filename    7d5e06c3    7d5e06c3    ok
```

### Handle directories recursively

Directories are skipped, by default. You can use `-r` option to handle directories recursively:

```
$ crchk -r docs/
docs/report1_[bd3f9af6].txt    filename    bd3f9af6    bd3f9af6    ok
docs/report2_[7d5e06c3].txt    filename    7d5e06c3    7d5e06c3    ok
docs/subdir/report3_[d1f4ad85].txt    filename    d1f4ad85    d1f4ad85    ok
docs/subdir/report4_[b9b1db50].txt    filename    b9b1db50    84462499    corrupted
```

Option `--recursive` has the same effect:

```
$ crchk --recursive docs/
docs/report1_[bd3f9af6].txt    filename    bd3f9af6    bd3f9af6    ok
docs/report2_[7d5e06c3].txt    filename    7d5e06c3    7d5e06c3    ok
docs/subdir/report3_[d1f4ad85].txt    filename    d1f4ad85    d1f4ad85    ok
docs/subdir/report4_[b9b1db50].txt    filename    b9b1db50    84462499    corrupted
```

### Display uppercase CRC code

Specify option `-u`, or `--uppercase`:

```
$ crchk -u docs/*.txt
docs/report1_[bd3f9af6].txt    filename    BD3F9AF6    BD3F9AF6    ok
docs/report2_[7d5e06c3].txt    filename    7D5E06C3    7D5E06C3    ok
```

```
$ crchk --uppercase docs/*.txt
docs/report1_[bd3f9af6].txt    filename    BD3F9AF6    BD3F9AF6    ok
docs/report2_[7d5e06c3].txt    filename    7D5E06C3    7D5E06C3    ok
```

### Handling SFV files

Any file with extension `.sfv` is handled as a SFV file.

#### Reading CRC from SFV

Consider a file named `docs.sfv` with the following content:

```
report1.txt bd3f9af6
report2.txt 7d5e06c3
```

Notice that the file name does not contain the CRC. If you pass those files to `crchk`, it will return `NA`:

```
$ crchk docs/*.txt
docs/report1.txt    filename    NA    bd3f9af6    NA
docs/report2.txt    filename    NA    7d5e06c3    NA
```

However, if you pass the file `docs.sfv`, it will succeed in verifying the 2 files:

```
$ crchk docs/docs.sfv
docs/report1.txt    sfv    bd3f9af6    bd3f9af6    ok
docs/report2.txt    sfv    7d5e06c3    7d5e06c3    ok
```

#### SFV and recursive search

When `-r` is specified, SFV files have preference over regular files. So, if there is a SFV inside a directory, it will be used as the source for verification.

```
$ crchk -r docs/
docs/report1.txt    sfv    bd3f9af6    bd3f9af6    ok
docs/report2.txt    sfv    7d5e06c3    7d5e06c3    ok
docs/subdir/report3.txt    sfv    d1f4ad85    d1f4ad85    ok
docs/subdir/report4.txt    sfv    b9b1db50    84462499    corrupted
```

You may change this behaviour specifying `-i` option, for ignoring SFV files:

```
$ crchk -ri docs/
docs/report1.txt    filename    NA    bd3f9af6    NA
docs/report2.txt    filename    NA    7d5e06c3    NA
docs/subdir/report3.txt    filename    NA    d1f4ad85    NA
docs/subdir/report4.txt    filename    NA    84462499    NA
```

#### Generating SFV

You can use `-g` or `--generate-sfv` option to generate SFV files.

```
$ crchk -g docs/*.txt
docs/report1.txt    filename    NA    bd3f9af6    NA
docs/report2.txt    filename    NA    7d5e06c3    NA
```

Notice that the output is the same as file verification. The SFV file has the same name of the parent directory and extension `.sfv`. In the example above, a file named `docs.sfv` will be created inside `docs` directory.

You can use `-g` option combined with `-u` to save uppercase CRC codes. You can also combine `-g` with `-r`. In this case, a different SFV file will be created inside each directory containing the CRC codes for each regular file in the directory. Each SFV file created this way will receive the name of its parent directory.

```
$ crchk -gru docs/
docs/report1.txt    filename    NA    BD3F9AF6    NA
docs/report2.txt    filename    NA    7D5E06C3    NA
docs/subdir/report3.txt    filename    NA    D1F4AD85    NA
docs/subdir/report4.txt    filename    NA    84462499    NA
```

In the example above, two SFV will be created: `docs/docs.sfv` and `docs/subdir/subdir.sfv`.

**Important Note:** It is important to know that if a file with the same name already exists, then the SFV file is not created or modified. There is no option for overwriting existing SFV files, if you want to, you have to delete the files manually. This decision was made to avoid unintended modification of existing SFV files, especially when recursive option is enabled.

### Appending CRC to the file name

The option `-a` or `--append-crc` can be used to append calculated CRC-32 to the file name.

```
$ crchk -a docs/*.txt
docs/report1.txt    filename    NA    bd3f9af6    NA
docs/report2.txt    filename    NA    7d5e06c3    NA

$ ls docs/*.txt
docs/report1_[bd3f9af6].txt  docs/report2_[7d5e06c3].txt
```

In the example above we have 2 files, `report1.txt` and `report2.txt`. After running `crchk -a`, the files were renamed to `report1_[bd3f9af6].txt` and `report2_[7d5e06c3].txt` respectively.

You can combine `-a` with `-u` and/or `-r`:

```
$ crchk -aru docs/
docs/report1.txt    filename    NA    BD3F9AF6    NA
docs/report2.txt    filename    NA    7D5E06C3    NA
docs/subdir/report3.txt    filename    NA    D1F4AD85    NA
docs/subdir/report4.txt    filename    NA    84462499    NA

$ ls -R docs/
docs/:
report1_[BD3F9AF6].txt  report2_[7D5E06C3].txt  subdir

docs/subdir:
report3_[D1F4AD85].txt  report4_[84462499].txt
```

In this example, the files were verified recursively and uppercase CRC were appented to their names.


**Important Note:** The CRC will only be appended to the file name when detected CRC is `NA`, otherwise nothing is done. To avoid unintended loss of original CRC information, there is no option for replacing or removing the CRC already present in the file name.

```
$ crchk -aru docs/
docs/report1.txt    sfv    BD3F9AF6    BD3F9AF6    ok
docs/report2.txt    sfv    7D5E06C3    7D5E06C3    ok
docs/subdir/report3.txt    sfv    D1F4AD85    D1F4AD85    ok
docs/subdir/report4.txt    sfv    B9B1DB50    84462499    corrupted

$ ls -R docs/
docs/:
docs.sfv  report1.txt  report2.txt  subdir

docs/subdir:
report3.txt  report4.txt  subdir.sfv
```

In the example above, the CRC were detected from SFV file. Then the calculated CRC were not appended to the file names. As you can see, there is a corrupted file (`docs/subdir/report4.txt`). It would be a mistake to append the calculated CRC to this kind of file.

#### Changing the naming pattern

You can use `-p` (or `--pattern`) option to specify a new naming pattern when `-a` is also specified.

There are some variables you can use to write the pattern:

* `%NAME`: file name (without extension)
* `%EXT`: file extension
* `%CRC`: calculated CRC
* `%%`: literal %

Any other characters will be treated as raw text.

Example:

```
$ crchk -aup '%NAME - (%CRC).%EXT' docs/*.txt
docs/report1.txt    filename    NA    2065074A    NA
docs/report2.txt    filename    NA    0B485489    NA

$ ls -1 docs/*
docs/report1 - (2065074A).txt
docs/report2 - (0B485489).txt
```

Example using `--pattern`

```
$ crchk -au --pattern='%NAME - (%CRC).%EXT' docs/*.txt
docs/report1.txt    filename    NA    2065074A    NA
docs/report2.txt    filename    NA    0B485489    NA

$ ls -1 docs/*
docs/report1 - (2065074A).txt
docs/report2 - (0B485489).txt
```

