---
layout: post
title:  "AWS S3 local exaample"
date:   2020-11-19 13:03:16 +0200
categories:
---
<pre class="hljs" style="display: block; overflow-x: auto; padding: 0.5em; background: rgb(40, 42, 54); color: rgb(248, 248, 242);">PS C:\Users\admin\apps&gt; sam init
<span class="hljs-keyword" style="color: rgb(139, 233, 253); font-weight: 700;">Which</span> template source would you like to <span class="hljs-keyword" style="color: rgb(139, 233, 253); font-weight: 700;">use</span>?
        1 - AWS Quick Start Templates
        2 - Custom Template Location
Choice: 1

<span class="hljs-keyword" style="color: rgb(139, 233, 253); font-weight: 700;">Which</span> runtime would you like to <span class="hljs-keyword" style="color: rgb(139, 233, 253); font-weight: 700;">use</span>?
        1 - nodejs12.x
      ....
        7 - nodejs10.x
        8 - python3.7
      .......
        14 - dotnetcore2.1
Runtime: 8

Project name [sam-<span class="hljs-keyword" style="color: rgb(139, 233, 253); font-weight: 700;">app</span>]: aws-<span class="hljs-keyword" style="color: rgb(139, 233, 253); font-weight: 700;">local</span>-examples

Cloning <span class="hljs-keyword" style="color: rgb(139, 233, 253); font-weight: 700;">app</span> templates from https:<span class="hljs-comment" style="color: rgb(98, 114, 164);">//github.com/awslabs/aws-sam-cli-app-templates.git</span>

AWS quick start application templates:
        1 - Hello World Example
        2 - EventBridge Hello World
        3 - EventBridge <span class="hljs-keyword" style="color: rgb(139, 233, 253); font-weight: 700;">App</span> from scratch (100+ Event Schemas)
        4 - Step Functions <span class="hljs-keyword" style="color: rgb(139, 233, 253); font-weight: 700;">Sample</span> <span class="hljs-keyword" style="color: rgb(139, 233, 253); font-weight: 700;">App</span> (Stock Trader)
Template selection: 1

-----------------------
Generating application:
-----------------------
Name: aws-<span class="hljs-keyword" style="color: rgb(139, 233, 253); font-weight: 700;">local</span>-examples
Runtime: python3.7
Dependency Manager: pip
Application Template: hello-world
Output Directory: .

Next steps can be found <span class="hljs-keyword" style="color: rgb(139, 233, 253); font-weight: 700;">in</span> the README <span class="hljs-keyword" style="color: rgb(139, 233, 253); font-weight: 700;">file</span> at ./aws-<span class="hljs-keyword" style="color: rgb(139, 233, 253); font-weight: 700;">local</span>-examples/README.md</pre>






<pre class="hljs" style="display: block; overflow-x: auto; padding: 0.5em; background: rgb(40, 42, 54); color: rgb(248, 248, 242);">PS C:<span class="hljs-tag">\<span class="hljs-name" style="color: rgb(241, 250, 140); font-weight: 700;">Users</span></span><span class="hljs-tag">\<span class="hljs-name" style="color: rgb(241, 250, 140); font-weight: 700;">admin</span></span><span class="hljs-tag">\<span class="hljs-name" style="color: rgb(241, 250, 140); font-weight: 700;">aws</span></span>-local-examples&gt; aws configure
AWS Access Key ID [****************72QZ]:
AWS Secret Access Key [****************N+N3]:
Default region name [eu-central-1]:
Default output format [text]:
PS C:<span class="hljs-tag">\<span class="hljs-name" style="color: rgb(241, 250, 140); font-weight: 700;">Users</span></span><span class="hljs-tag">\<span class="hljs-name" style="color: rgb(241, 250, 140); font-weight: 700;">admin</span></span><span class="hljs-tag">\<span class="hljs-name" style="color: rgb(241, 250, 140); font-weight: 700;">aws</span></span>-local-examples&gt; aws s3 ls
2020-11-19 15:08:38 my-backet-tarja-turunen-897979
2020-11-19 15:21:13 my-backet-tarja-turunen-897980
PS C:<span class="hljs-tag">\<span class="hljs-name" style="color: rgb(241, 250, 140); font-weight: 700;">Users</span></span><span class="hljs-tag">\<span class="hljs-name" style="color: rgb(241, 250, 140); font-weight: 700;">admin</span></span><span class="hljs-tag">\<span class="hljs-name" style="color: rgb(241, 250, 140); font-weight: 700;">aws</span></span>-local-examples&gt;</pre>






 <pre class="hljs" style="display: block; overflow-x: auto; padding: 0.5em; background: rgb(40, 42, 54); color: rgb(248, 248, 242);"><span class="hljs-keyword" style="color: rgb(139, 233, 253); font-weight: 700;">import</span> json
