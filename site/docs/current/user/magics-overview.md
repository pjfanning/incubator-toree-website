---
layout: doc
title: Magics Overview
type: doc
section: user
weight: 45
tagline: Apache Project !
---
<!--
{% comment %}
Licensed to the Apache Software Foundation (ASF) under one or more
contributor license agreements.  See the NOTICE file distributed with
this work for additional information regarding copyright ownership.
The ASF licenses this file to you under the Apache License, Version 2.0
(the "License"); you may not use this file except in compliance with
the License.  You may obtain a copy of the License at

http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
{% endcomment %}
-->

{% include JB/setup %}

# Overview of magics for Apache Toree

The concept of magics stems from IPython, where various pieces of functionality
are provided through line and cell magics. Line magics are represented by a
single `%` followed by the magic name (e.g. `%AddJar <args>`) and only
allow for a single line of content. Cell magics are represented by `%%` 
followed by the magic name (e.g. `%%HTML <args>`) and allow for multiple
lines of content. See the examples below for more details.

## Line Magics

### Description

A line magic is basically a function that is supported by the kernel to expand its capabilities. 
Line magics are placed at the beginning of a line, prefixed by a `%` symbol, and receive the rest of the line as arguments:

```scala
// foo and bar will be arguments to ExampleLineMagic
%ExampleLineMagic foo bar
```

Multiple line magics can be invoked in a single cell:

```scala
val x = 3
%AddDeps com.google.guava guava 18.0
val z = 5
%lsmagic
import com.google.common.base.Strings._
```

### Other Things to Note

- Magic names are case insensitive; if a line magic `AddJar` exists, then `%addjar`, `%ADDJar`, and all other variants are valid. 

- Each magic has its own arguments; usage information can be obtained for a magic by typing `%<MagicName>`.

- Line magics receive the _literal_ rest of the line as arguments, so the following string interpolation will not work:
```scala
for(i <- (1 to 10)) 
  %addDeps s"com.google.guava guava $i"
```


### Available Line Magics

* **AddDeps** _<my.company> <artifact-id> <version> [--transitive]_

    * Adds the specified dependencies from Maven Central to the Spark Kernel
      and Spark cluster

    * Requires the company name, artifact id, and version of the dependency

    * Including _--transitive_ will include all needed dependencies

    * Examples:
        * Adding a single library with all of its dependencies
            ```scala           
            %AddDeps org.apache.spark spark-streaming-kafka_2.10 1.1.0 --transitive
            ```

        * Using the programmatic API
            ```scala           
            kernel.magics.addDeps("org.apache.spark spark-streaming-kafka_2.10 1.1.0 --transitive")
            ```
* **AddJar** _<jar-path> [-f]_

    * Adds the specified jars to the Spark Kernel and Spark cluster

    * Requires the path to the jar, which can either be a local path or remote
      jar hosted via HTTP

    * Including _-f_ will ignore any cached jars and redownload remote
      jars

    * Examples:

        * Adding a single jar from HTTP and forcing redownload if cached
            ```scala 
            %AddJar http://example.com/some_lib.jar -f
            ```

        * Adding a single jar from the file system relative to the kernel
            ```scala 
            %AddJar file:/path/to/some/lib.jar
            ```

        * Using the programmatic API
            ```scala            
            kernel.magics.addJar("http://example.com/some_lib.jar -f")
            ```

* **LSMagic**

    * Lists all available magics in the kernel

    * Examples:

        * Percent syntax
            ```scala 
            %LSMagic
            ```

        * Using the programmatic API
            ```scala 
            kernel.magics.lsMagic()
            ```

* **ShowTypes**

    * Controls whether type information for a result is displayed. 

    * Examples:
    
        * Get current ShowTypes status
        ```scala
          %ShowTypes
        ```

        * Enable ShowTypes
        ```scala
          %ShowTypes on
        ```

        * Disable ShowTypes
        ```scala
          %ShowTypes off
        ```

* **Truncation**

    * Control whether statements results are truncated or not (default on)
    
    * Examples:
    
        * Get current truncation status
        ```scala
          %Truncation
        ```

        * Enable truncation
        ```scala
          %Truncation on
        ```

        * Disable truncation
        ```scala
          %Truncation off
        ```


## Cell Magics

### Description

Cell Magics are magics that take the rest of the cell as input. Unlike Line Magics, Cell Magics can alter the output of a cell and must be the first thing in the cell.

As an example, the `%%HTML` cell magic renders the contents of the cell as HTML:
```scala
%%HTML
<h1>Hello World!</h1>
// more arguments...
```

Magic names are case insensitive, so _AddJar_ is the same as _addjar_.

### Available Cell Magics

* **DataFrame**

    * The %%DataFrame magic is used to convert a Spark SQL DataFrame into various formats. 
    Currently, json, html, and csv are supported. 

    * Examples:

        * Percent syntax
            ```scala 
            %%DataFrame
            ```


* **Html** _<html-code>_

    * Returns the given code with a HTML MIME-type.

    * Examples:

        * Percent syntax
            ```scala 
            %%Html
            <h1>Hello</h1>
            <p>World</p>
            ```

        * Using the programmatic API
            ```scala 
            kernel.magics.html("<h1>Hello</h1><p>World</p>")
            ```

* **JavaScript** _<javascript-code>_

    * Returns the given code with an `application/javascript` MIME-type.

    * Examples:

        * Percent syntax
            ```scala 
            %%JavaScript
            var x = 3
            var y = x + 2
            ```

        * Using the programmatic API
            ```scala 
            kernel.magics.javascript("var x = 3; var y = x + 2")
            ```
          
* **SQL**

## Examples

A [Magic Tutorial](https://github.com/apache/incubator-toree/blob/master/etc/examples/notebooks/magic-tutorial.ipynb)
notebook is available where one can learn more about magics and experiment with its behavior.