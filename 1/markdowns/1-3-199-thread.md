
# Post \#65429877 [Link](https://stackoverflow.com/questions/65429877/)

## AWS Lambda Container Running Selenium With Headless Chrome Works Locally But Not In AWS Lambda

**Vote**: 15 (318/702) **Views**: 9764 (420/702) 

**Internal ID** \#1-3-199

Created at 2020-12-23 19:25:28

Tags: `python` `docker` `selenium` `aws-lambda` `chromium`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I am currently developing a Python program which has a segment which uses a headless version of Chrome and Selenium to perform a repetitive process. I am aiming to run the program on Lambda.
The overall program has around 1GB of dependencies so the option to use the standard method of using a [.zip archive](https://docs.aws.amazon.com/lambda/latest/dg/python-package.html), containing all my function code and dependencies is not an option as the total unzipped size of the function and all layers can't exceed the unzipped deployment package size limit of 250 MB.
So, that is where the new [AWS Lambda – Container Image Support](https://aws.amazon.com/blogs/aws/new-for-aws-lambda-container-image-support/) (I used this linked tutorial to develop this whole implementation so please read if you need more info) comes in. This allows me to package and deploy my Lambda function as container images of up to 10 GB in size.
I am using the base image hosted in ECR Public provided by AWS which runs Amazon Linux 2. Firstly - in my Dockerfile I:
- - - - - 
and finally - I install both Chrome (87.0.4280.88 at the time of reading) and Chromedriver (87.0.4280.88)
- 
there is a possibility this could be where the problem lies, but I highly doubt this as both are the same version - [ChromeDriver uses the same version number scheme as Chrome](https://www.chromium.org/developers/version-numbers).
This is my :
```
# 1) DOWNLOAD BASE IMAGE.
FROM public.ecr.aws/lambda/python:3.8

# 2) DEFINE GLOBAL ARGS.
ARG MAIN_FILE="main.py"
ARG ENV_FILE="params.env"
ARG REQUIREMENTS_FILE="requirements.txt"
ARG FUNCTION_ROOT="."
ARG RUNTIME_VERSION="3.8"

# 3) COPY FILES.
# Copy The Main .py File.
COPY ${MAIN_FILE} ${LAMBDA_TASK_ROOT}
# Copy The .env File.
COPY ${ENV_FILE} ${LAMBDA_TASK_ROOT}
# Copy The requirements.txt File.
COPY ${REQUIREMENTS_FILE} ${LAMBDA_TASK_ROOT}
# Copy Helpers Folder.
COPY helpers/ ${LAMBDA_TASK_ROOT}/helpers/
# Copy Private Folder.
COPY priv/ ${LAMBDA_TASK_ROOT}/priv/
# Copy Source Data Folder.
COPY source_data/ ${LAMBDA_TASK_ROOT}/source_data/

# 4) INSTALL DEPENDENCIES.
RUN --mount=type=cache,target=/root/.cache/pip python3.8 -m pip install --upgrade pip
RUN --mount=type=cache,target=/root/.cache/pip python3.8 -m pip install wheel
RUN --mount=type=cache,target=/root/.cache/pip python3.8 -m pip install urllib3
RUN --mount=type=cache,target=/root/.cache/pip python3.8 -m pip install -r requirements.txt --default-timeout=100

# 5) DOWNLOAD & INSTALL CHROMEIUM + CHROMEDRIVER.
#RUN yum -y upgrade
RUN yum -y install wget unzip libX11 nano wget unzip xorg-x11-xauth xclock xterm

# Install Chrome
RUN wget https://intoli.com/install-google-chrome.sh
RUN bash install-google-chrome.sh

# Install Chromedriver
RUN wget https://chromedriver.storage.googleapis.com/87.0.4280.88/chromedriver_linux64.zip
RUN unzip ./chromedriver_linux64.zip
RUN rm ./chromedriver_linux64.zip
RUN mv -f ./chromedriver /usr/local/bin/chromedriver
RUN chmod 755 /usr/local/bin/chromedriver

# 5) SET CMD OF HANDLER.
CMD [ "main.lambda_handler" ]
```

This image always builds without a problem and creates my image as expected.
and my  file:
```
version: "3.7"
services:
  lambda:
    image: tbg-lambda:latest
    build: .
    ports:
      - "8080:8080"
    env_file:
     - ./params.env
```

So - now that the image is build I can test locally with cURL. Here, I am passing an empty JSON payload:
```
curl -XPOST "http://localhost:8080/2015-03-31/functions/function/invocations" -d '{}'
```

which runs the whole program perfectly start to end using Chrome headless mode with no errors.
So great - the Docker container works locally and as expected.
Lets upload it to ECR so I can use it with my Lambda Function (ECR URL changed for security):
```
aws ecr create-repository --repository-name tbg-lambda:latest --image-scanning-configuration scanOnPush=true
docker tag tbg-lambda:latest 123412341234.dkr.ecr.sa-east-1.amazonaws.com/tbg-lambda:latest
aws ecr get-login-password | docker login --username AWS --password-stdin 123412341234.dkr.ecr.sa-east-1.amazonaws.com
docker push 123412341234.dkr.ecr.sa-east-1.amazonaws.com/tbg-lambda:latest
```

Everything pushes up as expected - I then create my new lambda function, choosing "Container Image" as the function option and attach the IAM role with all the permissions I need:
[](https://i.stack.imgur.com/pNZnU.png)
I have the memory set the max value just to ensure this isn't the problem:
[](https://i.stack.imgur.com/1pUO5.png)
Ok - so lets to get to the 
I use a test event to invoke the function through the console:
[](https://i.stack.imgur.com/kKHzm.png)
Everything runs perfectly until it hits the code which creates the webdriver driver with Chrome:
```
options = Options()
    options.add_argument('--no-sandbox')
    options.add_argument('--headless')
    options.add_argument('--single-process')
    options.add_argument('--disable-dev-shm-usage')
    options.add_argument('--remote-debugging-port=9222')
    options.add_argument('--disable-infobars')
    driver = webdriver.Chrome(
        service_args=["--verbose", "--log-path={}".format(logPath)],
        executable_path=f"/usr/local/bin/chromedriver",
        options=options
    )
```

PS: logPath is just another folder in the project directory - the logs output here as expected, the logs are shown below.

```
Caught WebDriverException Error: unknown error: Chrome failed to start: crashed.

(unknown error: DevToolsActivePort file doesn't exist)

(The process started from chrome location /usr/bin/google-chrome is no longer running, so ChromeDriver is assuming that Chrome has crashed.)

END RequestId: 7c933bca-5f0d-4458-9529-db28da677444

REPORT RequestId: 7c933bca-5f0d-4458-9529-db28da677444 Duration: 59104.94 ms Billed Duration: 59105 ms Memory Size: 10240 MB Max Memory Used: 481 MB

RequestId: 7c933bca-5f0d-4458-9529-db28da677444 Error: Runtime exited with error: exit status 1 Runtime.ExitError
```


```
[1608748453.064][INFO]: Starting ChromeDriver 87.0.4280.88 (89e2380a3e36c3464b5dd1302349b1382549290d-refs/branch-heads/4280@{#1761}) on port 54581
[1608748453.064][INFO]: Please see https://chromedriver.chromium.org/security-considerations for suggestions on keeping ChromeDriver safe.
[1608748453.064][INFO]: /dev/shm not writable, adding --disable-dev-shm-usage switch
[1608748453.679][SEVERE]: CreatePlatformSocket() failed: Address family not supported by protocol (97)
[1608748453.679][INFO]: listen on IPv6 failed with error ERR_ADDRESS_UNREACHABLE
[1608748454.432][INFO]: [13826d22c628514ca452d1f2949eb011] COMMAND InitSession {
   "capabilities": {
      "alwaysMatch": {
         "browserName": "chrome",
         "goog:chromeOptions": {
            "args": [ "--no-sandbox", "--headless", "--single-process", "--disable-dev-shm-usage" ],
            "extensions": [  ]
         },
         "platformName": "any"
      },
      "firstMatch": [ {

      } ]
   },
   "desiredCapabilities": {
      "browserName": "chrome",
      "goog:chromeOptions": {
         "args": [ "--no-sandbox", "--headless", "--single-process", "--disable-dev-shm-usage" ],
         "extensions": [  ]
      },
      "platform": "ANY",
      "version": ""
   }
}
[1608748454.433][INFO]: Populating Preferences file: {
   "alternate_error_pages": {
      "enabled": false
   },
   "autofill": {
      "enabled": false
   },
   "browser": {
      "check_default_browser": false
   },
   "distribution": {
      "import_bookmarks": false,
      "import_history": false,
      "import_search_engine": false,
      "make_chrome_default_for_user": false,
      "skip_first_run_ui": true
   },
   "dns_prefetching": {
      "enabled": false
   },
   "profile": {
      "content_settings": {
         "pattern_pairs": {
            "https://*,*": {
               "media-stream": {
                  "audio": "Default",
                  "video": "Default"
               }
            }
         }
      },
      "default_content_setting_values": {
         "geolocation": 1
      },
      "default_content_settings": {
         "geolocation": 1,
         "mouselock": 1,
         "notifications": 1,
         "popups": 1,
         "ppapi-broker": 1
      },
      "password_manager_enabled": false
   },
   "safebrowsing": {
      "enabled": false
   },
   "search": {
      "suggest_enabled": false
   },
   "translate": {
      "enabled": false
   }
}
[1608748454.433][INFO]: Populating Local State file: {
   "background_mode": {
      "enabled": false
   },
   "ssl": {
      "rev_checking": {
         "enabled": false
      }
   }
}
[1608748454.433][INFO]: Launching chrome: /usr/bin/google-chrome --disable-background-networking --disable-client-side-phishing-detection --disable-default-apps --disable-dev-shm-usage --disable-hang-monitor --disable-popup-blocking --disable-prompt-on-repost --disable-sync --enable-automation --enable-blink-features=ShadowDOMV0 --enable-logging --headless --log-level=0 --no-first-run --no-sandbox --no-service-autorun --password-store=basic --remote-debugging-port=0 --single-process --test-type=webdriver --use-mock-keychain --user-data-dir=/tmp/.com.google.Chrome.xgjs0h data:,
mkdir: cannot create directory ‘/.local’: Read-only file system
touch: cannot touch ‘/.local/share/applications/mimeapps.list’: No such file or directory
/usr/bin/google-chrome: line 45: /dev/fd/62: No such file or directory
/usr/bin/google-chrome: line 46: /dev/fd/62: No such file or directory
prctl(PR_SET_NO_NEW_PRIVS) failed
[1223/183429.578846:FATAL:zygote_communication_linux.cc(255)] Cannot communicate with zygote
Failed to generate minidump.[1608748469.769][INFO]: [13826d22c628514ca452d1f2949eb011] RESPONSE InitSession ERROR unknown error: Chrome failed to start: crashed.
  (unknown error: DevToolsActivePort file doesn't exist)
  (The process started from chrome location /usr/bin/google-chrome is no longer running, so ChromeDriver is assuming that Chrome has crashed.)
[1608748469.769][DEBUG]: Log type 'driver' lost 0 entries on destruction
[1608748469.769][DEBUG]: Log type 'browser' lost 0 entries on destruction
```

One thing that I might think could be the problem would the way lambda is running this container vs how I am running it locally.
Alot of people reccomend  to not run chrome as root - so is Lambda running the container as root and thats what is causing this? If so how can I tell Lambda or Docker to run the code as a non-root user.
This is mentioned here: [https://github.com/heroku/heroku-buildpack-google-chrome/issues/46#issuecomment-484562558](https://github.com/heroku/heroku-buildpack-google-chrome/issues/46#issuecomment-484562558)
I have been fighting with this error pretty much since AWS announced the lambda containers so any help with this would be brilliant  Please ask for any more info if I missed something!
Thanks in advance.


----------
        
## Answer \#0

**Accepted** Vote: 6

Created at 2021-01-08 19:17:22

------------

Python v3.6 works great. I have a `bin` directory with `chromedriver v2.41` ([https://chromedriver.storage.googleapis.com/2.41/chromedriver_linux64.zip](https://chromedriver.storage.googleapis.com/2.41/chromedriver_linux64.zip)) and `headless-chrome v68.0.3440.84` ([https://github.com/adieuadieu/serverless-chrome/releases/download/v1.0.0-53/stable-headless-chromium-amazonlinux-2017-03.zip](https://github.com/adieuadieu/serverless-chrome/releases/download/v1.0.0-53/stable-headless-chromium-amazonlinux-2017-03.zip)).
Below is my Dockerfile where I copy `chromedriver` and `headless-chrome` from source `bin` directory to the destination `bin` directory. The reason for having the destination `bin` directory is mentioned below.
```
FROM public.ecr.aws/lambda/python:3.6

COPY app.py ${LAMBDA_TASK_ROOT}
COPY requirements.txt ${LAMBDA_TASK_ROOT}

RUN --mount=type=cache,target=/root/.cache/pip python3.6 -m pip install --upgrade pip
RUN --mount=type=cache,target=/root/.cache/pip python3.6 -m pip install -r requirements.txt

RUN mkdir bin

ADD bin bin/

CMD [ "app.handler" ]
```

In my python script, I will copy the files in `bin` directory (Docker Container) to `/tmp/bin` directory (Amazon Linux 2) with `775` permission because `tmp` is the only directory where we can write files in Amazon linux 2 as the lambda will be executed here.
```
BIN_DIR = "/tmp/bin"
CURR_BIN_DIR = os.getcwd() + "/bin"


def _init_bin(executable_name):
    if not os.path.exists(BIN_DIR):
        logger.info("Creating bin folder")
        os.makedirs(BIN_DIR)

    logger.info("Copying binaries for " + executable_name + " in /tmp/bin")

    currfile = os.path.join(CURR_BIN_DIR, executable_name)
    newfile = os.path.join(BIN_DIR, executable_name)

    shutil.copy2(currfile, newfile)

    logger.info("Giving new binaries permissions for lambda")

    os.chmod(newfile, 0o775)
```

In the `handler` function, use the below options to avoid few exceptions raised by chrome driver.
```
def handler(event, context):

    _init_bin("headless-chromium")
    _init_bin("chromedriver")

    options = Options()

    options.add_argument("--headless")
    options.add_argument("--disable-gpu")
    options.add_argument("--no-sandbox")
    options.add_argument('--disable-dev-shm-usage')
    options.add_argument('--disable-gpu-sandbox')
    options.add_argument("--single-process")
    options.add_argument('window-size=1920x1080')
    options.add_argument(
        '"user-agent=Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.100 Safari/537.36"')

    options.binary_location = "/tmp/bin/headless-chromium"
    browser = webdriver.Chrome(
        "/tmp/bin/chromedriver", options=options)
```



------------
    
    
## Answer \#1

 Vote: 14

Created at 2021-03-12 05:34:49

------------

Now it's known that native chromium can works on lambda, and you don't need serverless-chrome anymore. In this my repository, Latest one use native chromium, but you can also find serverless-chrome is being used in old releases.
[https://github.com/umihico/docker-selenium-lambda/](https://github.com/umihico/docker-selenium-lambda/)
So far, the latest versions I could find are below.
- - - - 


------------
    
    
## Answer \#2

 Vote: 4

Created at 2021-01-17 16:25:33

------------

The solution from Sandeep Kumar works (upvoted it but not working as i am new user).
This is the minimal setup for running selenium in container-based lambda.

1. Download and copy the binary files as Sandeep mentioned(chromedriver v2.41 and headless-chrome v68.0.3440.84) into bin folder
2. requirements.txt


```
selenium==3.14.0
```


1. Dockerfile (note: python 3.8 does not work)


```
FROM public.ecr.aws/lambda/python:3.6

COPY app.py ${LAMBDA_TASK_ROOT}
COPY requirements.txt ${LAMBDA_TASK_ROOT}

RUN pip install --upgrade pip
RUN pip install -r requirements.txt

RUN mkdir bin
ADD bin /bin/

RUN chmod 755 /bin/chromedriver

CMD [ "app.handler" ]
```


1. app.py


```
from selenium import webdriver

def handler(event, context):
    options = webdriver.ChromeOptions()

    options.add_argument("--headless")
    options.add_argument("--disable-gpu")
    options.add_argument("--no-sandbox")
    options.add_argument('--disable-dev-shm-usage')
    options.add_argument('--disable-gpu-sandbox')
    options.add_argument("--single-process")
    options.add_argument('window-size=1920x1080')
    options.add_argument(
        '"user-agent=Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.100 Safari/537.36"')

    options.binary_location = "/bin/headless-chromium"
    browser = webdriver.Chrome(
        executable_path="/bin/chromedriver", options=options)

    browser.get("https://feng.lu")
    print(browser.title)

    browser.quit()
```


1. Have default IAM permission in AWS lambda is good enough


Note: I did not copy the content into /tmp/bin folder as Sandeep did, just use the bin folder, and I update the CHMOD permission inside the docker file.


------------
    
    
## Answer \#3

 Vote: 1

Created at 2021-01-21 13:13:43

------------

I managed to fix this by switching over to ubuntu 18.04 and installing some dependencies (see step #4, #5 and #6) which Chrome required to run.
Heres my Dockerfile:
```
# 1) DOWNLOAD BASE IMAGE.
FROM public.ecr.aws/ubuntu/ubuntu:18.04_edge

# 2) DEFINE GLOBAL ARGS.
ARG MAIN_FILE="main.py"
ARG ENV_FILE="params.env"
ARG REQUIREMENTS_FILE="requirements.txt"
ARG FUNCTION_ROOT="."
ARG RUNTIME_VERSION="3.8"
ARG LAMBDA_TASK_ROOT="/var/task"
ARG LAMBDA_RUNTIME_DIR="/var/runtime"
ENV LAMBDA_TASK_ROOT="/var/task"
ENV LAMBDA_RUNTIME_DIR="/var/runtime"

# 3) CREATE FUNCTION DIR
WORKDIR ${LAMBDA_TASK_ROOT}

# 4) COPY FILES.
# Copy The Main .py File.
COPY ${MAIN_FILE} ${LAMBDA_TASK_ROOT}
# Copy The .env File.
COPY ${ENV_FILE} ${LAMBDA_TASK_ROOT}
# Copy The requirements.txt File.
COPY ${REQUIREMENTS_FILE} ${LAMBDA_TASK_ROOT}
# Copy Helpers Folder.
COPY helpers/ ${LAMBDA_TASK_ROOT}/helpers/
# Copy Private Folder.
COPY priv/ ${LAMBDA_TASK_ROOT}/priv/
# Copy Source Data Folder.
COPY source_data/ ${LAMBDA_TASK_ROOT}/source_data/

# 5) DOWNLOAD & INSTALL CHROMEIUM + CHROMEDRIVER.
RUN apt-get update
RUN apt-get install -y software-properties-common
RUN add-apt-repository ppa:deadsnakes/ppa
RUN apt-get install -y wget \
  unzip \
  libx11-dev \
  nano \
  g++ \
  make \
  cmake \
  unzip \
  python3.7 \
  python3-pip \
  libcurl4-openssl-dev \
  libfontconfig \
  xvfb \
  libnss3-dev \
  libosmesa6-dev \
  ffmpeg

# 6) CREATE LINKS
RUN ln -s /usr/bin/pip3 /usr/bin/pip
RUN ln -s /usr/bin/python3.7 /usr/bin/python
RUN ln -s /usr/lib/x86_64-linux-gnu/libOSMesa.so /usr/local/bin/libosmesa.so

# 7) INSTALL DEPENDENCIES.
RUN python -m pip install --upgrade pip
RUN python -m pip install wheel
RUN python -m pip install urllib3
RUN python -m pip install awslambdaric
RUN python -m pip install -r requirements.txt --default-timeout=100

# 8) INSTALL CHROME
RUN wget https://github.com/adieuadieu/serverless-chrome/releases/download/v1.0.0-37/stable-headless-chromium-amazonlinux-2017-03.zip
RUN unzip ./stable-headless-chromium-amazonlinux-2017-03.zip
RUN rm ./stable-headless-chromium-amazonlinux-2017-03.zip
RUN mv -f ./headless-chromium /usr/local/bin/headless-chromium
RUN chmod 755 /usr/local/bin/headless-chromium

# 9) INSTALL CHROMEDRIVER
RUN wget https://chromedriver.storage.googleapis.com/2.37/chromedriver_linux64.zip
RUN unzip ./chromedriver_linux64.zip
RUN rm ./chromedriver_linux64.zip
RUN mv -f ./chromedriver /usr/local/bin/chromedriver
RUN chmod 755 /usr/local/bin/chromedriver

# 10) DEFINE ENV VARS
ENV AWS_LAMBDA_RUNTIME_API=""
ENV DISPLAY=":20"

# 11) SET CMD OF HANDLER.
ENTRYPOINT [ "/usr/bin/python", "-m", "awslambdaric" ]
CMD [ "main.lambda_handler" ]
```

And my Selenium setup in Python:
```
options = Options()
options.binary_location = "/usr/local/bin/headless-chromium"
options.add_argument("--headless")
options.add_argument("--disable-gpu")
options.add_argument("--window-size=1280x1696")
options.add_argument("--disable-application-cache")
options.add_argument("--disable-infobars")
options.add_argument("--no-sandbox")
options.add_argument("--hide-scrollbars")
options.add_argument("--enable-logging")
options.add_argument("--log-level=0")
options.add_argument("--single-process")
options.add_argument("--ignore-certificate-errors")
options.add_argument("--homedir=/tmp")
options.add_argument("--disable-gpu")
driver = webdriver.Chrome(
    service_args=["--verbose", "--log-path={}".format(logPath)],
    executable_path=f"/usr/local/bin/chromedriver",
    options=options
)
```

Thanks to the other who answered in the meantime - I would like to switch back to Amazon Linux eventually so those answers will help.


------------
    
    