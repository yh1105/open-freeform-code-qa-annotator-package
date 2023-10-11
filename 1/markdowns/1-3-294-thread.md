
# Post \#63902467 [Link](https://stackoverflow.com/questions/63902467/)

## module 'six.moves' has no attribute 'collections_abc'

**Vote**: 4 (532/702) **Views**: 8423 (450/702) 

**Internal ID** \#1-3-294

Created at 2020-09-15 13:04:20

Tags: `python` `google-api` `airflow` `six`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I have a script that connects to YouTube API version 3 and retrieves public data. This script is deployed in airflow, it was working fine for a month, today it failed with this message for the following line:
```
def youtube_search(term,region):
        youtube = build(YOUTUBE_API_SERVICE_NAME, YOUTUBE_API_VERSION, developerKey=DEVELOPER_KEY,cache_discovery=False)
```

```
File "/usr/local/airflow/.local/lib/python3.6/site-packages/googleapiclient/discovery.py", line 455, in build_from_document
    if isinstance(client_options, six.moves.collections_abc.Mapping):
AttributeError: module 'six.moves' has no attribute 'collections_abc'
```

I went to check the [455th](https://github.com/googleapis/google-api-python-client/blob/master/googleapiclient/discovery.py) line on `discovery.py`
```
if isinstance(client_options, six.moves.collections_abc.Mapping):
        client_options = google.api_core.client_options.from_dict(client_options)
```

The `six` [module](https://github.com/benjaminp/six/blob/master/six.py) has not changed for a long time, nor has my script deployed in airflow, which config has not changed either.
As [Google](https://github.com/googleapis/google-api-python-client/blob/master/googleapiclient/discovery.py) module imports `six`, which has not changed, I can't figure out why would I get this error at random?


----------
        
## Answer \#0

**Accepted** Vote: 8

Created at 2020-09-15 14:35:37

------------

Are you explicitly specifying the version of google-api-python-client that you're using, or just using the latest version available?
There was a new version (v1.12.0) released on 9/14/20, followed by a bug fix version v.1.12.1 that fixes a bug related to the dependency on the `six` module. (release notes from GitHub: require six>=1.13.0 (#1030) (4acecc3))
You may be running into the bug in v1.12.0.


------------
    
    
## Answer \#1

 Vote: 3

Created at 2021-01-18 20:11:31

------------

simply run `pip install --upgrade six` The cause of this issue is usually that the module 'six' in your local environment is out of date with the version that is required by the google-api-python-client library.


------------
    
    
## Answer \#2

 Vote: 2

Created at 2020-09-15 17:00:26

------------

Six is one of the libraries installed by default. According to this Github issue:
> At the moment tests always install the latest version of dependencies. We're going to move to having explicit requirements.txt for tests runs so we catch lower bound issues like this automatically in the future.

[https://github.com/googleapis/google-api-python-client/issues/1029](https://github.com/googleapis/google-api-python-client/issues/1029)


------------
    
    