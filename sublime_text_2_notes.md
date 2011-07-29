## User File Settings

    {
        "font_face": "Meslo LG S DZ",
        "font_size": 12,
        "tab_size": 4,
        "translate_tabs_to_spaces": true,
        "use_tab_stops": true
    }

## User Global Settings

Added the [Soda Theme](https://github.com/buymeasoda/soda-theme) to make things look a little nicer.

    {
        "theme": "Soda Dark.sublime-theme"
    }

## Setup Sublime Command Line Helper

    ln -s "/Applications/Sublime Text 2.app/Contents/SharedSupport/bin/subl" ~/usr/local/bin/subl

Now you can load a folder into Sublime from the command line.

    cd ~/Sites/mysite
    subl .

## Packages I've Added

- [Django4ST2](https://github.com/squ1b3r/Django4ST2)
- [sublime-2-zencoding](https://bitbucket.org/sublimator/sublime-2-zencoding) ([usage notes](http://www.sublimetext.com/forum/viewtopic.php?f=2&t=580&p=10654#p10654))

## Things TextMate had that I Miss

- Git commit messages had syntax highlighting

## Wish List

- I wish I could hit CMD+p and type a path to a folder and then hit enter to have it loaded to my project.
- It would be nice if you could right click on a folder and have a choice to "open in terminal"
- Typing a few letters of a variable, method or object and then hitting escape in a python or javascript file would complete the name of what you where typing.