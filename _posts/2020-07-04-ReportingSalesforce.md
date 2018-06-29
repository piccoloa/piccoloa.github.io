---
layout: post
title:  "Data ETL: Connecting to Salesforce API"
date:   2020-06-30
comments: true
excerpt: 'Python script to connect to salesforce api to visualize and explore salesforce.com data'
---


_Part 1_
### Notes Exploring Salesforce.com data.

Helpful links:  
[pypy.org straight from your Salesforce report](https://pypi.org/project/salesforce-reporting/)  
[set-up remote access](https://developer.salesforce.com/forums/?id=906F00000008qsJIAQ)


[this is the sight to work with first) make the connection then try other scripts in notebook](https://www.mydatahack.com/salesforce-api-with-simple-salesforce-for-python/)


ssl error caused by linux:  think answer is here:
https://stackoverflow.com/questions/48946036/docker-container-always-shows-ssl-connection-error
ask luke



Each object in SF has ID. You need to collect IDs for reports prepared in step 1.
![png](/static/salesforce2.png)


Configure a salesforce report or copy url string from report in SF:
![png](/static/salesforce1.png)

    from simple_salesforce import Salesforce

    login = 'apiccolo@commvault.com'
    password = ‘XXXXXXXXXXXXXXXXXXX’

    sf = Salesforce(username='apiccolo.cv@commvault.com',password=’XXXXXXXXXXXXXXXXXX’,security_token='XXXXXXXXXXXXXXXXXXXXXXXXXX')
    sid = sf.session_id

    def get_pd(report, sf_sid):
      response = requests.get("https://commvault.my.salesforce.com/" + report + "?view=d&snip&export=1&enc=UTF-8&xf=csv", headers=sf.headers, cookies={'sid' : sf_sid})
      lines = response.content.decode('utf-8').splitlines()
      reader = csv.reader(lines, delimiter=',')
      data = list(reader)
      df = pd.DataFrame(data)
      df.columns = df.iloc[0]
      df = df.drop(0)
      return df
