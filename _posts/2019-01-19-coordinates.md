---
layout: post
title: Text to Map
subtitle: using Python and QGIS
image: /img/1861_world.png
---

In the following blog post the steps towards a map built out of place names mentioned in the "Dispatch" are being described.

For this, there is always a patch of code shown, followed by a description/explanation/comment about/on it.

First all the tgn numbers within the articles had to be extracted and their abundance frequencies for each year were calculated. 

```
import re
import os

source_location = "./practice3/"   # the folder in which the "dispatch" is
target_location = "./TSV_with_tgn_only_articles/" # the folder in which the new files will be saved 

lof=os.listdir(source_location) # getting all files from a folder
```
Imports the necessary libraries, sets location where the files, that we need for working, are and the location where the solution files should be saved
Then there follows a line which gets all the files and safes them in the variable "lof".

```
def tgn(year):
    resultsTSV= []  
    dicFreq = {}
    for f in lof: # looping through all the files
```
My first try was without a function. I had a global dictionaries for each year  (dic_1860, dic_1861, etc.) and saved the counts in them if the issue year (extracted for each issue) was the same as the year for this global dictionary. I tried to use the code "eval(…)" which should interpret the parts within the brackets as python code. 
The code "eval(“dic_” + int(issue_year))"  did result into "dic_1860" when putting a print statement around it, however, it still did not recognize it as the dictionary created above. 
My program did run before last class, but the output numbers were off when comparing the frequencies of single files with the ones over a whole year. Therefore, the approach with a function was adopted. 

The code defines a function with the input variable “year” and creates an empty list for the results (like in the homework before, to get a clearer overview) and an empty dictionary. Then it starts looping through all the files in "lof".

```
	with open(source_location + f, "r", encoding="utf8") as f1:
            text0 = f1.read() # reads the file in
```
Opening each file one after another, the program now reads the files into the variable "text0", just like in the homeworks before.

```
        ## Extracting the date of the Issue = date of the articles

            date_section = re.search(r"<titlePage>([\n-^\w—]*)</titlePage>", text0).group(1) #finds the section where the date is in
            issue_date=re.search(r"<date value=\"([\d-]*)\" authname",date_section).group(1) #extracts the date of the issue
            issue_year=re.search(r"(\d\d\d\d)",issue_date).group(1)

```

Here, the date section of the issue is looked for within the section "titlePage" and the issue date itself is extracted. This is essentially the same code as for the previous homework. 
In addition, specifically the issue year is saved as a separate variable.

```
			results = re.split(r"<div3 ", text0) # split the text into the div3 parts

            for r in results[1:]: #first one is a "library"
           
                r ="<div3 " + r # reattach so that it can be canceled out later on
    
            ## type
    
                try:
                    item_type=re.search(r"type=\"(\w*)\" ",r).group(1) #check if it has a type, save it
                except:
                    item_type="noItemType"
	

```
In order to be able to check whether the item of the issue is a article or not, the type of the item has to be extracted as well. 
For this, the code used of the previous homework is taken as reference for constructing the block of code where the items are split and then a loop runs through each of them. 
The readding of the "<div3" is not necessary in this case, but for completeness it is included. 
Then the program tries to find an item type. In the solution of the instructor all the items of the issues were included which means that also the advertisments etc. are included. 
This does not give a correct picture of the issue at hand, as ads are usually local and do not tell us something about the news coverage. 
Therefore, in order to improve the accuracy, I exclude them in my solution by filtering the items by their type, namely, only items with the type “article” are included. 
I performed both filtering methods, an examples of the obtained numbers is:

### 1861 for the tgn-number 7007919: 

8953 counts when using all items

61 counts when using onyl the articles 

### 1862 for the tgn-number 7013964: 

8723 counts when using all items

291 counts when using onyl the articles


