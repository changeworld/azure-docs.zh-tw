---
title: 使用 Apache 蜂巢 Azure HDInsight 的 JAVA 使用者定義函數 （UDF）
description: 了解如何建立能配合 Apache Hive 使用的以 Java 為基礎的使用者定義函式 (UDF)。 此範例 UDF 會將文字字串的資料表轉換成小寫。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/20/2019
ms.openlocfilehash: 73a2a612a4eeb4a59f12abf0660fffb092f0547f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74327200"
---
# <a name="use-a-java-udf-with-apache-hive-in-hdinsight"></a>在 HDInsight 中搭配使用 Java UDF 和 Apache Hive

了解如何建立能配合 Apache Hive 使用的以 Java 為基礎的使用者定義函式 (UDF)。 此範例中的 Java UDF 會將文字字串的資料表轉換成全部小寫。

## <a name="prerequisites"></a>Prerequisites

* HDInsight 上的 Hadoop 群集。 請參閱[在 Linux 上開始使用 HDInsight。](./apache-hadoop-linux-tutorial-get-started.md)
* [Java Developer Kit (JDK) 第 8 版](https://aka.ms/azure-jdks)
* 根據 Apache 正確[安裝](https://maven.apache.org/install.html)的 [Apache Maven](https://maven.apache.org/download.cgi)。  Maven 是適用於 Java 專案的專案建置系統。
* 您叢集主要儲存體的 [URI 配置](../hdinsight-hadoop-linux-information.md#URI-and-scheme)。 這將wasb://用於 Azure 存儲、azure 資料存儲第 2 代存儲abfs://或 Azure 資料存儲第 1 代adl://。 如果已對 Azure 儲存體啟用安全傳輸，URI 會是 `wasbs://`。  另請參閱[安全傳輸](../../storage/common/storage-require-secure-transfer.md)。

* 文字編輯器或 Java IDE

    > [!IMPORTANT]  
    > 如果您是在 Windows 用戶端上建立 Python 檔案，就必須使用以 LF 做為行尾結束符號的編輯器。 如果您不確定編輯器是使用 LF 或 CRLF，請參閱[疑難排解](#troubleshooting)一節，以了解有關移除 CR 字元的步驟。

## <a name="test-environment"></a>測試環境

本文使用的環境是運行 Windows 10 的電腦。  命令在命令提示符中執行，並且使用記事本編輯各種檔。 根據您的環境進行相應修改。

從命令提示符中，輸入下面的命令以創建工作環境：

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-an-example-java-udf"></a>建立範例 Java UDF

1. 通過輸入以下命令創建新的 Maven 專案：

    ```cmd
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    此命令創建一個名為`exampleudf`的目錄，其中包含 Maven 專案。

2. 創建專案後，通過輸入以下命令刪除`exampleudf/src/test`作為專案的一部分創建的目錄：

    ```cmd
    cd ExampleUDF
    rmdir /S /Q "src/test"
    ```

3. 通過`pom.xml`輸入以下命令打開：

    ```cmd
    notepad pom.xml
    ```

    然後用以下`<dependencies>`XML 替換現有條目：

    ```xml
    <dependencies>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-client</artifactId>
            <version>2.7.3</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.apache.hive</groupId>
            <artifactId>hive-exec</artifactId>
            <version>1.2.1</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>
    ```

    這些項目能指定 HDInsight 3.6 隨附之 Hadoop 和 Hive 的版本。 在 [HDInsight 元件版本設定](../hdinsight-component-versioning.md) 文件中，您可以找到有關 HDInsight 隨附之 Hadoop 和 Hive 的版本資訊。

    在檔案結尾處 `</project>` 之前新增 `<build>` 區段。 此區段應該包含下列 XML：

    ```xml
    <build>
        <plugins>
            <!-- build for Java 1.8. This is required by HDInsight 3.6  -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.3</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
            <!-- build an uber jar -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>3.2.1</version>
                <configuration>
                    <!-- Keep us from getting a can't overwrite file error -->
                    <transformers>
                        <transformer
                                implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                        </transformer>
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer">
                        </transformer>
                    </transformers>
                    <!-- Keep us from getting a bad signature error -->
                    <filters>
                        <filter>
                            <artifact>*:*</artifact>
                            <excludes>
                                <exclude>META-INF/*.SF</exclude>
                                <exclude>META-INF/*.DSA</exclude>
                                <exclude>META-INF/*.RSA</exclude>
                            </excludes>
                        </filter>
                    </filters>
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
        </plugins>
    </build>
    ```

    這些項目會定義如何建置專案。 具體來說，定義專案使用的 Java 版本以及如何建置 uberjar 以部署至叢集。

    完成變更後，儲存檔案。

4. 輸入下面的命令以創建和打開新檔`ExampleUDF.java`：

    ```cmd
    notepad src/main/java/com/microsoft/examples/ExampleUDF.java
    ```

    然後複製下面的 java 代碼並將其粘貼到新檔中。 然後關閉檔。

    ```java
    package com.microsoft.examples;

    import org.apache.hadoop.hive.ql.exec.Description;
    import org.apache.hadoop.hive.ql.exec.UDF;
    import org.apache.hadoop.io.*;

    // Description of the UDF
    @Description(
        name="ExampleUDF",
        value="returns a lower case version of the input string.",
        extended="select ExampleUDF(deviceplatform) from hivesampletable limit 10;"
    )
    public class ExampleUDF extends UDF {
        // Accept a string input
        public String evaluate(String input) {
            // If the value is null, return a null
            if(input == null)
                return null;
            // Lowercase the input string and return it
            return input.toLowerCase();
        }
    }
    ```

    此程式碼會實作接受字串值並傳回小寫版本之字串的 UDF。

## <a name="build-and-install-the-udf"></a>建置及安裝 UDF

在下面的命令中，如果`sshuser`不同，請替換為實際使用者名。 替換為`mycluster`實際的群集名稱。

1. 通過輸入以下命令編譯和打包 UDF：

    ```cmd
    mvn compile package
    ```

    此命令會建置 UDF 並將它封裝到 `exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar` 檔案。

2. 使用`scp`命令通過輸入以下命令將檔案複製到 HDInsight 群集：

    ```cmd
    scp ./target/ExampleUDF-1.0-SNAPSHOT.jar sshuser@mycluster-ssh.azurehdinsight.net:
    ```

3. 通過輸入以下命令，使用 SSH 連接到群集：

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

4. 從打開的 SSH 會話中，將 jar 檔案複製到 HDInsight 存儲。

    ```bash
    hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars
    ```

## <a name="use-the-udf-from-hive"></a>從 Hive 使用 UDF

1. 通過輸入以下命令從 SSH 會話啟動 Beeline 用戶端：

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

    此命令假設您使用預設值 **admin** 來當做叢集的登入帳戶。

2. 一旦到達 `jdbc:hive2://localhost:10001/>` 提示後，請輸入下列命令以將 UDF 新增至 Hive，並將它公開為函式。

    ```hiveql
    ADD JAR wasbs:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
    CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';
    ```

3. 使用 UDF 將從資料表擷取的值轉換成小寫字串。

    ```hiveql
    SELECT tolower(state) AS ExampleUDF, state FROM hivesampletable LIMIT 10;
    ```

    此查詢從表中選擇狀態，將字串轉換為小寫，然後將其與未修改的名稱一起顯示。 此輸出看起來類似下列文字：

        +---------------+---------------+--+
        |  exampleudf   |     state     |
        +---------------+---------------+--+
        | california    | California    |
        | pennsylvania  | Pennsylvania  |
        | pennsylvania  | Pennsylvania  |
        | pennsylvania  | Pennsylvania  |
        | colorado      | Colorado      |
        | colorado      | Colorado      |
        | colorado      | Colorado      |
        | utah          | Utah          |
        | utah          | Utah          |
        | colorado      | Colorado      |
        +---------------+---------------+--+

## <a name="troubleshooting"></a>疑難排解

回合組態單元作業時，可能會遇到類似于以下文本的錯誤：

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.

這個問題可能是由 Python 檔案中的行尾結束符號所引起。 許多 Windows 編輯器預設都是使用 CRLF 做為行尾結束符號，但是 Linux 應用程式通常預期使用 LF。

若要在檔案上傳至 HDInsight 之前移除 CR 字元，您可以使用下列 PowerShell 陳述式︰

```PowerShell
# Set $original_file to the python file path
$text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
[IO.File]::WriteAllText($original_file, $text)
```

## <a name="next-steps"></a>後續步驟

如需 Hive 的其他操作方法，請參閱 [搭配 HDInsight 使用 Apache Hive](hdinsight-use-hive.md)。

如需 Hive 使用者定義函式的詳細資訊，請造訪 apache.org 並參閱 Hive wiki 的 [Apache Hive 運算子和使用者定義函式](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF)一節。
