## A plugin to support SAP authentication in Python Requests

[Requests](https://requests.readthedocs.io/en/latest/) is a simple HTTP library for Python. It supports many authentication mechanisms, but also
creating a custom one.

In this blog post I will explain how to use custom authentication plugin for SAP portal authentication.

Why would we need it? In cases we want to automate the file management on SAP portal, we may have an automation to check if files are valid or similar.

---

### The SAP portal authentication

The SAP portal authenation consist of few steps. In each step the client must send the hidden data to the post from, which is returned in the HTTP response.
At the end we receive `SAMLResponse` data, which we can use to create a HTTP session and access our HTTP link.

#### The code

{% gist be1007b3a99e41a23d997d43e09b3ab3 %}

### Usage

The usage of the [library](https://pypi.org/project/requests-sap/) is very simple, you just need to pass the `SAPAuth` instance to the `requests` HTTP call.
And the authentication is happening behind the scene for you, no need to know any details, just `username` and `password`.

#### Install

The library is hosted on [pypi](https://pypi.org/project/requests-sap/), so you can simply install it via `pip`.

```bash
pip install requests-sap
```

#### Sample code

This sample code will login to SAP portal, and fetch information and print the information to stdout.

{% gist 6d652ed1fae08deb147a28abfc2772fc %}

The output is:

```bash
SAP HANA Platform Edt. 2.0 SPS05 rev57 Linux x86_64 is AVAILABLE
```
