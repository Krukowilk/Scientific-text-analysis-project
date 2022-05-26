
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
  
Having this type of information, it is possible to use the Crawl_articles function (see Lib file - SOON WILL BE ADDED TO THE PROJECT).

    def Crawl_articles(url,container,aclass,query):
      from bs4 import BeautifulSoup as BS
      import requests as rq
      import re

      #Check response
      response = rq.get(url)
      if response.status_code != 200:
          print("Error fetching page",url)
      else:
          content = response.content
          #Parsing page content with BS4
          soup = BS(response.content, 'html.parser')
          Items = soup.find_all(container, class_=aclass)

          #Seek links
          art_list = []
          for i in Items:
              M = re.findall(query,str(i))
              art_list.append(M)

      return(content,art_list)
      
 
If above function is used in loop with created pages list, the list of links is created.

## Crawling text
