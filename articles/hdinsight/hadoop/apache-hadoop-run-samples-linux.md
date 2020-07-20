---
title: 在 HDInsight 上執行 Apache Hadoop MapReduce 範例 - Azure
description: 開始使用 HDInsight 中隨附的 jar 檔案中的 MapReduce 範例。 使用 SSH 連接到叢集，然後使用 Hadoop 命令執行範例工作。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/12/2019
ms.openlocfilehash: cc0918f3d9739a214e682d7faa460b6cc519cb0b
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86207834"
---
# <a name="run-the-mapreduce-examples-included-in-hdinsight"></a>執行包含在 HDInsight 中的 MapReduce 範例

[!INCLUDE [samples-selector](../../../includes/hdinsight-run-samples-selector.md)]

了解如何執行隨附於 HDInsight 上 Apache Hadoop 的 MapReduce 範例。

## <a name="prerequisites"></a>Prerequisites

* HDInsight 上的 Apache Hadoop 叢集。 請參閱[開始在 Linux 上使用 HDInsight](./apache-hadoop-linux-tutorial-get-started.md)。

* SSH 用戶端。 如需詳細資訊，請參閱[使用 SSH 連線至 HDInsight (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md)。

## <a name="the-mapreduce-examples"></a>MapReduce 範例

範例位於 HDInsight 叢集上的 `/usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar`。 這些範例的原始程式碼包含在 HDInsight 叢集上，位於 `/usr/hdp/current/hadoop-client/src/hadoop-mapreduce-project/hadoop-mapreduce-examples`。

下列範例都包含在此封存中：

|範例 |描述 |
|---|---|
|aggregatewordcount|計算輸入檔中的字數。|
|aggregatewordhist|計算輸入檔中單字的長條圖。|
|bbp|會使用貝利-Borwein-普 bailey-borwein-plouffe) 來計算 Pi 的確切位數。|
|dbcount|計算儲存在資料庫中的 pageview 記錄。|
|distbbp|使用 BBP 類型的公式來計算 Pi 的確切位。|
|grep|計算輸入中 RegEx 的相符專案。|
|join|執行已排序且平均分割之資料集的聯結。|
|multifilewc 可|從數個檔案計算字數。|
|pentomino|並排顯示計畫，以尋找 pentomino 問題的解決方案。|
|pi|使用 Monte Carlo 方法來預估 Pi。|
|randomtextwriter|針對每個節點寫入 10 GB 的隨機文字資料。|
|randomwriter|為每個節點寫入 10 GB 的亂數據。|
|secondarysort|定義 [縮減] 階段的次要排序。|
|sort|排序隨機寫入器所寫入的資料。|
|sudoku|數獨解答程式。|
|teragen|產生用於 TeraSort 的資料。|
|terasort|執行 TeraSort。|
|teravalidate|檢查 TeraSort 的結果。|
|wordcount|計算輸入檔中的字數。|
|wordmean|計算輸入檔中字詞的平均長度。|
|wordmedian|計算輸入檔中文字的中位數長度。|
|wordstandarddeviation|計算輸入檔中字詞長度的標準差。|

## <a name="run-the-wordcount-example"></a>執行 wordcount 範例

1. 使用 SSH 連線到 HDInsight。 將取代 `CLUSTER` 為您的叢集名稱，然後輸入下列命令：

    ```cmd
    ssh sshuser@CLUSTER-ssh.azurehdinsight.net
    ```

2. 在 SSH 會話中，使用下列命令來列出範例：

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar
    ```

    此命令會產生本文件上一節中的範例清單。

3. 使用下列命令可取得特定範例的說明。 在此情況下為 **wordcount** 範例：

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount
    ```

    您會看見下列訊息：

    ```output
    Usage: wordcount <in> [<in>...] <out>
    ```

    此訊息表示您可以為來源文件提供數個輸入路徑。 最後一個路徑是輸出 (來源文件中的字數計數) 的儲存處。

