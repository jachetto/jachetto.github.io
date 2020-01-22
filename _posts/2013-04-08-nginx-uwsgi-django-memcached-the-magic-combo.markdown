---
layout: post
title: "nginx uwsgi django memcache the magic combo"
date: 2013-04-08 16:51
description: "How to build a perfect deploy with Nginx, Django, Uwsgi, Memcached"
comments: true
categories: [nginx, django,]
tags: [memcached, performance]
---
Well, one of the most interesting feature in Django, is the cache framework. Every django developer use the django cache to speed up the performance.
On the other side, Nginx it's probably the fastest server for the static file management.
Finally, in this story, we have also uwsgi and memcache, an application server really strong and a memory storage really fast.

Now, a classic deploy could be:

* nginx as reverse-proxy and to handle static file
* uwsgi as application server
* django as framework and cache management
* memcache as cache storage

So every request is handled by django. The chain is nginx - uwsgi - django - if the page is in memcache serve it from the cache, if not, exec a django view or something.

Our goal is another chain:

* nginx as reverse-proxy
* nginx to handle "every request if in cache"
* nginx to handle static file
* uwsgi as application server
* django only as cache writer
* memcahce as cache storage

So, let's start:

```python
#custom/middleware.py
class NginxMemcacheMiddleWare:
    cache_timeout = settings.CACHE_MIDDLEWARE_SECONDS
    key_prefix = settings.CACHE_MIDDLEWARE_KEY_PREFIX        
	   nginx_cache_prefix == settings.NGINX_CACHE_PREFIX

    def _session_accessed(self, request):
         try:
             return request.session.accessed
         except AttributeError:
             return False    

    def process_response(self, request, response):
        path = request.get_full_path()
        
        if request.GET.has_key(u'nocache') and request.GET[u'nocache']=="$no$cache":
            key = "%s:%s" % ('NS', path)
            cache.delete(key)
         
        if request.method != "GET" or response.status_code != 200 or (path.startswith('/admin')):
            return response

        
        try:
            key = "%s:%s" % (nginx_cache_prefix, path)
            cache.set(key, response.content,self.cache_timeout)
        except:
            pass

        try:
            del response['Last-Modified']
            del response['Expires']
        except:
            pass

        response['Vary'] = 'Accept-Encoding'
        
        return response
```


Our settings.py will looks like:

```python
#settings.py
MIDDLEWARE_CLASSES = (    
    'django.middleware.common.CommonMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'custom.middleware.NginxMemcacheMiddleWare',
)
```

And nginx site-enabled configuration:

```nginx
# site_enabled/default.conf
           location / {
	       	   expires       -1;
               add_header    Cache-Control no-cache;
               add_header    Vary User-Agent;
	       	   include     /etc/nginx/uwsgi_params;
               if ($request_method = POST) {
                        uwsgi_pass  uwsgicluster;
               }

	      	   default_type  "text/html";
               charset utf-8;
               set $memcached_key ":1:CUSTOMKEY:$request_uri";
               memcached_pass localhost:11211;
               error_page 404 502 = @fallback;
            }

			location @fallback {
				expires       -1;
		        add_header    Cache-Control no-cache;
		        add_header    Vary User-Agent;
		        include     /etc/nginx/uwsgi_params;
		        uwsgi_pass  uwsgicluster;
				internal; 
	  		}
```

Where CUSTOMKEY è id the same used in settings.py

That's all
