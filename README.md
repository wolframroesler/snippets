# Code Snippets

Small snippets of code that I refer to in my daily work.

## Table of Contents

* [C++](#cpp)
* [git](#git)
* [cmake](#cmake)
* [Linux](#linux)

## <a name="cpp"></a> C++

These assume that you are using C++14 and boost.

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
std::ifstream ifs(file);
std::string const orig(
    std::istreambuf_iterator<char>(ifs),
    std::istreambuf_iterator<char>()
);
```

### Copy all from one stream into another

```cpp
std::istream in = ...;
std::ostream out = ...;
out << in.rdbuf();
```

### Make a long random string

```cpp
#include <fstream>
std::string longstr;
{
    std::ifstream ifs("/dev/urandom",std::ios::binary);
    std::istream_iterator<char> isi(ifs);
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
boost::network::http::client::request request("http://...");
request << boost::network::header("Connection", "close");
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

## <a name="linux"></a> Linux

### Natural Scrolling in Linux

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
