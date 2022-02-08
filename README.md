# Scrap-Data-Lowongan-Pekerjaan-Data-Analyst-Indonesia-pada-Website-Indeed

#Indeed Job Scraper

#Libraries
from datetime import datetime
import requests
from bs4 import BeautifulSoup
import pandas as pd

#Create URL
position = '"Data Analyst"'
location = 'Indonesia'
url = 'https://id.indeed.com/jobs?q={}&l={}&start='.format(position, location)

#Cek response
response = requests.get(url)
print(response.status_code)

#List datas
datas = []

#Looping page
count_page = 0
for page in range(0, 1000, 10):

    count_page+=10
    print('scraping page :', count_page)

    response = requests.get(url+str(page))
    soup = BeautifulSoup(response.text, 'html.parser')
    boxs = soup.find_all('div', 'job_seen_beacon')
    for box in boxs:
        job_title = box.find('div', class_="heading4 color-text-primary singleLineTitle tapItem-gutter").text.replace('Baru', '').strip()
        company_name = box.find('span', class_="companyName").text.strip()
        location = box.find('div', class_="companyLocation").text.replace('•', ' / ').strip()
        try : salary_month_Rp = box.find('div', class_="heading6 tapItem-gutter metadataContainer").text.replace(' per bulan', '').replace('Rp. ', '').strip()
        except : salary_month_Rp = ''
        posted_days_ago = box.find('span', class_="date").text.replace('Posted', '').replace(' hari yang lalu', '').replace('EmployerAktif ', '').strip()
        today = datetime.today().strftime('%Y-%m-%d')
        summary = box.find('div', class_="job-snippet").text.strip().replace('\n', ' ').replace('…', '').strip()
        
        job = {
            'job_title': job_title,
            'company_name': company_name,
            'location': location,
            'salary_month_Rp': salary_month_Rp,
            'posted_days_ago': posted_days_ago,
            'today': today,
            'summary': summary
        }
        datas.append(job)

#save to csv
df = pd.DataFrame(datas)
print(df.head())
df.to_csv('project_Bagus_Sist.csv')
