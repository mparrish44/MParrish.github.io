#!/usr/bin/env python
# coding: utf-8


import requests
from bs4 import BeautifulSoup
import csv

# URL
page = "https://www.census.gov/programs-surveys/popest.html"

# Request
r = requests.get(page)

# Retrieves all raw HTMLs
raw_html = r.text

# parse HTML
soup = BeautifulSoup(raw_html, 'html.parser')
tags = soup.find_all('a', href=True)

print('Number of URLs retrieved: ', len(tags))

url_list = set()
for link in tags:
    hrefs = link.get('href')
    # removes blank URLs
    if hrefs.startswith('None'):
        continue
    # removes tags that starts with #    
    elif hrefs.startswith('#'):
        continue      
    # changes url to absolute and removes duplicates
    elif hrefs.startswith('/'):
         url_list.add('https://www.census.gov' + hrefs)   
    elif hrefs.endswith('/'):
         url_list.add(hrefs[-1:])        
    elif hrefs.startswith('#http'):
         url_list.add(hrefs[1:])
    else:
         url_list.add(hrefs)
             
print ('Total Unique URLs: ', len(url_list)) 

final_list = (list(url_list))

with open('MParrish_Assessment.csv', 'w') as f:
    writer = csv.writer(f)
    for url in final_list:
        writer.writerow([url])
f.close()
print("[INFO] --- Writing to CSV file completed ---")
print("[INFO] --- get-estimates.py completed successfully") 






