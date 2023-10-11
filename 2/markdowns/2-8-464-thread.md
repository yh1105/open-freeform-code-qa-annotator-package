
# Post \#58133166 [Link](https://stackoverflow.com/questions/58133166/)

## error "fork/exec /var/task/main: no such file or directory" while executing aws-lambda function

**Vote**: 46 (133/702) **Views**: 34940 (208/702) 

**Internal ID** \#2-8-464

Created at 2019-09-27 10:57:58

Tags: `go` `amazon-s3` `aws-lambda` `handler`

----------

#### Metadata:

Area: `Back-end`

Language: `go` (full parsed tag list: `go`)

----------

**Notepad**


----------

Getting `error fork/exec /var/task/main: no such file or directory` while executing lambda function.
I am using windows platform to run and build code in Go.
I have done following steps to deploy go aws-lambda handler:

1. Written code in go language with VSCode in windows platform
2. Build project with : go build main.go
3. Convert main.exe to main.zip
4. Uploaded main.zip with handler name main aws-lambda fuction using aws console account
5. Created test event to test lambda function
6. Got error "fork/exec /var/task/main: no such file or directory while executing lambda function"


```
package main

import (
    "fmt"

    "github.com/aws/aws-lambda-go/lambda"
)

// Request represents the requested object
type Request struct {
    ID    int    `json:"ID"`
    Value string `json:"Value"`
}

// Response represents the Response object
type Response struct {
    Message string `json:"Message"`
    Ok      bool   `json:"Ok"`
}

// Handler represents the Handler of lambda
func Handler(request Request) (Response, error) {
    return Response{
        Message: fmt.Sprint("Process Request Id %f", request.ID),
        Ok:      true,
    }, nil
}

func main() {
    lambda.Start(Handler)
}
```

build command
```
go build main.go
```

Detail Error in AWS console
```
{
  "errorMessage": "fork/exec /var/task/main: no such file or directory",
  "errorType": "PathError"
}
```

Log Output in AWS console
```
START RequestId: 9ef206ed-5538-407a-acf0-06673bacf2d7 Version: $LATEST
fork/exec /var/task/main: no such file or directory: PathError
null
END RequestId: 9ef206ed-5538-407a-acf0-06673bacf2d7
REPORT RequestId: 9ef206ed-5538-407a-acf0-06673bacf2d7  Duration: 0.64 ms   Billed Duration: 100 ms Memory Size: 512 MB Max Memory Used: 31 MB  Init Duration: 1.49 ms
```



----------
        
## Answer \#0

**Accepted** Vote: 25

Created at 2019-09-27 12:58:29

------------

Run following commands in command prompt
```
set GOOS=linux
set GOARCH=amd64
set CGO_ENABLED=0
```

