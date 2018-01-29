---
layout: post
title: "Custom Django auth to login with email"
tagline: "Custom Django auth to login with email"
date: 2013-04-05 14:13
comments: true
categories: Django
---
Someone asked in django irc channel (#django on freenode): how can login using email as user?

In my opinion there is a simple way: write a custom auth backend.

{% codeblock settings.py lang:python %}
AUTHENTICATION_BACKENDS = (
    'backend.email-auth.EmailBackend',
    'django.contrib.auth.backends.ModelBackend',
)
{% endcodeblock %}

at this point you can write your custom backend:

{% codeblock backend/email-auth.py lang:python %}
from django.db import models
from django.contrib.auth.backends import ModelBackend
from django.contrib.auth.models import User
import re

email_re = re.compile(
    r"(^[-!#$%&'*+/=?^_`{}|~0-9A-Z]+(\.[-!#$%&'*+/=?^_`{}|~0-9A-Z]+)*"
    r'|^"([\001-\010\013\014\016-\037!#-\[\]-\177]|\\[\001-\011\013\014\016-\177])*"'
    r')@(?:[A-Z0-9-]+\.)+[A-Z]{2,6}$', re.IGNORECASE) 
            

class BasicBackend:
    def get_user(self, user_id):
        try:
            return User.objects.get(pk=user_id)
        except User.DoesNotExist:
            return None

class EmailBackend(BasicBackend):
    def authenticate(self, username=None, password=None):
        if email_re.search(username):
            try:
                user = User.objects.get(email=username)
            except User.DoesNotExist:
                return None
        else:
            try:
                user = User.objects.get(username=username)
            except User.DoesNotExist:
                return None
        if user.check_password(password):
            return user

{% endcodeblock %}

thats'all