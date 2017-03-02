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

    #include <regex>
    auto const result = regex_replace(str,std::regex("<"),"&lt;")

### Check if a string matches a regex

    #include <regex>
    if (std::regex_match(string,std::regex("[a-zA-Z]*")) {
        cout << "Matches\n";
    }

### Load a file into memory

    #include <fstream>
    std::ifstream ifs(file);
    std::string const orig(
        std::istreambuf_iterator<char>(ifs),
        std::istreambuf_iterator<char>()
    );

### Copy all from one stream into another

    std::istream in = ...;
    std::ostream out = ...;
    out << in.rdbuf();

### Make a long random string

    #include <fstream>
    std::string longstr;
    {
        std::ifstream ifs("/dev/urandom",std::ios::binary);
        std::istream_iterator<char> isi(ifs);
        std::copy_n(isi,
            10'000'000,
            std::insert_iterator<std::string>(longstr,longstr.begin()));
    }

### Create all subdirectories required for a file

    #include <boost/filesystem.hpp>
    boost::filesystem::create_directories(boost::filesystem::path(file).parent_path());

### Get current local time as a struct tm

    #include <boost/date_time/posix_time/posix_time.hpp>
    auto const tm = boost::posix_time::to_tm(boost::posix_time::second_clock::local_time());

### Load a URL with cpp-netlib

    boost::network::http::client::request request("http://...");
    request << boost::network::header("Connection", "close");
    auto const result = body(boost::network::http::client().get(request));

## <a name="git"></a> git

### Create the “git godlog” command

    git config --global alias.godlog "log --graph --oneline --decorate"

## <a name="cmake"></a> cmake

### Same output directory for all sub-projects

    set (EXECUTABLE_OUTPUT_PATH "${CMAKE_SOURCE_DIR}/build")

### Use compiler cache (ccache)

    find_program (CCACHE_FOUND ccache)
    if (CCACHE_FOUND)
        set_property(GLOBAL PROPERTY RULE_LAUNCH_COMPILE ccache)
    endif (CCACHE_FOUND)

## <a name="linux"></a> Linux

### Natural Scrolling in Linux

Put the following into /usr/share/X11/xorg.conf.d/20-natural-scrolling.conf, then reboot:

    Section "InputClass"
            Identifier "Natural Scrolling"
            MatchIsPointer "on"
            MatchDevicePath "/dev/input/event*"
            Option "VertScrollDelta" "-1"
            Option "HorizScrollDelta" "-1"
            Option "DialDelta" "-1"
    EndSection

Source: <https://kofler.info/natural-scrolling-mit-dem-mausrad/#more-1956>
