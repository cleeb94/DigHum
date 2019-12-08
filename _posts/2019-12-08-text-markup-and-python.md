---
layout: post
title: Text Markup, and how to remove it
subtitle: Using Python scripts
image: /img/dispatch.jpg
---

The Python script just creating clean copies of the text from each issue is:

"""
import re
import os

source_location = "./practice3/"   # the folder in which the "dispatch" is
target_location = "./Clean_copies_issues/" # the folder in which the new files will be saved 

lof=os.listdir(source_location) # getting all files from a folder

## looping through all the files

for f in lof:
    new_file=f + "_modified.xml" # need to add the extension to work with it
    with open(source_location + f, "r", encoding="utf8") as f1:
        text0 = f1.read() # reads the file in
        
        ## Extracting the date of the Issue = date of the articles

        date_section = re.search(r"<titlePage>([\n-^\w—]*)</titlePage>", text0).group(1) #finds the section where the date is in
        issue_date=re.search(r"<date value=\"([\d-]*)\" authname",date_section).group(1) #extracts the date of the issue


        ## Getting the articles

        clean = re.sub("<[^<]+>", "", text0) #get rid of all < >
        clean = re.sub(" +\n|\n +","\n",clean) #get rid of all line breaks with spacing
        clean = clean.strip() #get rid of all spacing in beginning and end
        clean = re.sub("\n+","\n",clean) #get rid of all additional line breaks 
    

        ## saving files
        with open(target_location+new_file, "w", encoding ="utf8") as f2: #open in target folder
            f2.write(clean) #write in one file all the information
"""

The Python script for the clean copies of items is:

"""
import re
import os

source_location = "./practice3/"   # the folder in which the "dispatch" is
target_location = "./Clean_copies_articles/" # the folder in which the new files will be saved 

lof=os.listdir(source_location) # getting all files from a folder

## looping through all the files

for f in lof:
    new_file=f + "_modified.xml" # need to add the extension to work with it
    list_items= [] # to append list of items at the end
    with open(source_location + f, "r", encoding="utf8") as f1:
        text0 = f1.read() # reads the file in
        
        ## Extracting the date of the Issue = date of the articles

        date_section = re.search(r"<titlePage>([\n-^\w—]*)</titlePage>", text0).group(1) #finds the section where the date is in
        issue_date=re.search(r"<date value=\"([\d-]*)\" authname",date_section).group(1) #extracts the date of the issue


        ## Getting the articles

        results = re.split(r"<div3 ", text0) # split the text into the div3 parts
        count= 0 # to be able to number the articles later

        for r in results[1:]: #first one is a "library"
            count = count +1
            r ="<div3 " + r # reattach so that it can be canceled out later on
    
            ## type
    
            try:
                item_type=re.search(r"type=\"(\w*)\" ",r).group(1) #check if it has a type, save it
            except:
                item_type="noItemType"
        
            ## header of article
    
            try: 
                header_section= re.search(r"<head>(.*)</head>",r).group(1) #check if it has a header, save it
            except: 
                header_section = "NoHeader"
        
            ## cleaning 
    
            clean = re.sub("<[^<]+>", "", r) #get rid of all < >
            clean = re.sub(" +\n|\n +","\n",clean) #get rid of all line breaks with spacing
            clean = clean.strip() #get rid of all spacing in beginning and end
            clean = re.sub("\n+","\n",clean) #get rid of all additional line breaks
            header = re.sub("<[^<]+>", "", header_section) #cleaning header of < > 
    
            ## collecting all information
    
            if len(re.sub("\W","", clean)) != 0: #noticed that some don't have text but still something -> this way don't include them
                item= "item:" + issue_date +"_"+ item_type + "_%03d" % count #create item information
                date_final = "date:" + issue_date 
                item_type_final = "type:" + item_type
                header_final = "header:" + header
                text_final = "text:" + clean + "\n\n\n" # so that the articles are seperated clearly
    
                collect = "\n".join([item,date_final,item_type_final,header_final,text_final]) #collect the info

                list_items.append(collect)    #append to list of all items in this issue

        ## saving files
        new_issue ="".join(list_items) #combines the parts
        with open(target_location+new_file, "w", encoding ="utf8") as f2: #open in target folder
            f2.write(new_issue) #write in one file all the information
"""