# symmusic

Creates pseudo-libraries of music based on tags and using your regular filesystem through using symbolic links.

## Contents

 1. [About](#1-about)
 2. [License](#2-license)
 3. [Prerequisites](#3-prerequisites)
 4. [Installation](#4-installation)
 5. [Usage](#5-usage)
 6. [TODO](#6-todo)

***

## 1. About

Symmusic is meant for poorly-named music collections which you don't want to manipulate, but need to browse in a nice manner. It recursively reads a directory for audio tags and uses them to build a new  directory structure. The bottom-level audio files are then symbolically linked to their originating file. This allows one to keep multiple directories organized by artist, genre, album, etc without much disk overhead even for large music collections.

* Works with mp3, FLAC, OGGVorbis. Can choose 1, 2 or all 3. 
* Command line specification of hierarchy based on audio tags.
* Return simple count of found files and created symbolic links.
* (Option) Symbolically link artwork (.jpg) into new directory.
* (Option) Only query and build links to recently added/modified files.
* (Option) Delete directory with a minimum number of songs (useful for excluding compilations.)
* (Option) Delete broken links and empty directories.
* (Option) Return list of failed creations that can be redirected.

## 2. License

This was originally under the "beerware" license by the original author, reproduced below. 

"THE BEER-WARE LICENSE" (Revision 42):
jmeb wrote this file. As long as you retain this notice you
can do whatever you want with this stuff. If we meet some day, and you think this stuff is worth it, you can buy me a beer in return. 


## 3. Prerequisites

* UNIX-like OS. (Linux, Mac OSX)
* Python (3.x; tested on 3.9)
* [mutagen] -- a python module to handle audio metatdata
* argparse

## 4. Installation

## 5. Usage

* *--dn* : directory names. 
    - each argument will create a directory level. Follows given order.
    - *options*: %a (artist), %l (album), %g (genre), %n (track number), %t (track title), %y (year/date)
    - *note*: albums (%l) will be appended with file extensions.
* *--fn* : File name. 
    - each argument will be added to the file name separated by a hyphen. All files will have proper file extension added.
    - *options*: %a (artist), %l (album), %g (genre), %n (track number), %t (track title), %y (year/date)
* *--dst* : Directory to build new file structure in. 

* *--formats* (*-f*): specify audio formats to search and parse
    - *options*: mp3 , ogg , flac
    - *default*: All (mp3, ogg, flac)
* *--src* : Directory to search for music files.
    - will be searched recursively. 
    - *default*: current working directory.
* *--hours* : Only query and build links to files modified in the last N hours. Takes an integer.
* *-a, --art*: symbolically link .jpg files from origin directories into new structure.
* *-v* : Print a lots of extra about deleted directories, failed symbolic link attempts, and the like. If you use this you'll probably want to redirect output (> log.txt) to a file.
* *-n, --number*: Takes an integer. This integer is the minimum number of songs that a directory must have at the end of creation. Directories with less will be deleted. Useful for excluding compilations from artist lists. Use of this option implies *--clean*.
* *-c, --clean*: Clean given destination directory of broken symbolic links and empty directories.

--lower

## 5a. Use Cases

* Use with [Subsonic] to sort your files in multiple ways.
* Browse a directory you have read-only access to nicely.
* Combine multiple users' poorly organized music folders into one nicely sorted directory without much overhead.

## 5b. Syntax Examples

* Sort only mp3s by: Genre / Artist / Album. Filename is Number - Title, and include album art:

        symmusic.py -a --formats mp3 --dn %g %a %l --fn %t --src /music --dst /by-genre

* Sort all formats by: Year / Genre / Album. Filename is Artist - Title AND redirect failures to a log file:

        symmusic.py -v --dn %y %g %l --fn %a %t --src /music --dst /by-year > failures.log

* Sort by: Artist / Album. File name is Track Number and Title. Delete all directories with less than 3 songs:
        
    
        symmusic.py -n 3 --dn %a %l --fn %n %t --src /music --dst /by-artist
    
* Sort by: Album. Filename is Artist - Year - Title:

        symmusic.py --dn %l --fn %a %y %t --src /music --dst /by-album

## 6. Fork changes

* Crashes related to IOError (broken links)
* Crashes related to mutagen.flac.error
* Python3 upgrade
* added --lower for lowercase
* changed divider to "-" (maybe to make configurable later?)
* removed extension on album as dirname (like, why)
* changed tracknumber to be padded 2 digit of the actual track number
* 00 if no track number, not unknown
* if leading with unknown track number (e.g. ^00-trackname) then omit track# (to make configurable later)
* validation and removal of special characters from filename (to make configurable later)
    specialChars = "/\\*?;\':|\.\"" 
* replaces & with and (to make configurable later)


## 6. TODO

Make some of these decisions above configurable instead of hard-coded.