After this , build your project and upload zip file to aws console lambda
like this
`GOOS=linux GOARCH=amd64 CGO_ENABLED=0 go build -o main main.go`
Reference Link : [https://github.com/aws/aws-lambda-go](https://github.com/aws/aws-lambda-go)


------------
    
    
## Answer \#1

 Vote: 40

Created at 2020-07-08 18:00:15

------------

In my case the problem was default setted handler to '' function.
Needed to change it to '' via AWS Lambda view panel -> Basic Settings -> Edit.
[](https://i.stack.imgur.com/0yTU4.png)


------------
    
    
## Answer \#2

 Vote: 11

Created at 2020-01-09 20:10:50

------------

There are two reasons can happen:


1. You did't use GOOS=linux GOARCH=amd64 with go build, so try: GOOS=linux GOARCH=amd64 go build -o main main.go
2. You used to build this programm some CI function with golang-alpine base image, so try to use full golang image instead.




------------
    
    
## Answer \#3

 Vote: 10

Created at 2020-09-18 06:50:52

------------

Recently I was facing similar issue, and I solved it. As the error says it s finding executable with handle name, so you should name your executable same as handler.
Follow these steps and your will not get this error, I am using PowerShell
```
> go.exe get -u github.com/aws/aws-lambda-go/cmd/build-lambda-zip # If you do not this have utility earlier
> $env:GOOS = "linux"
> $env:GOARCH = "amd64"
> $env:CGO_ENABLED = "0"
> go build -o .\Handler .\main.go # considering your are in the same directory where your main.go or handler code is present
> ~\Go\Bin\build-lambda-zip.exe -o .\Handler.zip .\Handler
```

Upload this code, and change handler name to Handler(name of the executable that you created above)
[](https://i.stack.imgur.com/MfgzW.png)
Let me know if this helps.


------------
    
    
## Answer \#4

 Vote: 3

Created at 2021-03-27 17:01:32

------------

In my embarrasing case I was zipping the folder from outside so the path in the zip I was uploading was 'folder/main'


------------
    
    
## Answer \#5

 Vote: 2

Created at 2022-06-24 23:16:45

------------

You can find the description when you click `AWS Lambda` > `Functions` > `<Your function name>` > `Handler info`
[](https://i.stack.imgur.com/D6Hjt.png)
> You can tell the Lambda runtime which handler method to invoke by
setting the handler parameter on your function's configuration.When you configure a function in Go, the value of the handler setting
is the executable file name. For example, using Handler would call the
function in the package main of the Handler executable file.
It means that your file which has a handler function is not necessarily named `main.go`. My Handler on AWS is `hello` and I use this command to create executable file `GOOS=linux GOARCH=amd64 CGO_ENABLED=0 go build -o hello hello.go`
Here is my case. I am learning Terraform to deploy AWS Lambda function.
I am following an [terraform official document](https://learn.hashicorp.com/tutorials/terraform/lambda-api-gateway) to deploy a hello function.
The page guides me to create a function using Node.js and I rewrite it using Go.
Here is my modified part of main.tf.
```
resource "aws_lambda_function" "hello_world" {
  function_name = "HelloWorld"
  s3_bucket = aws_s3_bucket.lambda_bucket.id
  s3_key    = aws_s3_object.lambda_hello_world.key
  runtime = "go1.x"
  handler = "hello"
  source_code_hash = data.archive_file.lambda_hello_world.output_base64sha256
  role = aws_iam_role.lambda_exec.arn
}
```

I deleted the `hello.js` file and created the `hello.go` file.
My `hello.go` is the same as [AWS example](https://docs.aws.amazon.com/lambda/latest/dg/golang-handler.html)
```
package main

import (
    "fmt"
    "context"
    "github.com/aws/aws-lambda-go/lambda"
)
type MyEvent struct {
    Name string `json:"name"`
}

func HandleRequest(ctx context.Context, name MyEvent) (string, error) 
{
    return fmt.Sprintf("Hello %s!", name.Name ), nil
}

func main() {
    lambda.Start(HandleRequest)
}
```

After deploying the lambda function using terraform, we can verify it.
```
% aws lambda invoke --region=us-east-1 --function-name=HelloWorld response.json
{
    "StatusCode": 200,
    "ExecutedVersion": "$LATEST"
}
```

I hope this is helpful.


------------
    
    
## Answer \#6

 Vote: 1

Created at 2022-09-18 22:49:16

------------

My issue (I think) was the zipping tool / GOOS=windows was set wrong.
[Zip Go file using AWS Lambda Tool](https://stackoverflow.com/questions/62311824/zip-go-file-using-aws-lambda-tool)
My setup is windows + following a golang tutorial. Published to lambda, all appeared fine, got the error and was correctly named and built etc.
I think it's the zipping tool, using build-lambda-zip and now it works for me. Note: I tried using another command to get the tool
`go.exe get -u github.com/aws/aws-lambda-go/cmd/build-lambda-zip` however this didn't actually get the tool for me.
Hopefully this saves someone else half an hour of googling


------------
    
    
## Answer \#7

 Vote: 0

Created at 2020-10-02 04:59:45

------------

In my case it was related to my Go setting.
As I use Go Version Manager, I had to run the following to specify the right Go path before compiling my project.
```
gvm use go1.x.x
```

Proceed to compile then deploy.


------------
    
    
## Answer \#8

 Vote: 0

Created at 2020-10-12 00:49:55

------------

My case was a strange one, but I'll leave it here in case someone needs it.
I built my executable in a Docker container and then used `Docker cp` to put the file in a local directory. Then I zipped the executable and deployed it.
Apparently, `Docker cp` creates a symlink-like file rather than an actual file, so it just wouldn't work.
My solution was just to use `sudo cp` and copy the executable as another file, which created a physical file, and deployed that instead.


------------
    
    
## Answer \#9

 Vote: 0

Created at 2021-05-27 20:43:14

------------

This error has nothing to do with the program; it's a configuration issue.
Please try to give the complied go file name in the lambda console under Go Runtime settings -
In my case my go lambda compiled file is test so configured Handler as test and it worked for me.
[Example Configuration](https://i.stack.imgur.com/nos24.png)


------------
    
    
## Answer \#10

 Vote: 0

Created at 2021-09-10 12:04:28

------------

In my case, the solution is very simple.
I was wrong with the command build.
what I did was `go build .` instead of `go build main.go`
the zip file should be `main.zip`.
and the handler info section in console AWS should be named `main`.
not `handler` because lambda calls the main function first


------------
    
    
## Answer \#11

 Vote: 0

Created at 2021-11-17 12:20:43

------------

If deploying with SAM cli, make sure to run "sam build" before the "sam deploy"


------------
    
    
## Answer \#12

 Vote: 0

Created at 2021-12-10 07:44:47

------------

In my case this had to do with the image I was using to build the go binaries.  I switched from using alpine to using amazon linux 2 and the problem was solved.


------------
    
    
## Answer \#13

 Vote: 0

Created at 2022-03-09 15:58:02

------------

setting environment variable CGO_ENABLED to 0 solved my problem, this is when using sam local start-api to locally test endpoint


------------
    
    