+++
title = "Python North East - Introduction to Apache Spark"
date = "2015-11-11"
+++

This month's [Python North East](http://pythonnortheast.co.uk/) talk will be an
introduction to [Apache Spark](http://spark.apache.org/), given by yours truly.

The presentation slides for the talk can be found 
[here](https://docs.google.com/presentation/d/1UAXYZ8Dee351Ajjebiw6OOnlCOowsI1cW-S3mrpkAYE/edit?usp=sharing).

## Code Dojo

### Get Spark

After the talk there will be chance to have a play with Spark yourself. Firstly you need
to download the [Spark
binaries](http://www.apache.org/dyn/closer.lua/spark/spark-1.5.2/spark-1.5.2-bin-hadoop2.6.tgz)
and unpack them to a folder on your local machine.

I also have a few USB keys with the binaries on, so wave your hand if the network is slow.

### Run the REPL

If you want to run an interactive PySpark interpreter then run the following command
inside the spark directory:

```
$ ./bin/pyspark --master local[2]
```

The master flag lets you specify the master node of the cluster, however the local option
allows you to run this on your machine with the specified number of worker threads. Be
careful not to set this number two high (certainly no higher than the number of CPU cores
you have available) or you will freeze your machine.

If you want to be able to import packages not in the standard library (or numpy) the you
can include the .egg, .zip or .py files as an argument. You can then use them as a
standard import:

```
$ ./bin/pyspark --master local[2] --py-files code.py
```

### Run a script

You can write your scripts and submit them to spark using the spark-submit script in the
bin folder. This works similarly to the pySpark shell:

``` 
$ ./bin/spark-submit --master local[2] --py-files dependencies.py path-to-script/myscript.py args
```

Remember to add the following import to your script:

```
from pyspark import SparkContext
```

The spark-submit script will handle the dependencies for this automatically. For your
scripts you also need to form a spark context object yourself (unlike for the pySpark
shell where this is provided for you):

``` 
sc = SparkContext("local[2]", "MyAppName")
```


Supplying the master address in the script is optional, if you do you don't need to use the `--master` flag in the spark-submit call.

## Word Count

The easiest way to get up and running with Spark is to try the word count example. You can run this via the shell or copy and paste the code into a script and use spark-submit:

```
text_file = sc.textFile("path/to/textfile.txt")

words = text_file.flatMap(lambda line: line.split(" "))

word_tuples = words.map(lambda word: (word, 1))

counts = word_tuples.reduceByKey(lambda a, b: a + b)

#The .collect() method will return the RDD as list of tuples
#Only use this if you know the resulting list will fit into the memory on your machine!
results = counts.collect()
```

A good text to try this on is the Complete Works of Shakespeare, freely available as a
[text file](http://www.gutenberg.org/ebooks/100.txt.utf-8) from [Project
Gutenberg](http://www.gutenberg.org/wiki/Main_Page).

Obviously the code above doesn't deal with punctuation etc and only gives you a word
count. The power of Python can then be used to do cool things with this "big" data!

## Spark Streaming Word Count

An easy way to see Spark Streaming in action is to extend the word count example to count
incoming words to a port on your machine:

```
from __future__ import print_function

from pyspark import SparkContext
from pyspark.streaming import StreamingContext

#Set the time window in seconds
time_window = 1

sc = SparkContext(master="local[2]", appName="PythonStreamingNetworkWordCount")
ssc = StreamingContext(sc, time_window)

#Create an RDD from the words coming into this machine on the specified port
lines = ssc.socketTextStream("localhost", 9999)

counts = lines.flatMap(lambda line: line.split(" ")).map(lambda word: (word, 1)).reduceByKey(lambda a, b: a+b)

counts.pprint()

ssc.start()
ssc.awaitTermination()
```

To provide input to the streaming job, you need to first run a Netcat server:

```
$ nc -lk 9999
```

and then run the spark script using spark-submit. Now type away to your hearts content in
your Netcat terminal and see the word counts pop up on the spark terminal.

Obliviously this basic script can be extended to do all kinds of interesting things!
