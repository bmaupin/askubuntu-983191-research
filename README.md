### Summary

Bullet points in Microsoft Word Online don't properly show up in Firefox on Linux.


### Workaround

1. Verify you're affected by the issue

    [https://bmaupin.github.io/askubuntu-983191-research/test-bullet-points.html](https://bmaupin.github.io/askubuntu-983191-research/test-bullet-points.html)

1. Apply the workaround

    ```
    mkdir -p ~/.fonts/
    wget https://source.winehq.org/git/wine.git/blob/HEAD:/fonts/symbol.ttf -O ~/.fonts/symbol.ttf
    wget https://source.winehq.org/git/wine.git/blob/HEAD:/fonts/wingding.ttf -O ~/.fonts/wingding.ttf
    wget https://bmaupin.github.io/askubuntu-983191-research/symbol_msfontservice.ttf -O ~/.fonts/symbol_msfontservice.ttf
    wget https://bmaupin.github.io/askubuntu-983191-research/wingdings_msfontservice.ttf -O ~/.fonts/wingdings_msfontservice.ttf
    fc-cache -f -v
    ```

1. Verify that it worked

    [https://bmaupin.github.io/askubuntu-983191-research/test-bullet-points.html](https://bmaupin.github.io/askubuntu-983191-research/test-bullet-points.html)


### Details

Instead of standard Unicode characters for bullet points, Microsoft Word (assumedly for compatibility reasons) uses special characters from 3 different font sets to generate its 3 different levels of fonts:

- First level
    - Character: unicode f0b7 ()
    - Font: Symbol
- Second level
    - Character: o
    - Font: Courier New
- Third level
    - Character: unicode f0a7 ()
    - Font: Wingdings

Linux doesn't typically come with any of these fonts as these are Microsoft fonts. Because the second level is just an "o", it shows up fine, but the other two don't show up properly in Firefox.

Microsoft's workaround is to provide these fonts as downloadable fonts using the following CSS:

```css
@font-face {
  font-family: symbol;
  src: url('https://fs.microsoft.com/fs/4.2/rawguids/26522609597');
}
@font-face {
  font-family: Courier New_MSFontService;
  src: url('https://fs.microsoft.com/fs/4.2/rawguids/38459451639');
}
@font-face {
  font-family: Wingdings_MSFontService;
  src: url('https://fs.microsoft.com/fs/4.2/rawguids/37798520877');
}
```

However, Firefox isn't able to handle the fonts and returns the following errors in the browser console:

```
downloadable font: not usable by platform (font-family: "Wingdings_MSFontService" style:normal weight:400 stretch:100 src index:0) source: https://fs.microsoft.com/fs/4.2/rawguids/37798520877
downloadable font: not usable by platform (font-family: "symbol" style:normal weight:400 stretch:100 src index:0) source: https://fs.microsoft.com/fs/4.2/rawguids/26522609597
```

The fonts can be installed (using open-source replacements from Wine) locally:

```
mkdir ~/.fonts/
wget https://source.winehq.org/git/wine.git/blob/HEAD:/fonts/symbol.ttf -O ~/.fonts/symbol.ttf
wget https://source.winehq.org/git/wine.git/blob/HEAD:/fonts/wingding.ttf -O ~/.fonts/wingding.ttf
fc-cache -f -v
```

However Word Online is specifically loading the "symbol" family as a web font, so it overrides the local font. Because the downloadable font fails, neither one shows up.


### Steps used to change font family name for workaround:

1. Download symbol.ttf from https://source.winehq.org/git/wine.git/blob/HEAD:/fonts/symbol.ttf

1. Open symbol.ttf with FontForge

1. *Element* > *Font Info*

1. *Family Name*: Symbol_MSFontService > *OK*

1. *File* > *Generate Fonts*

1. Change filename to symbol_msfontservice.ttf

1. Change type to *TrueType (Symbol)*

1. *Generate* > *Generate*

1. Repeat these steps for Wingdings, with the following differences:
    - Download winging.ttf from https://source.winehq.org/git/wine.git/blob/HEAD:/fonts/wingding.ttf
    - Change the family name to Wingdings_MSFontService
    - Save the file as wingdings_msfontservice.ttf
