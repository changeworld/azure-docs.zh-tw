---
title: 搭配 MapReduce 與 HDInsight 上的 Apache Hadoop
description: 瞭解如何在 HDInsight 叢集中的 Apache Hadoop 上執行 Apache MapReduce 作業。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 33d96262ca2e2ff3003fbf2b40ce9ceb496337ae
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944255"
---
# <a name="use-mapreduce-in-apache-hadoop-on-hdinsight"></a>搭配 MapReduce 與 HDInsight 上的 Apache Hadoop

了解如何在 HDInsight 叢集上執行 MapReduce 工作。

## <a name="example-data"></a>範例資料

HDInsight 提供各種範例資料集，這些範例資料及儲存在 `/example/data` 和 `/HdiSamples` 目錄。 這些目錄位於您的叢集預設儲存體中。 在本文件中，我們使用 `/example/data/gutenberg/davinci.txt` 檔案。 此檔案包含達文西 da 達文西的筆記本。

## <a name="example-mapreduce"></a>範例 MapReduce

範例 MapReduce 字數統計應用程式會包含您的 HDInsight 叢集。 此範例位於您叢集的預設儲存體上的 `/example/jars/hadoop-mapreduce-examples.jar`。

下列 Java 程式碼是 `hadoop-mapreduce-examples.jar` 檔案中包含的 MapReduce 應用程式原始碼︰

```java
package org.apache.hadoop.examples;

import java.io.IOException;
import java.util.StringTokenizer;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
import org.apache.hadoop.util.GenericOptionsParser;

public class WordCount {

    public static class TokenizerMapper
        extends Mapper<Object, Text, Text, IntWritable>{

    private final static IntWritable one = new IntWritable(1);
    private Text word = new Text();

    public void map(Object key, Text value, Context context
                    ) throws IOException, InterruptedException {
        StringTokenizer itr = new StringTokenizer(value.toString());
        while (itr.hasMoreTokens()) {
        word.set(itr.nextToken());
        context.write(word, one);
        }
    }
    }

    public static class IntSumReducer
        extends Reducer<Text,IntWritable,Text,IntWritable> {
    private IntWritable result = new IntWritable();

    public void reduce(Text key, Iterable<IntWritable> values,
                        Context context
                        ) throws IOException, InterruptedException {
        int sum = 0;
        for (IntWritable val : values) {
        sum += val.get();
        }
        result.set(sum);
        context.write(key, result);
    }
    }

    public static void main(String[] args) throws Exception {
    Configuration conf = new Configuration();
    String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
    if (otherArgs.length != 2) {
        System.err.println("Usage: wordcount <in> <out>");
        System.exit(2);
    }
    Job job = new Job(conf, "word count");
    job.setJarByClass(WordCount.class);
    job.setMapperClass(TokenizerMapper.class);
    job.setCombinerClass(IntSumReducer.class);
    job.setReducerClass(IntSumReducer.class);
    job.setOutputKeyClass(Text.class);
    job.setOutputValueClass(IntWritable.class);
    FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
    FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
    System.exit(job.waitForCompletion(true) ? 0 : 1);
    }
}
```

如需撰寫您自己的 MapReduce 應用程式的指示，請參閱 [開發適用于 HDInsight 的 JAVA MapReduce 應用程式](apache-hadoop-develop-deploy-java-mapreduce-linux.md)。

## <a name="run-the-mapreduce"></a>執行 MapReduce

HDInsight 可以使用各種方法執行 HiveQL 工作。 請使用下表決定適合您的方法，然後跟著連結逐項閱讀介紹。

| **使用此**.。。 | **...若要這樣做** |  ...從此 **用戶端作業系統** |
|:--- |:--- |:--- |:--- |
| [SSH](apache-hadoop-use-mapreduce-ssh.md) |透過 **SSH** |Linux、Unix、Mac OS X 或 Windows |
| [Curl](apache-hadoop-use-mapreduce-curl.md) |使用 **REST** |Linux、Unix、Mac OS X 或 Windows |
| [Windows PowerShell](apache-hadoop-use-mapreduce-powershell.md) |使用 **Windows PowerShell**  |Windows |

## <a name="next-steps"></a>後續步驟

若要深入了解如何處理 HDInsight 的資料，請參閱下列文件：

* [開發 HDInsight 的 Java MapReduce 程式](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

* [搭配 HDInsight 使用 Apache Hive](./hdinsight-use-hive.md)
