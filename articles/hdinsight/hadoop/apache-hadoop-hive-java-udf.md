---
title: JAVA 使用者定義函式 (UDF) 與 Apache Hive Azure HDInsight
description: 了解如何建立能配合 Apache Hive 使用的以 Java 為基礎的使用者定義函式 (UDF)。 此範例 UDF 會將文字字串的資料表轉換成小寫。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017, devx-track-java
ms.date: 11/20/2019
ms.openlocfilehash: bf9c2ea544c6b510a0507b6b020f0eae0b101dc7
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946570"
---
# <a name="use-a-java-udf-with-apache-hive-in-hdinsight"></a>在 HDInsight 中搭配使用 Java UDF 和 Apache Hive

了解如何建立能配合 Apache Hive 使用的以 Java 為基礎的使用者定義函式 (UDF)。 此範例中的 Java UDF 會將文字字串的資料表轉換成全部小寫。

## <a name="prerequisites"></a>先決條件

* HDInsight 上的 Hadoop 叢集。 請參閱[開始在 Linux 上使用 HDInsight](./apache-hadoop-linux-tutorial-get-started.md)。
* [JAVA Developer 套件 (JDK) 第8版](/azure/developer/java/fundamentals/java-jdk-long-term-support)
* 根據 Apache 正確[安裝](https://maven.apache.org/install.html)的 [Apache Maven](https://maven.apache.org/download.cgi)。  Maven 是適用於 Java 專案的專案建置系統。
* 您叢集主要儲存體的 [URI 配置](../hdinsight-hadoop-linux-information.md#URI-and-scheme)。 這會 wasb://到適用于 Azure Data Lake Storage Gen1 的 Azure Data Lake Storage Gen2 或 adl://的 Azure 儲存體、abfs://。 如果已對 Azure 儲存體啟用安全傳輸，URI 會是 `wasbs://`。  另請參閱[安全傳輸](../../storage/common/storage-require-secure-transfer.md)。

* 文字編輯器或 Java IDE

    > [!IMPORTANT]  
    > 如果您是在 Windows 用戶端上建立 Python 檔案，就必須使用以 LF 做為行尾結束符號的編輯器。 如果您不確定編輯器是使用 LF 或 CRLF，請參閱[疑難排解](#troubleshooting)一節，以了解有關移除 CR 字元的步驟。

## <a name="test-environment"></a>測試環境

本文所使用的環境是執行 Windows 10 的電腦。  命令會在命令提示字元中執行，並使用「記事本」編輯各種不同的檔案。 據以針對您的環境進行修改。

從命令提示字元中，輸入下列命令來建立工作環境：

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-an-example-java-udf"></a>建立範例 Java UDF

1. 輸入下列命令來建立新的 Maven 專案：

    ```cmd
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    此命令會建立名為的目錄 `exampleudf` ，其中包含 Maven 專案。

2. 一旦建立專案之後，請 `exampleudf/src/test` 輸入下列命令來刪除已建立為專案一部分的目錄：

    ```cmd
    cd ExampleUDF
    rmdir /S /Q "src/test"
    ```

3. `pom.xml`輸入下列命令以開啟：

    ```cmd
    notepad pom.xml
    ```

    然後使用下列 XML 來取代現有的 `<dependencies>` 專案：

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

4. 輸入下列命令以建立並開啟新的檔案 `ExampleUDF.java` ：

    ```cmd
    notepad src/main/java/com/microsoft/examples/ExampleUDF.java
    ```

    然後將下列 java 程式碼複製並貼到新的檔案中。 然後關閉檔案。

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

在下列命令中， `sshuser` 將取代為實際的使用者名稱（如果不同）。 取代 `mycluster` 為實際的叢集名稱。

1. 輸入下列命令來編譯和封裝 UDF：

    ```cmd
    mvn compile package
    ```

    此命令會建置 UDF 並將它封裝到 `exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar` 檔案。

2. 藉 `scp` 由輸入下列命令，使用命令將檔案複製到 HDInsight 叢集：

    ```cmd
    scp ./target/ExampleUDF-1.0-SNAPSHOT.jar sshuser@mycluster-ssh.azurehdinsight.net:
    ```

3. 輸入下列命令以使用 SSH 連接到叢集：

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

4. 從開啟的 SSH 會話，將 jar 檔案複製到 HDInsight 儲存體。

    ```bash
    hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars
    ```

## <a name="use-the-udf-from-hive"></a>從 Hive 使用 UDF

1. 輸入下列命令，從 SSH 會話啟動 Beeline 用戶端：

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

    此查詢會從資料表中選取狀態、將字串轉換為小寫，然後將它們連同未修改的名稱一起顯示。 此輸出看起來類似下列文字：

    ```output
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
    ```

## <a name="troubleshooting"></a>疑難排解

執行 hive 工作時，您可能會遇到類似下列文字的錯誤：

```output
Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.
```

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