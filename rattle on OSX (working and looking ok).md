in a terminal:

    % brew install libiodbc (for compiling RODBC)
    % brew install brew install homebrew/x11/gtk-chtheme (to get gtk-chteme)

get a proper theme, like http://dgk15.deviantart.com/art/OS-X-Yosemite-theme-0-2-for-Ubuntu-14-04-472626539
download, uncompress it and put the directory in ~/.themes/ (which needs to be created first)

in a terminal, run:

    % /usr/local/Cellar/gtk-chtheme/0.3.1_1/bin/gtk-chtheme 

(to select the Yosemite theme, adjust fonts...)

optionally, to disable icons on buttons:
     
     % echo 'gtk-button-images = 0' > ~/.gtkrc.mine

in R:

    > install.packages("rattle", dep=c("Suggests"))
(answer yes, ggobi will fail but don't care for now)

then to launch it:

    > Sys.setenv(LANGUAGE="en")
    > library(rattle)
    > rattle()


tips:

<http://rattle.togaware.com/rattle-install-troubleshooting.html>
<http://stackoverflow.com/questions/24531085/cant-get-gtk-themes-to-run-on-os-x>
<http://rpackages.ianhowson.com/cran/RGtk2/man/gtk-Resource-Files.html>
<http://apple.stackexchange.com/questions/152850/change-gtk-theme-for-x11-apps>
<http://rattle.togaware.com/rattle-install-mac.html>