```
				if item_type == "article":  
					## trying to find the tgn numbers
					if int(issue_year) == int(year):
						placesNames = re.findall(r"<placeName(.*)</placeName>",text0) # finds section
						for j in placesNames:
							places= re.findall(r"key=\"tgn,([\d-]*)\"", j) # extracts tgn number
							for i in places:
                            
								if i in dicFreq:
									dicFreq[i] += 1
								else:
									dicFreq[i] = 1

```
Only if the item-type really is an article, the program will run the code.
It first checks if the issue year fits the input variable, if so it continuous to find all place names which is in contrast with the instructor’s solution.
Those place name sections are then checked for tgn numbers. 
Now the dictionary is used in order to check whether a tgn number has been found before. 
If so, the corresponding entry in the dictionary is incremented, if not, the entry is being created.
In my program and in the instructor’s program, places that have not been equipped with the corresponding tgn number will not be taken into account. 
The way my program is structured (first extracting the section “placeName” and then the tgn numbers), 
it is possible to keep track of the incidents where there is a “placeName” without a tgn number. 
This way it is at least possible to see how many cases are there and one can judge if it is too much for the issue at hand. 
If so, it would be necessary to extract the place names themselves and then match those with the list of tgn numbers 
(in this case one would need to use the place names column instead) and their coordinates. 

```
    for key, value in dicFreq.items():
        if value > 1: # this will exclude items with frequency 1
            newVal = "%09d\t%s" % (value, key)
            # newVal will looks like: `000005486 TAB tgn      
            resultsTSV.append(newVal)    

```
Now, all the values which are only 1 are being excluded, as proposed in class and the format for the necessary information is assigned.            
```
    resultsTSV = sorted(resultsTSV, reverse=True)
    #print(len(resultsCSV)) # will print out the number of items in the list
    resultsToSave = "\n".join(resultsTSV)
    header = "freq\ttgn\n"
    new_file = str(year) + "tgn.tsv"

```
The results are being sorted by size, and then saved. 
A header is being created, as this is needed for the QGIS program later on.
```
with open(target_location+new_file, "w", encoding ="utf8") as f2: #open in target folder
        f2.write(header+resultsToSave) #write in one file all the information

```
In this step the file in which all the tgn numbers should be safed is being created and saved under the correct name within the assigned location.
```
print("1860")
tgn(1860)

print("1861")
tgn(1861)

print("1862")
tgn(1862)

print("1863")
tgn(1863)

print("1864")
tgn(1864)

print("1865")
tgn(1865)

```
In order to keep track of the process, a print statement of the year being checked is created, after that the function with the year of interested as the input variable is called. 



Now, the corresponding coordinates for the places had to be found. For this, the given files were used and one was opened in order to check what the structure of the files is like.

```
import re
import os

source_location = "./tgn_xml_1219/"
target_location = "./coordinates/"

```
Again, the necessary libraries have been loaded and the source location is assigned. 
In addition and in contrast to the instructor’s solution, also a target location is assigned in order to keep a better overview through a folder system.
```
def coordinates (source_location):
    
    lof = os.listdir(source_location)
    coor_list = []
    no_coor_list = []

```
Another function is created that takes the source location as an input variable. 
Then the files within this source location are loaded and two empty lists are created, 
which should serve for saving the tgn numbers and their corresponding information.
```
	for f in lof: # looping through all the files
    
        with open(source_location + f, "r", encoding="utf8") as f1:
            text0 = f1.read() # reads the file in

```
Looping through every single file within the variable "lof", 
the program opens this file and then reads the content into the variable "text0",
like in the other programs before.
```
            results = re.split("</Subject>", text0) 

```
When checking the structure of the files, it becomes clear that it needs to be splited into sections of subjects, 
which has been done here. For better comparisment and a clearer structure, I decided to use the same variable names as in the previous programs. 
```
            for r in results: 
           
                r =re.sub("\n +", "", r)
                
                if "Subject_ID" in r:
                    ID = re.search(r"Subject_ID=\"(\d+)\"",r).group(1)
                    #try:
                    name = re.search(r"<Term_Text>([^<]+)</Term_Text>",r).group(1)
                    #except:
                     #   name = "NoName"


```
Now, the line breaks are exchanged and the program starts looking for the section “Subject_ID”. 
If it is found, the code, extracting the ID is executed. 
Also the name, meaning the corresponding place name, is extracted. 
First, I tried to use another regular expression (namely: "(\w*)") 
which worked quite well when trying it within "sublime" with the text. 
However, when running it in the python program, it quickly became clear that it was thrown off, as it complained that there sometimes was no such thing and that it could not match it and therefore not safe anything into the variable. 
This is why, I went back to the regular expression seen above.      
```
					if "<Coordinates>" in r:
                        latGr = re.search(r"<Latitude>(.*)</Latitude>", r).group(1)
                        lat = re.search(r"<Decimal>(.*)</Decimal>", latGr).group(1)
                        
                        lonGr = re.search(r"<Longitude>(.*)</Longitude>", r).group(1)
                        lon = re.search(r"<Decimal>(.*)</Decimal>", lonGr).group(1)
                    else:
                        lat = "NA"
                        lon = "NA"