<span class="hljs-keyword" style="color: rgb(139, 233, 253); font-weight: 700;">import</span> boto3
<span class="hljs-comment" style="color: rgb(98, 114, 164);"># import requests</span>
<span class="hljs-comment" style="color: rgb(98, 114, 164);"># Creating the low level functional client</span>
client = boto3.client(<span class="hljs-string" style="color: rgb(241, 250, 140);">'s3'</span>)

<span class="hljs-function"><span class="hljs-keyword" style="color: rgb(255, 121, 198); font-weight: 700;">def</span> <span class="hljs-title" style="color: rgb(241, 250, 140); font-weight: 700;">lambda_handler</span><span class="hljs-params">(event, context)</span>:</span>
    <span class="hljs-comment" style="color: rgb(98, 114, 164);"># Fetch the list of existing buckets</span>
    clientResponse = client.list_buckets()

    <span class="hljs-comment" style="color: rgb(98, 114, 164);"># Print the bucket names one by one</span>
    print(<span class="hljs-string" style="color: rgb(241, 250, 140);">'Printing bucket names...'</span>)
    bucketList = []
    <span class="hljs-keyword" style="color: rgb(139, 233, 253); font-weight: 700;">for</span> bucket <span class="hljs-keyword" style="color: rgb(139, 233, 253); font-weight: 700;">in</span> clientResponse[<span class="hljs-string" style="color: rgb(241, 250, 140);">'Buckets'</span>]:
        bucketList.append(bucket[<span class="hljs-string" style="color: rgb(241, 250, 140);">"Name"</span>])
        print(f<span class="hljs-string" style="color: rgb(241, 250, 140);">'Bucket Name: {bucket["Name"]}'</span>)

    <span class="hljs-keyword" style="color: rgb(139, 233, 253); font-weight: 700;">return</span> {
        <span class="hljs-string" style="color: rgb(241, 250, 140);">"statusCode"</span>: <span class="hljs-number">200</span>,
        <span class="hljs-string" style="color: rgb(241, 250, 140);">"body"</span>: json.dumps(
            {<span class="hljs-string" style="color: rgb(241, 250, 140);">"message"</span>: bucketList}
        )
    }
</pre>



<pre class="hljs" style="display: block; overflow-x: auto; padding: 0.5em; background: rgb(40, 42, 54); color: rgb(248, 248, 242);">PS C:\Users\admin\aws-local-examples&gt; sam build --use-container
Starting Build inside <span class="hljs-keyword" style="color: rgb(139, 233, 253); font-weight: 700;">a</span> container
Building codeuri: hello_world/ <span class="hljs-keyword" style="color: rgb(139, 233, 253); font-weight: 700;">runtime</span>: <span class="hljs-keyword" style="color: rgb(139, 233, 253); font-weight: 700;">python3</span>.<span class="hljs-number">8</span> metadat<span class="hljs-variable" style="color: rgb(241, 250, 140);">a:</span> {} <span class="hljs-keyword" style="color: rgb(139, 233, 253); font-weight: 700;">function</span><span class="hljs-variable" style="color: rgb(241, 250, 140);">s:</span> [<span class="hljs-string" style="color: rgb(241, 250, 140);">'HelloWorldFunction'</span>]

Fetching amazon/aws-sam-cli-build-image-<span class="hljs-keyword" style="color: rgb(139, 233, 253); font-weight: 700;">python3</span>.<span class="hljs-number">8</span> Docker container image......
Mounting C:\Users\admin\aws-local-examples\hello_world <span class="hljs-keyword" style="color: rgb(139, 233, 253); font-weight: 700;">as</span> /tmp/samcli/<span class="hljs-keyword" style="color: rgb(139, 233, 253); font-weight: 700;">source</span>:ro,delegated inside <span class="hljs-keyword" style="color: rgb(139, 233, 253); font-weight: 700;">runtime</span> container

Build Succeeded

Built Artifacts  : .aws-sam\build
Built Template   : .aws-sam\build\template.yaml

Commands you can use <span class="hljs-keyword" style="color: rgb(139, 233, 253); font-weight: 700;">next</span>
=========================
[*] Invoke Function: sam local invoke
[*] Deploy: sam deploy --guided

