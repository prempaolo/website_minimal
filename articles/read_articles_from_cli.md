Read your articles from CLI and zathura

[Newsboat](https://newsboat.org/) is a great tool to manage your rss feeds from the command line. However, it just displays the content of a feed and, most of the time, this is just a minimal part of the entire article. I didn't like to open the browser any time I wanted to read the full article and I started to look for some way to solve this issue. There is a tool made by Mozilla and called [readability](https://github.com/mozilla/readability), which parses the html of an article and extracts only the useful informations. I tried to combine it with [pandoc](https://pandoc.org/), a very useful tool to convert file formats using latex and other engines, and the result was awesome. Here is a video showing the final result:

![Article](../data/pics/article.gif)

The video shows that only a minimal part of the entire article is shown within Newsboat, while the full text and images are retireved and then converted into a pdf in the background. The full script can be found [here](https://github.com/prempaolo/dotfiles/blob/master/.local/bin/tools/article_to_pdf), while in this article I will go through its main components. The first thing it does is to call the CLI wrapper of readability to extract all the useful information from the url passed as argument and store it in a temporary html file.
```
readability "$URL" > $HOME/Articles/tmp.html
```
Then, it extracts the title of the article and formats it to be used as the name of the file
```
TITLE="$(head -1 $HOME/Articles/tmp.html | awk -F '1>' '{ print $2 }' | sed 's#&lt;/h##g; s/^ //g; s/ $//; s/ /_/g')"
```
Finally, it converts the html to a pdf using Pandoc. I changed the engine used to convert it and the font for aesthetic preferences.
```
pandoc --pdf-engine=pdflatex -V 'fontfamily:dejavu' "$HOME"/Articles/tmp.html -o "$HOME"/Articles/"$TITLE".pdf
```
To make it work within Newsboat, I inserted the following line in its config file to define a macro:
```
macro a set browser "article_to_pdf -o -u %u"; open-in-browser ; set browser "firefox -new-window %u"
```
To call a macro within Newsboat, you simply press **,** and then the key of the macro you want to execute, in this case **a**. It simply tells Newsboat to open the url (defined in the variable **%u**) using the command specified. After that, it resets the browser variable to the actual command that opens the browser. In this way, when I am using Newsboat and I want to read the full article, I simply press **, + a** and the command to retrieve the article and convert it to pdf is executed.
