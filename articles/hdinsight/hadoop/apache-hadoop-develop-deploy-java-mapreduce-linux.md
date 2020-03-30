---
title: 建立適用於 Apache Hadoop 的 Java MapReduce - Azure HDInsight
description: 了解如何使用 Apache Maven 來建立以 Java 為基礎的 MapReduce 應用程式，然後在 Azure HDInsight 上使用 Hadoop 來加以執行。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 01/16/2020
ms.openlocfilehash: a37a8bb45c11d5b74f3059a153806e3d083cf452
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76311949"
---
# <a name="develop-java-mapreduce-programs-for-apache-hadoop-on-hdinsight"></a>開發適用於 HDInsight 上 Apache Hadoop 的 Java MapReduce 程式

了解如何使用 Apache Maven 來建立以 Java 為基礎的 MapReduce 應用程式，然後在 Azure HDInsight 上使用 Apache Hadoop 來加以執行。

## <a name="prerequisites"></a>Prerequisites

* [JAVA 開發人員工具組 （JDK） 版本 8](https://aka.ms/azure-jdks).

* 根據 Apache 正確[安裝](https://maven.apache.org/install.html)的 [Apache Maven](https://maven.apache.org/download.cgi)。  Maven 是適用於 Java 專案的專案建置系統。

## <a name="configure-development-environment"></a>設定開發環境

本文使用的環境是運行 Windows 10 的電腦。 命令在命令提示符中執行，並且使用記事本編輯各種檔。 根據您的環境進行相應修改。

從命令提示符中，輸入下面的命令以創建工作環境：

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>建立 Maven 專案

1. 輸入以下命令以創建名為**wordcountjava**的 Maven 專案：

   ```bash
   mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

    此命令創建一個目錄，該目錄的名稱由`artifactID`參數指定（本示例中的**字數java）。** 此目錄包含以下項：

    * `pom.xml` - [專案物件模型 (POM)](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html)，包含用來建置專案的資訊和組態詳細資料。
    * src_main_java_org_apache_hadoop_示例：包含應用程式代碼。
    * src_test_java_org_apache_hadoop_示例：包含應用程式的測試。

1. 刪除生成的示例代碼。 刪除生成的測試和應用程式檔`AppTest.java`，並`App.java`輸入以下命令：

    ```cmd
    cd wordcountjava
    DEL src\main\java\org\apache\hadoop\examples\App.java
    DEL src\test\java\org\apache\hadoop\examples\AppTest.java
    ```

## <a name="update-the-project-object-model"></a>更新專案物件模型

如需 pom.xml 檔案的完整參考，請參閱 https://maven.apache.org/pom.html。 通過`pom.xml`輸入以下命令打開：

```cmd
notepad pom.xml
```

### <a name="add-dependencies"></a>新增相依性

在`pom.xml`中，在`<dependencies>`部分中添加以下文本：

```xml
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-mapreduce-examples</artifactId>
    <version>2.7.3</version>
    <scope>provided</scope>
</dependency>
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-mapreduce-client-common</artifactId>
    <version>2.7.3</version>
    <scope>provided</scope>
</dependency>
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-common</artifactId>
    <version>2.7.3</version>
    <scope>provided</scope>
</dependency>
```

這會定義含有特定版本 (列於&lt;version\> 內) 的必要程式庫 (列於 &lt;artifactId\> 內)。 在編譯期間，會從預設的 Maven 儲存機制下載這些相依性。 您可以使用 [Maven 儲存機制搜尋](https://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) (英文) 檢視詳細資訊。

`<scope>provided</scope>` 會告訴 Maven 這些相依性不應該和應用程式一起封裝，因為 HDInsight 叢集會在執行階段提供這些相依性。

> [!IMPORTANT]
> 使用的版本應該符合您叢集上的 Hadoop 版本。 如需有關版本的詳細資訊，請參閱 [HDInsight 元件版本設定](../hdinsight-component-versioning.md)文件。

### <a name="build-configuration"></a>建置組態

Maven 外掛程式可讓您自訂專案的建置階段。 此區段會用來新增外掛程式、資源，和其他組建組態選項。

將以下代碼添加到檔中，`pom.xml`然後保存並關閉該檔。 此文字必須位在檔案中的 `<project>...</project>` 標籤內，例如在 `</dependencies>` 和 `</project>` 之間。

```xml
<build>
    <plugins>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>2.3</version>
        <configuration>
        <transformers>
            <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
            </transformer>
        </transformers>
        </configuration>
        <executions>
        <execution>
            <phase>package</phase>
                <goals>
                <goal>shade</goal>
                </goals>
        </execution>
        </executions>
        </plugin>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.6.1</version>
        <configuration>
        <source>1.8</source>
        <target>1.8</target>
        </configuration>
    </plugin>
    </plugins>
</build>
```

本節配置阿帕奇 Maven 編譯器外掛程式和 Apache Maven 陰影外掛程式。 Compiler 外掛程式用來編譯拓撲。 Shade 外掛程式用來防止以 Maven 所建置的 JAR 封裝發生授權重複。 此外掛程式是用於防止 HDInsight 叢集在執行階段發生「重複的授權檔案」錯誤。 使用 maven-shade-plugin 搭配 `ApacheLicenseResourceTransformer` 實作可防止此錯誤。

maven-shade-plugin 也會產生 uber jar，其中含有應用程式需要的所有相依性。

儲存 `pom.xml` 檔案。

## <a name="create-the-mapreduce-application"></a>建立 MapReduce 應用程式

1. 輸入下面的命令以創建並打開新檔`WordCount.java`。 在提示符處選擇 **"是**"以創建新檔。

    ```cmd
    notepad src\main\java\org\apache\hadoop\examples\WordCount.java
    ```

2. 然後複製下面的 java 代碼並將其粘貼到新檔中。 然後關閉檔。

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

    請注意，封裝名稱是 `org.apache.hadoop.examples`，而類別名稱是 `WordCount`。 提交 MapReduce 作業時會用到這些名稱。

## <a name="build-and-package-the-application"></a>建置和封裝應用程式

從 `wordcountjava` 目錄，使用下列命令來建置含有應用程式的 JAR 檔案：

```cmd
mvn clean package
```

此命令會清除任何先前的組建構件、下載任何尚未安裝的相依性，然後建置並封裝應用程式。

一旦命令完成之後，`wordcountjava/target` 目錄就會包含名為 `wordcountjava-1.0-SNAPSHOT.jar` 的檔案。

> [!NOTE]
> `wordcountjava-1.0-SNAPSHOT.jar` 檔案是一個 uberjar，其中不只含有 WordCount 作業，還有該作業在執行階段所需的相依性。

## <a name="upload-the-jar-and-run-jobs-ssh"></a>上傳 JAR 並執行作業 (SSH)

下列步驟使用 `scp`，將 JAR 複製到 HDInsight 叢集上 Apache HBase 的主要前端節點。 接著，會使用 `ssh` 命令連接到該叢集並直接在前端節點上執行範例。

1. 將罐子上載到群集。 替換為`CLUSTERNAME`HDInsight 群集名稱，然後輸入以下命令：

    ```cmd
    scp target/wordcountjava-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. 連接到群集。 替換為`CLUSTERNAME`HDInsight 群集名稱，然後輸入以下命令：

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 在 SSH 工作階段中，使用以下命令執行 MapReduce 應用程式：

   ```bash
   yarn jar wordcountjava-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /example/data/gutenberg/davinci.txt /example/data/wordcountout
   ```

    此命令會啟動 WordCount MapReduce 應用程式。 輸入檔案為 `/example/data/gutenberg/davinci.txt`，輸出目錄則為 `/example/data/wordcountout`。 輸入檔和輸出都會儲存至叢集的預設儲存體中。

1. 作業完成之後，請使用以下命令來檢視結果：

   ```bash
   hdfs dfs -cat /example/data/wordcountout/*
   ```

    您應該會收到一份單字和計數的清單，其中含有類似下列文字的值：

    ```output
    zeal    1
    zelus   1
    zenith  2
    ```

## <a name="next-steps"></a>後續步驟

在本文件中，您已學到如何開發 Java MapReduce 工作。 請參閱下列文件，了解其他的 HDInsight 使用方式。

* [搭配 HDInsight 使用 Apache Hive](hdinsight-use-hive.md)
* [搭配 HDInsight 使用 MapReduce](hdinsight-use-mapreduce.md)
* [JAVA 開發人員中心](https://azure.microsoft.com/develop/java/)
