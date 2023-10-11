
# Post \#49324802 [Link](https://stackoverflow.com/questions/49324802/)

## pip always fails ssl verification

**Vote**: 44 (143/702) **Views**: 127454 (67/702) 

**Internal ID** \#1-3-302

Created at 2018-03-16 15:50:09

Tags: `ssl` `pip`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

Pip always fails ssl even when I do `pip install dedupe` or `pip install --trusted-host pypi.python.org dedupe`

The output is always the same no matter what:

> Collecting dedupeRetrying (Retry(total=4, connect=None, read=None,
  redirect=None, status=None)) after connection broken by
  'SSLError(SSLError(1, '[SSL: CERTIFICATE_VERIFY_FAILED] certificate
  verify failed (_ssl.c:777)'),)': /simple/dedupe/
  Retrying...skippingCould not find a version that satisfies the requirement dedupe (from versions: ) No matching distribution found for dedupe

So I uninstalled anaconda and reinstalled it. Same thing.

Do you think the problem is that my _ssl.c file (which I have no idea where it is) must be corrupt or something? Why would pip need to reference that if I'm telling it to bypass ssl verification anyway?


----------
        
## Answer \#0

**Accepted** Vote: 71

Created at 2018-04-24 01:05:26

------------

It may be related to the [2018 change](https://pyfound.blogspot.com/2018/03/warehouse-all-new-pypi-is-now-in-beta.html) of PyPI [domains](https://github.com/pypa/pip/issues/5288#issuecomment-383071739).
Please ensure your firewall/proxy allows access to/from: 

- - 

So you could give a try to something like:  

> $ python -m pip `install` --trusted-host files.pythonhosted.org --trusted-host pypi.org --trusted-host pypi.python.org [--proxy ...] [--user] `<packagename>`  

Please see `$ pip help install` for the `--user` option description (omit if in a virtualenv).
The `--trusted-host` option doesn't actually bypass SSL/TLS, but allows to mark host as trusted when (and only when) it does not have valid (or any) HTTPS. It shouldn't really matter with PiPY because pypi.org (formerly pypi.python.org)  use HTTPS and there is CDN in front of it which always enforces TLSv1.2 handshake requirement regardless of the connecting pip client options.. But if you had your own local mirrors of pypi.org with HTTP-only access, then `--trusted-host` could be handy. Oh, and if you are behind a proxy, please also make sure to also specify: `--proxy [user:passwd@]proxyserver:port`
Some corporate proxies may even go as far as to [replace](https://stackoverflow.com/questions/5846652/can-proxy-change-ssl-certificate) the certificates of HTTPS connections on the fly. And if your system clock is out of sync, it could break SSL verification process as well.

If firewall / proxy / clock isn't a problem, then check SSL certificates being used in pip's SSL handshake. In fact, you could just get a current [cacert.pem](https://curl.haxx.se/ca/cacert.pem) (Mozilla's CA bundle from [curl](https://curl.haxx.se/docs/caextract.html)) and try it using the pip option `--cert`:  

> `$ pip --cert ~/cacert.pem install --user <packagename>`
  where `--cert` argument is system path to your alternate CA bundle in PEM format. (regarding the --user option, please see below).
  Or, it's possible to create a custom config ~/.pip/pip.conf and point the option at a valid system cert (or your cacert.pem) as a workaround, for example:
  
  
  (or another pem file)

It's even possible to manually replace the original cacert.pem found in pip with your trusty CA bundle (if your pip is very old for example). Older pip versions knew to fallback between pip/_vendor/requests/cacert.pem and system stores like `/etc/ssl/certs/ca-certificates.crt` or `/etc/pki/tls/certs/ca-bundle.crt` in case of cert issues, but in recent pip it's no longer the case, as it seems to rely solely on pip/_vendor/certifi/cacert.pem

Basically, pip package uses `requests` which uses `urllib3` which, among other things, verifies SSL certificates; and all of them are shipped (vendored) within pip, along with the `certifi` package (also included, since pip 9.0.2) that provides current CA bundle (cacert.pem file) required for TLS verification. Requests itself uses urllib3 and certifi internally, and before 9.0.2, pip used cacert.pem from requests or the system. What it all means is that actually updating pip may help fix the CERTIFICATE_VERIFY_FAILED error, particularly if the OS and pip were deployed long ago:  

- The OP used anaconda, so they could try:
`$ conda update pip` - because [issues can arise](https://www.anaconda.com/using-pip-in-a-conda-environment/) if conda and `pip`  are both used together in the same environment. If there's no pip version update available, they could try:
`$ conda config --add channels conda-forge; conda update pip`
Alternatively, it's possible to use [conda](https://conda.io/en/latest/) alone to directly install / manage python packages: it is a tool completely separate from pip, but provides similar features in terms of package and venv management. Its packages come not from PyPI, but from [anaconda's own repositories](https://docs.anaconda.com/anaconda/). 
The problem is, if you mix both and run conda after `pip`, the former can overwrite and break packages (and their dependencies) installed via pip, and render it all unusable. So it's recommended to , or, if you have to, use  conda (and no conda after pip), and only in isolated conda environments.  - On normal Linux Python installations without conda:
If you are using a version of pip supplied by your OS distribution, then use vendor-supplied upgrades for a system-wide pip update:
`$ sudo apt-get install python-pip`  or:  `$ sudo yum install python27-pip`
Some updates may not be readily available because distros usually lag behind PyPI. In this case, it's possible to upgrade pip at your  (right in your  dir), or inside a virtualenv, like:
`$ python -m pip install --user --trusted-host files.pythonhosted.org --trusted-host pypi.org --trusted-host pypi.python.org --upgrade pip`
(omit `--user` if in a virtualenv)
The `--user` switch will upgrade pip only for the current user (in your home ~/.local/lib/) rather than for the whole OS, which is a good practice to avoid interfering with the system python packages. It's enabled by default in a pip distributed in recent Ubuntu/Fedora versions. Be aware of how to solve [ImportError](https://stackoverflow.com/questions/49964093/file-usr-bin-pip-line-9-in-module-from-pip-import-main-importerror-canno/) if you don't use this option and happen to overwrite the OS-level system pip.
Alternatively (also at a user level) you could try:
`$ curl -LO https://bootstrap.pypa.io/get-pip.py && python get-pip.py --user`
The PyPA [script](https://pip.pypa.io/en/latest/installing/) contains a wrapper that extracts the .pem SSL bundle from pip._vendor.certifi.  

Otherwise, if still no-go, try running pip with `-vvv` option to add verbosity to the output and check if there is now another `SSLError` caused by [tlsv1 alert protocol version](https://stackoverflow.com/questions/51646558).  


------------
    
    
## Answer \#1

 Vote: 40

Created at 2020-12-23 19:31:02

------------

This worked for me, try this:
`pip install --trusted-host=pypi.org --trusted-host=files.pythonhosted.org --user {name of whatever I'm installing}`


------------
    
    
## Answer \#2

 Vote: 15

Created at 2020-03-19 16:50:50

------------

My way is a simplification of @Alex C's answer:

```
python -m pip install --trusted-host pypi.python.org --trusted-host files.pythonhosted.org --trusted-host pypi.org --upgrade pip
```



------------
    
    
## Answer \#3

 Vote: 14

Created at 2022-04-25 00:19:27

------------

I experienced the same issue because I have  (a cloud security software) installed and was causing:
- - - 
As mentioned by others, the below will fix individual package installations. `pypi.python.org` is not required since it has been replaced by `pypi.org`.
```
pip install --trusted-host pypi.org --trusted-host files.pythonhosted.org <package to install>
```

I permanently fixed the issue by creating `pip.ini` file (`pip.conf` in Unix) and adding the below:
```
[global]
trusted-host = pypi.python.org
               pypi.org
               files.pythonhosted.org
```

See [pip configuration files](https://pip.pypa.io/en/stable/topics/configuration/) for how to locate your `pip.ini`, or where to put it if you need to create one.


------------
    
    
## Answer \#4

 Vote: 3

Created at 2019-06-28 09:24:37

------------

The error above or one like it was caused by the virtual machine (VM) not be time synchronized, my guest Ubuntu VM was several days in the past.

I ran this commend to get the VM to pick up the correct network time:

```
sudo timedatectl set-ntp on
```


This makes the Ubuntu guest OS get the network time. (You may have to provide a network time source... I used this article: [Digital Ocean - How to set time on Ubuntu](https://www.digitalocean.com/community/tutorials/how-to-set-up-time-synchronization-on-ubuntu-16-04))

Check the time is correct: 

```
timedatectl
```


Re-run the failing pip command.


------------
    
    