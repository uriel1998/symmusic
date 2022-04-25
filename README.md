#This Fork

Fixes:

Python3 upgrade

Fixes:

* Crashes related to IOError (broken links)
* Crashes related to mutagen.flac.error

#Overview

Symmusic is meant for poorly-named music collections which you don't want to manipulate, but need to browse in a nice manner. It recursively reads a directory for audio tags and uses them to build a new  directory structure. The bottom-level audio files are then symbolically linked to their originating file. This allows one to keep multiple directories organized by artist, genre, album, etc without much disk overhead even for large music collections.

###Dependencies

* UNIX-like OS. (Linux, Mac OSX)
* Python (2.6 and more)
* [mutagen][] -- a python module to handle audio metatdata

#Features

* Works with mp3, FLAC, OGGVorbis. Can choose 1, 2 or all 3. 
* Command line specification of hierarchy based on audio tags.
* Return simple count of found files and created symbolic links.
* (Option) Symbolically link artwork (.jpg) into new directory.
* (Option) Only query and build links to recently added/modified files.
* (Option) Delete directory with a minimum number of songs (useful for excluding compilations.)
* (Option) Delete broken links and empty directories.
* (Option) Return list of failed creations that can be redirected.

###TODO

* Longer term, find windows workaround for symbolic links
* Option to write relative rather than absolute path links.
* Allow complex directory specification (e.g. multiple tags)

#Use

###Command line options

####Required

* *--dn* : directory names. 
	- each argument will create a directory level. Follows given order.
	- *options*: %a (artist), %l (album), %g (genre), %n (track number), %t (track title), %y (year/date)
	- *note*: albums (%l) will be appended with file extensions.
* *--fn* : File name. 
	- each argument will be added to the file name separated by a hyphen. All files will have proper file extension added.
	- *options*: %a (artist), %l (album), %g (genre), %n (track number), %t (track title), %y (year/date)
* *--dst* : Directory to build new file structure in. 

####Optional

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

###Examples

###Use Cases

* Use with [Subsonic][] to sort your files in multiple ways.
* Browse a directory you have read-only access to nicely.
* Combine multiple users' poorly organized music folders into one nicely sorted directory without much overhead.

###Syntax Examples

* Sort only mp3s by: Genre / Artist / Album. Filename is Number - Title, and include album art:

		symmusic.py -a --formats mp3 --dn %g %a %l --fn %t --src /music --dst /by-genre

* Sort all formats by: Year / Genre / Album. Filename is Artist - Title AND redirect failures to a log file:

		symmusic.py -v --dn %y %g %l --fn %a %t --src /music --dst /by-year > failures.log

* Sort by: Artist / Album. File name is Track Number and Title. Delete all directories with less than 3 songs:
		
	
		symmusic.py -n 3 --dn %a %l --fn %n %t --src /music --dst /by-artist
	
* Sort by: Album. Filename is Artist - Year - Title:

		symmusic.py --dn %l --fn %a %y %t --src /music --dst /by-album

####Caveats / Oddities

* All albums are appended with a tag of what filetype the contain. So "The White Album" becomes "The White Album [.mp3]". This is something my own collection needs because I often have flac and mp3 versions of the same files. 

* When a metadata tag can't be read it *should* return 'Unknown'. The effect is you might have (large) 'Unknown' directories in each level of your hierarchy. 

* I wouldn't put much faith in the 'files created' number that is printed. Its a dumb counter. 

###Comparison to similar utitilies

* [pytagsfs][] is an awesome already implemented version of what I'm trying to do. Its much more flexible then what this script will ever be. For instance, it lets your edit your metadata on the fly by simply rearranging material in the virtual filesystem, and can smartly exclude compilations.
	- *Issues:* Pytagsfs runs through fuse and stores its map of metadata in memory. For small collections this is fine, but this can be resource intensive on large, multiply sorted collections. (Especially if you don't really need all the functionality.) 
	- *Comparison to symmusic:* Symmusic generates a directory structure and then is done. It uses less resources, but is also less flexible. Whereas pytagsfs generates a read-write directory structure, symmusic's is basically read-only.

* Music organizers: a number of utilities exist that will reorganize a directory of music files based on their tags.
	- *Issues*: This modifies you directory structure and filenames. 
	- *Comparison to symmusic:* Reorganizers of this sort lock you into one directory structure (unless you keep multiple copies of the audio files around). Symmusic allows you to have multiple views with little overhead. Furthermore it never touches your original data. 


[pytagsfs]: http://www.pytagsfs.org/
[mutagen]: http://code.google.com/p/mutagen/
[unidecode]: http://pypi.python.org/pypi/Unidecode/
[Subsonic]: http://www.subsonic.org/

