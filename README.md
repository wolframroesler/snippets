# C++ Code Snippets

Small snippets of code that I refer to in my daily work. Well, some are not about C++, but they will stay here until I find a better place.

## Replace a string with regex

    #include <regex>
    auto result = regex_replace(str,std::regex("<"),"&lt")

## Check if a string matches a regex

    #include <regex>
    if (std::regex_match(string,std::regex("[a-zA-Z]*")) {
      cout << "Matches\n";
    }

## Load a file into memory

    #include <fstream>
    std::ifstream ifs(file); std::string const orig((std::istreambuf_iterator<char>(ifs)),std::istreambuf_iterator<char>());

## Make a long random string

    #include <fstream>
    std::string longstr;
    {
      std::ifstream ifs("/dev/urandom",std::ios::binary);
      std::istream_iterator<char> isi(ifs);
      std::copy_n(isi,10'000'000,std::insert_iterator<std::string>(longstr,longstr.begin()));
    }

## Load a URL with cpp-netlib

    boost::network::http::client::request request(URL(path));
    request << boost::network::header("Connection", "close");
    return body(boost::network::http::client().get(request));

## Create the “git godlog” command

    git config --global alias.godlog "log --graph --oneline --decorate"

## Natural Scrolling in Linux

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

## cmake: Same output directory for all sub-projects
    set(EXECUTABLE_OUTPUT_PATH "${CMAKE_SOURCE_DIR}/build")

