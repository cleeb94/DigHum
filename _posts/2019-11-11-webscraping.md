---
layout: post
title: Webscraping - WGET
subtitle: Spraping "Richmon Times Dispatch"
image: /img/Web-Scraping.jpeg
---

### Step-by-Step Explanation

1. Downloading WGET for windows (ZIP-file 64-version from this link: [https://eternallybored.org/misc/wget/][https://eternallybored.org/misc/wget/])
2. Copying the content of the file into the folder which was named *lesson07*
3. Opening the website under the given link: [http://www.perseus.tufts.edu/hopper/collection?collection=Perseus:collection:RichTimes][http://www.perseus.tufts.edu/hopper/collection?collection=Perseus:collection:RichTimes]
3. Opening the cmd-terminal and moving to the folder *lesson07*
4. Scraping the website with WGET, yielding the source-html-page: 
 > wget http://www.perseus.tufts.edu/hopper/collection?collection=Perseus:collection:RichTimes
5. Opening the source-html-page in text-editor supporting Regular Expressions (for example Sublime)
6. Using Regular Expressions to find all the necessary url-sources:
```
text\?doc=Perseus%atext%3a2006\.05\.\d\d\d\d 
```
> or shorter (instructors solution):
```
text\?doc=Perseus[^"]+
```
7. Copying all of them into a new file and save it ax *practice3.txt*
8. Clicking onto the first issue of the *Richmond Dispatch* on the website
9. Scrolling down to the bottom of the page and using the very last grey box: 

In the sentence "An XML version of this text is avalaible..." hovering on the "XML version" which shows a link
10. Comparing this shown link with the links extracted and saved in the file *practice3.txt*
11. Using Regular Expressions to edit the url-sources accordingly to point 10:

> Replace 
```
text\?
```
> with
```
www.perseus.tufts.edu/hopper/dltext?
```
> and replace *a* with *A*
12. Saving the edited file *practice3.txt*
13. Creating a subfolder *practice3* in the folder *lesson07*
13. Opening the terminal and checking that one is in the folder *lesson07* which contains the file *practice3.txt*
13. Scraping the articles from the file *practice3.txt* into the folder *practice3* without repetitions through the cmd-terminal:
> wget practice3.txt -P ./practice3/ -nc