4. 使用下列各項來計算達文西 da Vinci 的筆記本中的所有文字，並以您的叢集作為範例資料來提供：

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/davinciwordcount
    ```

    此作業的輸入讀取自 `/example/data/gutenberg/davinci.txt`。 此範例的輸出會儲存在 `/example/data/davinciwordcount`。 這兩個路徑都位於叢集的預設儲存體上，而不是本機檔案系統上。

   > [!NOTE]  
   > 如 wordcount 範例的說明所述，您也可以指定多個輸入檔。 例如， `hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/gutenberg/ulysses.txt /example/data/twowordcount` 會計算 davinci.txt 和 ulysses.txt 中的字數。

5. 工作完成後，使用以下命令來檢視輸出：

    ```bash
    hdfs dfs -cat /example/data/davinciwordcount/*
    ```

    此命令會串連作業所產生的所有輸出檔。 它會在主控台顯示輸出。 輸出大致如下：

    ```output
    zum     1
    zur     1
    zwanzig 1
    zweite  1
    ```

    每一行代表一個字詞，以及該字詞在輸入資料中出現的次數。

## <a name="the-sudoku-example"></a>數獨範例

[數獨](https://en.wikipedia.org/wiki/Sudoku) 是由九個 3x3 的方格所組成的邏輯謎題。 方格中的一些格子含有數字，而有些格子則為空白，因此目標就是解出空白格子的數字。 先前的連結提供謎題的詳細資訊，而此範例的目的是要解出空白格子的數字。 所以我們的輸入應該是具有下列格式的檔案：

* 具有九個資料列的九個資料行
* 每個資料行可以包含一個數字或 `?` (表示空白的格子)
* 格子之間以空格分隔

建立數獨謎題必須採用特定的方法，那就是您不能在某個資料行或資料列中使用重複的數字。 HDInsight 叢集上已經有一個正確建立的範例。 它位於 `/usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta` 且包含下列文字：

```output
8 5 ? 3 9 ? ? ? ?
? ? 2 ? ? ? ? ? ?
? ? 6 ? 1 ? ? ? 2
? ? 4 ? ? 3 ? 5 9
? ? 8 9 ? 1 4 ? ?
3 2 ? 4 ? ? 8 ? ?
9 ? ? ? 8 ? 5 ? ?
? ? ? ? ? ? 2 ? ?
? ? ? ? 4 5 ? 7 8
```

若要在 Sudoku 範例中執行此範例問題，請使用以下命令：

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar sudoku /usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta
```

出現的結果會類似如下文字：

```output
8 5 1 3 9 2 6 4 7
4 3 2 6 7 8 1 9 5
7 9 6 5 1 4 3 8 2
6 1 4 8 2 3 7 5 9
5 7 8 9 6 1 4 2 3
3 2 9 4 5 7 8 1 6
9 4 7 2 8 6 5 3 1
1 8 5 7 3 9 2 6 4
2 6 3 1 4 5 9 7 8
```

## <a name="pi--example"></a>Pi (π) 範例

Pi 範例會使用統計 (擬蒙特卡羅法) 方法來估計 pi 的值。 點隨機放置在單位正方形中。 正方形也包含一個圓形。 點落在圓形中的機率等於圓形的面積，pi/4。 Pi 的值可從 4R 的值來估計。 其中 R 是圓內點數佔正方形內總點數的比例。 使用的樣本點越多，估計越準確。

使用以下命令來執行此範例。 此命令會每次使用 16 個對應搭配 10,000,000 個取樣來估計 Pi 的值：

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar pi 16 10000000
```

此命令傳回的值類似於 **3.14159155000000000000**。 Pi 的前 10 個小數位數是 3.1415926535，供您參考。

## <a name="10-gb-graysort-example"></a>10 GB GraySort 範例

GraySort 是一種效能評定排序。 其計量為排序大量資料時 (通常至少為 100 TB) 所達成的排序速率 (TB/分鐘)。

本範例使用不太大的 10 GB 資料，所以執行起來相對較快。 本範例使用由 Owen O'Malley 和 Arun Murthy 共同開發的 MapReduce 應用程式。 這些應用程式會在2009中贏得年度一般用途 ( "耐力賽" ) tb 的排序基準測試，速率為 0.578 TB/分鐘 (100 TB) 的173分鐘。 如需此和其他排序基準的詳細資訊，請參閱[排序基準](https://sortbenchmark.org/)網站。

本範例使用三組 MapReduce 程式：

* **TeraGen**：MapReduce 程式，產生要排序的資料列

* **TeraSort**可取樣輸入資料並利用 MapReduce 將資料依全序排列

    TeraSort 是標準的 MapReduce 排序，但有一個自訂 Partitioner。 此 Partitioner 使用已排序的 N-1 取樣索引鍵清單，其定義每個歸納的索引鍵範圍。 尤其是，會傳送使得 sample[i-1] <= key < sample[i] 的所有索引鍵給歸納 i。 此 Partitioner 會保證歸納 i 的輸出全都小於歸納 i+1 的輸出。

* **TeraValidate**：可驗證全域排序輸出的 MapReduce 程式

    它會在輸出目錄中為每一個檔案建立一個對應，而每個對應可確保每一個索引鍵一定小於或等於前一個對應。 對應函式會產生每個檔案的第一個和最後一個金鑰的記錄。 歸納函式可確保檔案 i 的第一個金鑰大於檔案 i-1 的最後一個金鑰。 任何問題皆會回報為具備錯誤金鑰歸納階段的輸出。

使用下列步驟來產生資料、排序，並驗證輸出：

1. 產生 10 GB 的資料，這些資料稍後會儲存在 HDInsight 叢集預設儲存體中，位於 `/example/data/10GB-sort-input`：

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapred.map.tasks=50 100000000 /example/data/10GB-sort-input
    ```

    `-Dmapred.map.tasks` 會告訴 Hadoop 在這項工作中要使用多少 map 工作。 最後兩個參數會指示作業建立 10 GB 的資料，並將資料儲存在 `/example/data/10GB-sort-input`。

2. 使用以下命令來排序資料：

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-input /example/data/10GB-sort-output
    ```

    `-Dmapred.reduce.tasks` 會告訴 Hadoop 在這項工作中要使用多少 reduce 工作。 最後兩個參數只是資料的輸入和輸出位置。

3. 使用以下命令驗證依排序產生的資料：

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-output /example/data/10GB-sort-validate
    ```

## <a name="next-steps"></a>後續步驟

您已在本文中學到如何執行以 Linux 為基礎的 HDInsight 叢集所隨附的範例。 如需透過 HDInsight 使用 Pig、Hive 和 MapReduce 的教學課程，請參閱下列主題：

* [在 HDInsight 上將 Apache Hive 與 Apache Hadoop 搭配使用](hdinsight-use-hive.md)
* [搭配 MapReduce 與 HDInsight 上的 Apache Hadoop](hdinsight-use-mapreduce.md)
