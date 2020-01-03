---
layout: post
title: "Issues For Content Detection With Apache Tika"
description: ""
category: 
tags: []
---
{% include JB/setup %}
## 场景

在上传文件功能中，需要限制可上传文件的类型。不仅需要检测后缀，也需要检测文件内容真正的类型，以防不合法类型的文件通过修改后缀名而通过限制。

Apache Tika大部分情况下可以完成检测文件内容的功能。但是也有纰漏。

## Apache Tika可能出现的问题 

1. Application/xml 类型的文件被Tika识别成text/html。Application/xml类型的文件本应接受，但被reject。 
2. .js后缀的文件不能被上传，说明业务上不允许js文件上传，但改后缀名为valid的后缀（如.txt）之后可上传成功。Tika的detector并没有把真正的content type(application/javascript)检测出来。 
3. 不能在所有情况下检测到精确的文件类型。如类型是js类型的文件被检测到的content-type有时是text/plain。 

## 一部分测试数据 

（可接受后缀：.txt, .jpg。不可接受后缀：.html, .js） 

| Suffix         | Type in content | Detected file type by Tika | Content-type in MultipartFile from http request | Accepted |
| -------------- | --------------- | -------------------------- | ----------------------------------------------- | -------- |
| .txt (valid)   | Xml             | text/html                  | text/plain                                      | False    |
| .xml (valid)   | Xml             | text/html                  | application/xml                                 | False    |
| .xml (valid)   | Plain text      | text/plain                 | Application/xml                                 | True     |
| .jpg(valid)    | Plain text      | text/pain                  | image/jpeg                                      | True     |
| .js(illegal)   | js              | application/javascript     | application/javascript                          | False    |
| .jpg (valid)   | js              | text/plain                 | Image/jpeg                                      | True     |
| .html(illegal) | Html text       | text/html                  | text/html                                       |          |
| .xml(valid)    | Html text       | text/html                  | application/xml                                 | False    |

## Tika的检测原理 

对单个文件，先使用MIME magic detection，根据输入文件的开端存在的special ("magic") patterns of bytes检测到文件的类型。再根据传入的metadata中的文件名，或已知的type对类型进行校正。如第一步检测到的是text/plain，如果文件名或后缀有可判断文件类型的信息，如css-content.css，根据文件名修正为更精确的text/css. 

对有container的文件，如打包的文件，在有Tika Parsers依赖包的情况下使用Container aware detection检测包中文件的内容的类型。

但是，Tika不能在所有情况下检测到精确的类型。如内容为javascript，但文件名中无文件类型信息的文件，Tika的检测结果是text/plain。不能检测到其子类型，如tika-mimetypes.xml中所示： 

<div style="text-align:center"><img src ="/assets/images/tika-mimetypes.xml.png" style="height:300px;" /></div>

目前没有找到替换Tika的工具，待持续更新。

## References： 

### 定义： 

**MIME type**: Media type is a two-part identifier for file formats and format contents transmitted on the [Internet](https://en.wikipedia.org/wiki/Internet). (Can be used to help communication between clients and servers, eg. Client use it to determine what to do with the file.) 

A media type consists of a *type* and a *subtype*, which is further structured into a *tree*. A media type can optionally define a *suffix* and *parameters*:

The currently registered types are: application, audio, example, font, image, message, model, multipart, text and video.

As an example, an HTML file might be designated text/html; charset=UTF-8. In this example, text is the type, html is the subtype, and charset=UTF-8 is an optional parameter indicating the character encoding.

### Tika content detection的所有功能

1. Mime magic detection: by looking for special ("magic") patterns of bytes near the start of the file,it is often possible to detect the type of the file. 
2. Resource name based detection: Where the name of the file is known, it is sometimes possible to guess the file type from the name or extension. Within the tika-mimetypes.xml file is a list of patterns which are used to identify the type from the filename. 
3. Known content type detection: Sometimes, the mime type for a file is already known, such as when downloading from a webserver, or when retrieving from a content store.
4. The default  Mime type detection: By default, the mime type detection in Tika is provided by [org.apache.tika.mime.MimeTypes](https://tika.apache.org/1.23/api/org/apache/tika/mime/MimeTypes.html). This detector makes use of tika-mimetypes.xml to power magic based and filename based detection. 
5. Container aware detection: Used to detect the content type of the content inside a container. Several common file formats are actually held within a common container format. One example is the PowerPoint .ppt and Word .doc formats, which are both held within an OLE2 container. Another is Apple iWork formats, which are actually a series of XML files within a Zip file. Using magic detection, it is easy to spot that a given file is an OLE2 document, or a Zip file. For these cases, the additional container aware detectors contained in the Tika Parsers jar should be used.
6. The default Tika detector: [org.apache.tika.detect.DefaultDetector](https://tika.apache.org/1.23/api/org/apache/tika/detect/DefaultDetector.html) which auto-detects (based on service files) the available detectors at runtime, and tries these in turn to identify the file type. If only Tika Core is available, the Default Detector will work only with Mime Magic and Resource Name detection. However, if Tika Parsers (and its dependencies!) are available, additional detectors which known about containers (such as zip and ole2) will be used. 
7. Language detection: Tika is able to help identify the language of a piece of text, which is useful when extracting text from document formats which do not include language information in their metadata. 

**Ref:** https://tika.apache.org/1.23/detection.html