```
As not all the entries have corresponding coordinates, 
it is first looked if there was a section of coordinates. 
If so, these informations are extracted using regular expressions that extract the two different formats of the coordinates. 
If the program does not find coordinates, it saves the values “NA” for the variables that stand for the coordinates. 
```
					if lat == "NA":
                        no_coor_list.append("\t".join([ID,name,lat,lon]))
                    else:
                        coor_list.append("\t".join([ID,name,lat,lon]))


```
If there are no coordinates, the ID, name and “NA” information of this entry are assigned to one list. 
However, if there are coordinates, all this information is included in another list. 
It should be noted that here the decimal coordinates are used and saved. 
However, they seem to be the rounded version of the coordinates including degrees, minutes and seconds. 
This means that they are less accurate, which should be kept in mind when looking at the obtained results. 
If they are not accurate enough, one could go back here and choose to take the other coordinates instead.            
```
    header = "ID\tplacename\tlat\tlon\n"
    new_file = "tgn_coordinates.tsv"
    new_file_no = "tgn_coordinates_no.tsv"


```
A header is created, explaining what each column stands for and the names of the new files are being created,clearify immediately what their content is. 
In contrast to the instructor’s solution this is done explicitly, 
so that one could go back and just change the name of this variable without having to change anything else about the code and also to keep the same structure as with the other programs so far. 
```
    with open(target_location+new_file, "w", encoding ="utf8") as f2: #open in target folder
        f2.write(header+"\n".join(coor_list)) #write in one file all the information
        
    with open(target_location+new_file_no, "w", encoding ="utf8") as f3: #open in target folder
        f3.write(header+"\n".join(no_coor_list)) #write in one file all the information


```
Two files, one with the tgn numbers with coordinates and one with the tgn 
numbers without coordinates, are created and saved in their corresponding 
target locations. 
```
coordinates(source_location)
```
Now, the function is called with the source location as the input variable. 




The third program created in order to solve the given homework problems is described in the following and matches the obtained list of tgn numbers with their frequencies per year of the dispatch and the list of coordinates. 

```
target_location = "./matching/"
```
As the libraries re and os are never used in this separate program, 
they are not imported (in contrast to the instructor’s solution) 
to make the program more readable and faster. 
Also, again a target location iss assigned where the results should be saved to. 
```
def loading_coordinates (coordinates):
    with open(coordinates, "r", encoding = "utf8") as f1:
        text0 = f1.read().split("\n")
        
        dic_coor = {}

```
A function taking a coordinate file as input is created, 
which opens the file and reads it into the variable "text0" like always. 
An empty dictionary, in which the merged information should be saved in the 
end, is created as well. 
```
	for r in text0:
            
            r = r.split("\t") # split it at tab
            dic_coor[r[0]]= r # loading in the tgn numbers

    return(dic_coor)


```
Running through the text file, it is split into the different parts 
which have been separated with a tab (namely the columns). 
Now, the tgn number (saved in r[0]) is loaded into the dictionary and the 
corresponding information along with it. 
```
### matching process from coordinates to tgn numbers of the articles

def matching(freq_files, dic_coor):
    with open(freq_files, "r", encoding = "utf8") as f1:
        text0 = f1.read().split("\n") # split after each line

```
Another function is created taking the input of a frequency file and a 
dictionary to match with. 
Opening the frequency file and reading it into a variable happens next. 
It is split after each new line. 
```
		data_list = []
        data_list_no = []
        count= 0


```
Two empty lists for the two different versions (with or without coordinates) 
are created and a counter is initialized to count how many items could not 
be matched.
```
        for r in text0[1:]:
            ID = r.split("\t")[1] # tgn number
            freq = r.split("\t")[0] #freq


```
Looping through the text file (excluding the first row, as this is the header line), 
the 2nd column is saved in the variable "ID", as it contains the tgn number and 
the 1st column is saved in the variable "freq", as it contains the frequency per year.      
```
            if ID in dic_coor: # checking if matches
                val = "\t".join(dic_coor[ID])
                val = val + "\t" + freq


