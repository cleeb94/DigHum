---
layout: post
title: Webscraping - WGET
subtitle: Spraping "Richmon Times Dispatch"
image: /img/Web-Scraping.jpg
---

### Step-by-Step Explanation

1. Downloading WGET for windows (ZIP-file 64-version from this link: <https://eternallybored.org/misc/wget/>)

2. Copying the content of the file into a new folder which was named *lesson07*

3. Opening the website under the given link: <http://www.perseus.tufts.edu/hopper/collection?collection=Perseus:collection:RichTimes>

4. Opening the cmd-terminal and moving to the folder *lesson07*

5. Scraping the website with WGET, yielding the source-html-page: 
```
 wget http://www.perseus.tufts.edu/hopper/collection?collection=Perseus:collection:RichTimes
```

6. Opening the source-html-page in a text-editor supporting Regular Expressions (for example Sublime)

7. Using Regular Expressions to find all the necessary url-sources:
```
text\?doc=Perseus%atext%3a2006\.05\.\d\d\d\d 
```
or shorter (instructors solution):
```
text\?doc=Perseus[^"]+
```
8. Copying all of the found expressions/links into a new text-file and save it as *practice3.txt*

9. Clicking onto the first issue of the *Richmond Dispatch* on the website

10. Scrolling down to the bottom of the page

11. Hovering above "XML version" in the sentence "An XML version of this text is avalaible..."

12. Comparing the link that appears while hovering with the links extracted and saved in the file *practice3.txt*

13. Using Regular Expressions to edit the url-sources in the file *practice3.txt* accordingly to point 12:

Replace 
```
text\?
```
with
```
www.perseus.tufts.edu/hopper/dltext?
```
and replace 
```
a
```
with
```
A
```
14. Saving the edited file *practice3.txt*

15. Creating a subfolder *practice3* in the folder *lesson07*

16. Opening the terminal and checking that one is in the folder *lesson07* which contains the file *practice3.txt*

17. Scraping the articles from the file *practice3.txt* into the folder *practice3* without repetitions (-nc) through the cmd-terminal:
```
wget practice3.txt -P ./practice3/ -nc
```