Running PythonPipBuilder:ResolveDependencies
Running PythonPipBuilder:CopySource
PS C:\Users\admin\aws-local-examples&gt; sam local start-api
C:\Program Files\Amazon\AWSSAMCLI\<span class="hljs-keyword" style="color: rgb(139, 233, 253); font-weight: 700;">runtime</span>\lib\site-packages\chevron\renderer.<span class="hljs-keyword" style="color: rgb(139, 233, 253); font-weight: 700;">py</span>:<span class="hljs-number">73</span>: SyntaxWarnin<span class="hljs-variable" style="color: rgb(241, 250, 140);">g:</span> <span class="hljs-string" style="color: rgb(241, 250, 140);">"is"</span> with <span class="hljs-keyword" style="color: rgb(139, 233, 253); font-weight: 700;">a</span> literal. Did you mean <span class="hljs-string" style="color: rgb(241, 250, 140);">"=="</span>?
  <span class="hljs-keyword" style="color: rgb(139, 233, 253); font-weight: 700;">if</span> scope <span class="hljs-keyword" style="color: rgb(139, 233, 253); font-weight: 700;">is</span> <span class="hljs-number">0</span>:
Mounting HelloWorldFunction at http://<span class="hljs-number">127.0</span>.<span class="hljs-number">0.1</span>:<span class="hljs-number">3000</span>/hello [GET]
You can now <span class="hljs-keyword" style="color: rgb(139, 233, 253); font-weight: 700;">browse</span> <span class="hljs-keyword" style="color: rgb(139, 233, 253); font-weight: 700;">to</span> the above endpoints <span class="hljs-keyword" style="color: rgb(139, 233, 253); font-weight: 700;">to</span> invoke your functions. You <span class="hljs-keyword" style="color: rgb(139, 233, 253); font-weight: 700;">do</span> not need <span class="hljs-keyword" style="color: rgb(139, 233, 253); font-weight: 700;">to</span> restart/reload SAM CLI <span class="hljs-keyword" style="color: rgb(139, 233, 253); font-weight: 700;">while</span> working <span class="hljs-keyword" style="color: rgb(139, 233, 253); font-weight: 700;">on</span> your functions, <span class="hljs-keyword" style="color: rgb(139, 233, 253); font-weight: 700;">changes</span> will <span class="hljs-keyword" style="color: rgb(139, 233, 253); font-weight: 700;">be</span> reflected instantly/automatically. You <span class="hljs-keyword" style="color: rgb(139, 233, 253); font-weight: 700;">only</span> need <span class="hljs-keyword" style="color: rgb(139, 233, 253); font-weight: 700;">to</span> restart SAM CLI <span class="hljs-keyword" style="color: rgb(139, 233, 253); font-weight: 700;">if</span> you <span class="hljs-keyword" style="color: rgb(139, 233, 253); font-weight: 700;">update</span> your AWS SAM template
<span class="hljs-number">2020</span>-<span class="hljs-number">11</span>-<span class="hljs-number">19</span> <span class="hljs-number">17</span>:<span class="hljs-number">28</span>:<span class="hljs-number">16</span>  * Running <span class="hljs-keyword" style="color: rgb(139, 233, 253); font-weight: 700;">on</span> http://<span class="hljs-number">127.0</span>.<span class="hljs-number">0.1</span>:<span class="hljs-number">3000</span>/ (Press CTRL+C <span class="hljs-keyword" style="color: rgb(139, 233, 253); font-weight: 700;">to</span> <span class="hljs-keyword" style="color: rgb(139, 233, 253); font-weight: 700;">quit</span>)</pre>


<pre class="hljs" style="display: block; overflow-x: auto; padding: 0.5em; background: rgb(40, 42, 54); color: rgb(248, 248, 242);">PS <span class="hljs-string" style="color: rgb(241, 250, 140);">C:</span>\Users\admin\aws-local-examples&gt; curl <span class="hljs-string" style="color: rgb(241, 250, 140);">localhost:</span><span class="hljs-number">3000</span>/hello
{<span class="hljs-string" style="color: rgb(241, 250, 140);">"message"</span>: [<span class="hljs-string" style="color: rgb(241, 250, 140);">"my-backet-tarja-turunen-897979"</span>, <span class="hljs-string" style="color: rgb(241, 250, 140);">"my-backet-tarja-turunen-897980"</span>]}</pre>