```
Now it is checked if the tgn number also exists within the dictionary, 
and, if so, the ID and the frequency is being saved. 
My first impulse was to look through the list of all coordinates and 
tgn numbers and then match it with the frequency file, 
however, this includes unnecessary loopings as there are a lot more 
tgn numbers in general than used in the frequency file. 
It is much more efficient to do it the other way around, 
as has been done here.  
```
                if "\tNA\t" in val:
                    data_list_no.append(val)
                else: 
                    data_list.append(val)

```
Here, it is checked if there is a “NA” in "val" and if so, 
it is appended to the corresponding list. 
It should be noted, that this step could have been easily avoided if done differently in the program before. 
Instead of saving all the tgn numbers and their “coordinates” 
(whether real coordinates or “NA”) into one file and only saving 
the “NA” ones into a separate file, one could have also created a 
third file with only the ones having actual coordinates. 
Then, this step (checking if there is a “NA” entry) would not be 
necessary and could be skipped.
```
            else: 
                count += 1

```
This else is executed if the tgn ID could not be found in the list of 
tgn numbers with their coordinates. Then the variable count is incremented. 
```
	header = "ID\tplacename\tlat\tlon\tfreq\n"
    newFile= "coordinates_"+ freq_files[29:]
    newFile_no= "coordinates_no_" + freq_files[29:]


```
Again a header is created and the names of the new files are created to save 
them. As I prefer to have subfolders with the solutions and do not like to 
have all the different python files and the outputs within the same folder, 
I had to make a work around in order to be able to create the correct file 
names, while still loading the frequency files from their subfolder. 
This is why, here, only part of the frequency files name is being used to 
create the new file name. 
```
	with open(target_location + newFile, "w", encoding = "utf8") as f2:
        f2.write(header + "\n".join(data_list))
        
    with open(target_location + newFile_no, "w", encoding = "utf8") as f3:
        f3.write(header + "\n".join(data_list_no))

```
The two solution files are being created and saved correctly. 
```
	print("%d items have not been matched..." % count)

```
To see how many items of tgn numbers could not be matched, 
this print statement is executed within the function. 
```
dictionary = loading_coordinates("./coordinates/" + "tgn_coordinates.tsv")
```
Here, the function is called that loads the coordinates 
with the input variable of the location of the file and the files name.
```
# 1860 doesn't have matching articles
matching("./TSV_with_tgn_only_articles/" + "1861tgn.tsv", dictionary)
matching("./TSV_with_tgn_only_articles/" + "1862tgn.tsv", dictionary)
matching("./TSV_with_tgn_only_articles/" + "1863tgn.tsv", dictionary)
matching("./TSV_with_tgn_only_articles/" + "1864tgn.tsv", dictionary)
matching("./TSV_with_tgn_only_articles/" + "1865tgn.tsv", dictionary)


```
As I had extracted the frequencies for 1860 to 1865 for only articles, 
this were the files that I used for the matching process. 
However, 1860 does not have any articles with tgn numbers, which is why, 
this year was excluded. 
The matching function is being called for each year, taking the location 
of the files and their names as an input variable, 
as well as the dictionary that had been loaded before. 



Now, the obtained results (only articles) were loaded into QGIS and mapped. 
The frequencies were used as the parameter for the dot size. 
For each year there is a picture of the map of the whole world, 
showing that also other countries were mentioned in the newspaper each year. 
Then there are also pictures of each year with a focus on the U.S.
Here, the dots have also been labelled to get a better overview. 

![World map of 1861](\cleeb94.github.io\img\1861_world.png "World map 1861")
![U.S. map of 1861](\cleeb94.github.io\img\1861_US.png "US map 1861")

![World map of 1862](\cleeb94.github.io\img\1862_world.png "World map 1861")
![GU.S. map of 1862](\cleeb94.github.io\img\1862_US.png "US map 1861")

![World map of 1863](\cleeb94.github.io\img\1863_world.png "World map 1861")
![U.S. map of 1863](\cleeb94.github.io\img\1863_US.png "US map 1861")


![World map of 1864](\cleeb94.github.io\img\1864_world.png "World map 1861")
![U.S. map of 1864](\cleeb94.github.io\img\1864_US.png "US map 1861")


![World map of 1865](\cleeb94.github.io\img\1865_world.png "World map 1861")
![U.S. map of 1865](\cleeb94.github.io\img\1865_US.png "US map 1861")