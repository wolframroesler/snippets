# Code Snippets

Small snippets of code that I refer to in my daily work.

## Table of Contents

* [C++](#cpp)
* [git](#git)
* [cmake](#cmake)
* [bash](#bash)
* [Linux](#linux)

## <a name="cpp"></a> C++

These assume that you are using C++14 and boost.

### Activate std::string literals

... like `auto s = "this is a std::string"s;`

```cpp
using namespace std::literals;
```

### Activate std::chrono literals

... like `auto microseconds = 1000us;`

```cpp
using namespace std::chrono_literals;
```

### Replace a string with regex

```cpp
#include <regex>
auto const result = regex_replace(str,std::regex("<"),"&lt;")
```

### Check if a string matches a regex

```cpp
#include <regex>
if (std::regex_match(string,std::regex("[a-zA-Z]*")) {
    cout << "Matches\n";
}
```

### Load a file into memory

```cpp
#include <fstream>
auto ifs = std::ifstream(file);
auto const data = std::string(
    std::istreambuf_iterator<char>(ifs),
    std::istreambuf_iterator<char>()
);
```

### Copy all from one stream into another

```cpp
auto in = std::istream(...);
auto out = std::ostream(...);
out << in.rdbuf();
```

### Make a long random string

```cpp
#include <fstream>
std::string longstr;
{
    auto ifs = std::ifstream("/dev/urandom",std::ios::binary);
    auto isi = std::istream_iterator<char>(ifs);
    std::copy_n(isi,
        10'000'000,
        std::insert_iterator<std::string>(longstr,longstr.begin()));
}
```

### Create all subdirectories required for a file

```cpp
#include <boost/filesystem.hpp>
boost::filesystem::create_directories(boost::filesystem::path(file).parent_path());
```

### Get current local time as a struct tm

```cpp
#include <boost/date_time/posix_time/posix_time.hpp>
auto const tm = boost::posix_time::to_tm(boost::posix_time::second_clock::local_time());
```

### Load a URL with cpp-netlib

```cpp
auto request = boost::network::http::client::request("http://...");
request << boost::network::header("Connection","close");
auto const result = body(boost::network::http::client().get(request));
```

### Convert a file descriptor into an I/O stream

```cpp
#include <iostream>
#include <boost/iostreams/stream.hpp>
#include <boost/iostreams/device/file_descriptor.hpp>

// Output (file opened for writing)
int fd = ...;
boost::iostreams::file_descriptor_sink snk(fd,boost::iostreams::close_handle);
boost::iostreams::stream<boost::iostreams::file_descriptor_sink> os(snk);
os << "Hello World\n";

// Input (file opened for reading)
int fd = ...;
boost::iostreams::file_descriptor_source src(fd,boost::iostreams::close_handle);
boost::iostreams::stream<boost::iostreams::file_descriptor_source> is(src);
is >> myvariable;
```

## <a name="git"></a> git

### Create the “git godlog” command

```sh
git config --global alias.godlog "log --graph --oneline --decorate"
```

### Manage Libre Office files in git

Requires git 1.6.1 or later.

Add the following to `~/.gitconfig`:

```
[diff "odf"]
      textconv=odt2txt --stdout
```

Add the following to the `.gitattributes` file in the project root:

```
*.ods diff=odf
*.odt diff=odf
*.odp diff=odf
```

If `git diff` displays the following error:

```
Error: Unable to connect or start own listener. Aborting.
fatal: unable to read files to diff
```

then type `unoconv -l` and retry.

More information:

* http://www-verimag.imag.fr/~moy/opendocument/
* https://git-scm.com/book/en/v2/Customizing-Git-Git-Attributes ("Diffing Binary Files" section)

### Put git version number into PDF

... that is created from a Libre Office document which is managed in git as described in the previous section, using the tag-based version number generated by `git describe`. Tested with Libre Office 5 in Linux.

Preparation (once per document):

1. Open the document in LibreOffice Writer
1. Move cursor to where the version number is to be displayed
1. Insert → Field → More fields ...
1. Variables → Set variable, Name: version, Value: 0.0, Format: Text, Insert, Close
1. To show the version number elsewhere:  Insert → Field → More fields ... → Variables → Show variable, version, Insert, Close
1. Close and save the document
1. Add/commit the document to git

To convert the document into a PDF, replacing the "0.0" placeholder by the current git version number:

```sh
$ odt2pdf -o myname.pdf -F version=$(git describe --dirty --always) filename.odt
```

About tag-based git version numbers: https://git-scm.com/docs/git-describe

## <a name="cmake"></a> cmake

### Same output directory for all sub-projects

```cmake
set (EXECUTABLE_OUTPUT_PATH "${CMAKE_SOURCE_DIR}/build")
```

### Use compiler cache (ccache)

```cmake
find_program (CCACHE_FOUND ccache)
if (CCACHE_FOUND)
    set_property(GLOBAL PROPERTY RULE_LAUNCH_COMPILE ccache)
endif (CCACHE_FOUND)
```

## <a name="bash"></a> bash

### Shell Prompt

Show the last command's exit status in the shell prompt, and show the prompt with a dark background to make it stand out better. Put the following into your .profile or .bashrc.

```sh
PS1BEFORE=$(tput sgr0)$(tput rev)$(tput setaf 4)
PS1AFTER=$(tput sgr0)
PS1='\[$PS1BEFORE\]$? [\h:\w]\[$PS1AFTER\] \$ '
```

![Screenshot showing bash prompt](img01.png)
 
### Put host name and work directory into terminal window title

```sh
PS1="\[\e]0;\h:\w\a\]$PS1"
```

## <a name="linux"></a> Linux

### Natural Scrolling

Put the following into /usr/share/X11/xorg.conf.d/20-natural-scrolling.conf, then reboot:

```
Section "InputClass"
        Identifier "Natural Scrolling"
        MatchIsPointer "on"
        MatchDevicePath "/dev/input/event*"
        Option "VertScrollDelta" "-1"
        Option "HorizScrollDelta" "-1"
        Option "DialDelta" "-1"
EndSection
```

Source: <https://kofler.info/natural-scrolling-mit-dem-mausrad/#more-1956>

### Mount Nextcloud

Access the files in your Nextcloud without syncing them to your harddisk, using Nextcloud's WebDAV interface. Doesn't require disk space to store your Nextcloud files locally. Doesn't use the Nextcloud client software.

Tested with Ubuntu 16.04. Will probably work with Owncloud or any other WebDAV-based file service.

The following examples assume that

* `mycloud.example.com` is your Nextcloud server
* `myname` is your Nextcloud user name
* `mypassword`is your Nextcloud password

Preparation:

```sh
$ sudo apt install ca-certificates
$ sudo apt install davfs2
$ sudo mkdir /mnt/myname
$ sudo usermod -aG davfs2 $USER
```

Add the following line to `/etc/fstab`:

```
https://mycloud.example.com/remote.php/webdav /mnt/myname davfs user,noauto 0 0
```

If you want read-only access (you can read your cloud files but not change them):

```
https://mycloud.example.com/remote.php/webdav /mnt/myname davfs user,noauto,ro,dir_mode=555,file_mode=444 0 0
```

Add the following to `/etc/davfs2/secrets`:

```
/mnt/myname myname mypassword
```

Note: Every user on your Linux machine can mount your Nextcloud files, which may or may not be desired.

Finally, to mount your Nextcloud files:

```sh
$ sudo mount /mnt/myname
```

More information: https://wiki.ubuntuusers.de/WebDAV/

---
*Wolfram Rösler • wolfram@roesler-ac.de • https://twitter.com/wolframroesler • https://github.com/wolframroesler*
