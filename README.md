# StreamingPro

This document is for StreamingPro developers. User's manual is now on its way.

## Introduction

StreamingPro is not a complete
application, but rather a code library and API that can easily be used
to build your streaming application which may run on Spark Streaming and Storm.

StreamingPro also make it possible that assembling components in configuration file to build a
multi streaming job instead of programing. Of source ,we also encourage you program using API provided
by StreamingPro which is more easy to use.


## Setup Project

Since this project  depends on 

* [ServiceFramework](https://github.com/allwefantasy/ServiceFramework.git)
* [csdn_common](https://github.com/allwefantasy/csdn_common.git)
* [ServiceframeworkDispatcher](https://github.com/allwefantasy/ServiceframeworkDispatcher.git)

you should install them in your local maven repository/private maven repository .

Step 1

```
git clone https://github.com/allwefantasy/csdn_common.git
cd csdn_common
mvn -DskipTests clean install
```

step 2

```
git clone https://github.com/allwefantasy/ServiceFramework.git
cd ServiceFramework
mvn -DskipTests clean install
```

step 3

```
git clone https://github.com/allwefantasy/ServiceframeworkDispatcher.git
cd ServiceframeworkDispatcher
mvn -DskipTests clean install
```

step 4 

```
git clone https://github.com/allwefantasy/streamingpro.git

```

step 5

```
Import StreamingPro to your IDE.
```

Tips:

StreamingPro is a maven project and Intellj Idea is recommended cause it  have more powerful scala support which make
 your coding life more easy.
 
 
## Run Your First Application
 
First make sure you have selected `debug` profile in your IDE,then you can run:

```
streaming.core.LocalStreamingApp
```

when stared , you can see some message like follow:


```
+---+---+
|  a|  b|
+---+---+
|  1|  5|
+---+---+
```

Congratulations, everything is fine and you just run your first Spark Streaming Application.


You can find `strategy.v2.json` in `src/main/resource-debug` directory which describe what your streaming application have 
done .

```
{
  "test": {
    "desc": "测试",
    "strategy": "streaming.core.strategy.SparkStreamingStrategy",
    "algorithm": [],
    "ref": [],
    "compositor": [
      {
        "name": "streaming.core.compositor.spark.streaming.source.MockInputStreamCompositor",
        "params": [
          {           
          }
        ]
      },
      {
        "name": "streaming.core.compositor.spark.streaming.transformation.SingleColumnJSONCompositor",
        "params": [
          {
            "name": "a"
          }
        ]
      },
      {
        "name": "streaming.core.compositor.spark.streaming.transformation.JSONTableCompositor",
        "params": [
          {
            "tableName": "test"
          }
        ]
      },
      {
        "name": "streaming.core.compositor.spark.streaming.transformation.SQLCompositor",
        "params": [
          {
            "sql": "select a, \"5\" as b from test",
            "outputTableName": "test2"
          }
        ]
      },
      {
        "name": "streaming.core.compositor.spark.streaming.transformation.SQLCompositor",
        "params": [
          {
            "sql": "select a,b from test2"
          }
        ]
      },
      {
        "name": "streaming.core.compositor.spark.streaming.output.SQLPrintOutputCompositor",
        "params": [
          {
          }
        ]
      }
    ],
    "configParams": {
    }
  }
}
```

Step1: Configure  MockInputStreamCompositor to generate some lines.
Step2: Configure  SingleColumnJSONCompositor to convert string to Json string.
Step3: Configure  JSONTableCompositor to map Json to SQL table.
Step4: Configure  multi SQLCompositor to process data 
Step5: Finally, configure SQLPrintOutputCompositor to print result.


## How To Add New Compositor

In StreamingPro,every transformation can be implemented by Compositor. Suppose 
you wanna implements `map` function in Spark Streaming and convert a line into json 
string.

Create a class `AddColumnForJSONCompositor` which extends `BaseMapCompositor`

```
class AddColumnForJSONCompositor[T] extends BaseMapCompositor[T, String, String] {
  override def map: (String) => String = {
    (line: String) => {
      val res = JSONObject.fromObject(line)
      res.put("d", "1")
      res.toString
    }
  }
}
```

override map method and do anything you want to the line putted by Streaming program.








 