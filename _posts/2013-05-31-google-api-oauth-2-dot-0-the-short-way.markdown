---
layout: post
title: "Google API Oauth 2.0 the short way"
date: 2013-05-31 21:24
comments: true
categories: Google API Python
keywords: Oauth
---

Well 1st of all my problem: more or less one year ago i wrote a nice python script, a shell script that every night downloads datas from my analytics account and save them to a database. It's useful for my job because i can use this data to better analyze how my business web sites are going.

Now, more or less a week ago my script stopped to work. I had an error 401, limit rate exceeded you need to authenticate...

But my script was authenticated! Yes i used an old gdata api and a normal old ClientLogin API authorization protocol but it worked till last week.

Anyway i decided to switch to the Oauth 2.0 Authentication. There is a ton of documentation about how Oauth 2.0 works and there is a ton of documentation about how a Web Application and a Desktop Application can use Oauth 2.0 to be authenticated with Google API but there is really poor documentation about how a server side script can authenticate itself with google without an human presence.

<!--more-->

So this is my short tutorial.

1. You need an account on [Google API Console](https://code.google.com/apis/console/?pli=1#project:243201377985)
2. You need a new project
3. Click to "API Access" and "Create an OAuth 2.0 Client ID"
4. Select "Service Account" and Create Client ID
5. "Download Private Key" and save it in a secure place
6. Log in in your analytics account and add the email address related to your new project to your analytics property
7. Take note of your Client ID
8. Install the [google-api-python-client library](http://code.google.com/p/google-api-python-client/) to your server

We are ready to write code:


{% codeblock getdata.py lang:python %}
from apiclient.discovery import build
from oauth2client.file import Storage
from oauth2client.client import SignedJwtAssertionCredentials
import httplib2
import os.path

SITE_ROOT = os.path.dirname(os.path.realpath(__file__))

f = file('%s/%s' % (SITE_ROOT,'PATHTOYOURPRIVATEKEY'), 'rb')
key = f.read()
f.close()

http = httplib2.Http()
storage = Storage('analytics.dat')
credentials = storage.get()

if credentials is None or credentials.invalid:
    credentials = SignedJwtAssertionCredentials('YOURGSERVICEEMAIL', key, scope='https://www.google.com/analytics/feeds/')        
    storage.put(credentials)
else:
    credentials.refresh(http)
        
http = credentials.authorize(http)        
service = build(serviceName='analytics', version='v3', http=http)
                    
data_query = service.data().ga().get(
    ids = table_id.channel_value,
    start_date = date_start,
    end_date = date_end,
    metrics = 'ga:pageviews,ga:visitors',
    sort = '-ga:pageviews').execute()        

query = data_query.get('totalsForAllResults')
                    
for key, value in query.iteritems():
        	print '%s: %s' % (key,value)
{% endcodeblock %}

 
 You are done. I won't speak about the theory of how oauth works, this is a short tutorial. If you want you can learn more at [https://developers.google.com/accounts/docs/OAuth2](https://developers.google.com/accounts/docs/OAuth2)

