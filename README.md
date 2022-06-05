
# **Under construction!!!**
Not finished. Well, not really even started!

## Scientific-text-analysis-project
In this project I'll try to analyze mostly scientific texts and compare them with other types of publications

## Project overwiev
The final script will be divided in the following parts:
  1) Links crawling
  2) Text crawling
  3) Basic text analysis
  4) Text identification

Only publications available in open-access will be used. This will enable all users to access the same publications withous need to pay/use academic networks.
In the first step only 3 thousands scientific papers, 3 thousands popular-science papers and 3 thousands papers of other types will be crawled. This will be the test base.

First two parts of the script will be controlled by input csv file containing the following fields:
Index number, Title of Journal, Base_page, post_iterator, container_for_link, class_for_link, regex_query_for_link, container_for_text, class_for_text.

## Crawling links
Before fully collecting text from publications, we want to create a list of links to the publications. In this step, I'll show you how this can be done. As an example, I'll use Nature Communications journal. The home page of the journal can be accessed at:
https://www.nature.com/
but the list of the latest issues can be found on this page:
https://www.nature.com/ncomms/research-articles?searchType=journalSearch&sort=PubDate&page=1

As you can see , the second link ends with "page=1". I'll use it to create list of pages, which will be passed to the crawling function. List of pages can be either created with simple loop, or you can do it in spreadsheet.
    
    pages = []
    base = "https://www.nature.com/ncomms/research-articles?searchType=journalSearch&sort=PubDate&page="
    for i in range(0,100):
      pages.append(base+str(i))

Next - it is cruciall to know what is the name of the container storing a publication link. In this case, I point the cursor at publication title, right-click it and select "inspect". It can be seen that there is a <a> container titled "c-card__link u-link-inherit". I save these for the crawling function. Last information that is needed is the link format. In case of Nature magazine it looks like this: "/articles/XXXXXX-XXX-XXXXX-X" where X can be an alpha-numeric character. To extract this information I use regex query: (?:href=")(\/articles\/.+?)(?:")
  
Having this type of information, it is possible to use the Crawl_articles function (see Lib file - SOON WILL BE ADDED TO THE PROJECT). We use the loop, to create list of urls for the next step.

    for url in pages:
       C, AL = Crawl_articles(url,container_pg,class_pg,filter)
       for a in AL:
          ListOfPapers.append(a)
      
 

## Crawling text
Once list of publications is complete, I use "Crawl_text" function for getting raw text. At this point, we don't care for text cleaning. This will be done at the next step. Before "Crawl_text" can be applied to full list, we need to check the following:
  -What is the name of container, class and ID of text?
  -Should we omit some parts of the text? (e.g. both text and and ad are in "div" container of the same class, but different parent)
  -Is there something we should cut at the end of the text (author list, supporting information ect.)
Once we have such information, the function should be tested on two-three examples to be sure that we get what we need. Again, the function is executed in a loop. Mandatory parameters of the function are: url, container and class. Non-mandatory parameters are by default set to 'None', and are in use only if changed to different value. Also there is a deepth parameters with which, we can add filtering basing not only on parent of container, but also on parent of parent. See function and full documentation in "Lib" (soon will be added).
  
    for ulr in ListOfPapers:
        C,I,T = Crawl_text(url,container_ar,class_ar)
  
## Cleaning text
  The text that was crawled is not yet ready for analysis. There are certain things that need to be done. First of all text need to be tokenized, and filtered - for any non alphanumeric characters. Also we would like to remove stop words. The function "Clean_text" does that and outputs 3 lists:
  - List of tokenized worlds
  - Tokenized, only alphanumeric words
  - Tokenized, alphanumeric, with stop words removed.
This is done to make future analysis easier - if there is an error on any level of cleaning, we can just load selected list. The last group will be used for 'Bag of words' analysis. After using "Clean_text", I save the data with "PickleResults" function. Also, the un-cleaned text is saved, and will be used at later stage - in per-sentence analysis.
  
  ## Analysis
  ### Bag of words analysis
  ...
