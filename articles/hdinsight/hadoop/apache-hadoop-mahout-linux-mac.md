---
title: 在 Azure HDInsight 中使用 Apache Mahout 產生建議
description: 了解如何搭配 HDInsight (Hadoop) 使用 Apache Mahout 機器學習庫來產生電影推薦。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/03/2020
ms.openlocfilehash: 33110e9f1d45fcd11e5f4cad1b589ab929a9472d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75767631"
---
# <a name="generate-movie-recommendations-using-apache-mahout-with-apache-hadoop-in-hdinsight-ssh"></a>在 HDInsight 中使用 Apache Mahout 搭配 Apache Hadoop 來產生電影推薦（SSH）

[!INCLUDE [mahout-selector](../../../includes/hdinsight-selector-mahout.md)]

了解如何使用搭配 Azure HDInsight 的 [Apache Mahout](https://mahout.apache.org) 機器學習庫產生電影推薦。

Mahout 是 Apache Hadoop 的[機器學習服務](https://en.wikipedia.org/wiki/Machine_learning)程式庫。 Mahout 包含可處理資料的演算法，例如篩選、分類和叢集化。 在本文中，您會使用推薦引擎，以根據朋友看過的電影來產生電影推薦。

## <a name="prerequisites"></a>先決條件

HDInsight 上的 Apache Hadoop 叢集。 請參閱[開始在 Linux 上使用 HDInsight](./apache-hadoop-linux-tutorial-get-started.md)。

## <a name="apache-mahout-versioning"></a>Apache Mahout 版本控制

如需 HDInsight 中 Mahout 版本的詳細資訊，請參閱 [HDInsight 版本和 Apache Hadoop 元件](../hdinsight-component-versioning.md)。

## <a name="understanding-recommendations"></a>了解推薦

Mahout 提供的其中一項功能是推薦引擎。 這個引擎接受 `userID`、`itemId` 和 `prefValue` (項目的喜好設定) 格式的資料。 Mahout 接著可以執行共生分析來判斷：「偏好某項目的使用者同時也偏好其他這些項目」**。 接著 Mahout 會以偏好的類似項目判斷使用者，並以此做出推薦。

以下工作流程是一個使用電影資料的簡化範例：

* **共同出現**： Joe、Alice 和 Bob 全都喜歡*星形星際大戰*，帝國會*傳回* *Jedi*。 Mahout 將判斷喜歡上述任何一部電影的使用者，也會喜歡另外兩部電影。

* **共生**：Bob 和 Alice 同時也喜歡《威脅潛伏》**、《複製人全面進攻》** 和《西斯大帝的復仇》**。 Mahout 將判斷喜歡前三部電影的使用者，也會喜歡這三部電影。

* **相似性建議**：因為 joe 喜歡前三部電影，Mahout 會查看其他具有類似喜好設定的電影，但 Joe 未監看（贊/評等）。 在此情況下，Mahout 將會推薦 *《威脅潛伏》*、*《複製人全面進攻》* 和 *《西斯大帝的復仇》*。

### <a name="understanding-the-data"></a>了解資料

[GroupLens 研究](https://grouplens.org/datasets/movielens/)提供 Mahout 相容格式的電影評價資料，相當方便。 您可在位於 `/HdiSamples/HdiSamples/MahoutMovieData`的叢集預設儲存體取得這份資料。

有兩個檔案 `moviedb.txt` 和 `user-ratings.txt`。 `user-ratings.txt` 檔案是用於分析期間。 檢視結果時，`moviedb.txt` 用來提供使用者易記的文字資訊。

`user-ratings.txt`中包含的資料具有`userID`、 `movieID`、 `userRating`和`timestamp`的結構，表示每位使用者對電影評分的程度。 以下是資料範例：

    196    242    3    881250949
    186    302    3    891717742
    22     377    1    878887116
    244    51     2    880606923
    166    346    1    886397596

## <a name="run-the-analysis"></a>執行分析

1. 使用[ssh 命令](../hdinsight-hadoop-linux-use-ssh-unix.md)連接到您的叢集。 以您叢集的名稱取代 CLUSTERNAME，然後輸入命令，以編輯下面的命令：

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 使用下列命令來執行推薦工作：

    ```bash
    mahout recommenditembased -s SIMILARITY_COOCCURRENCE -i /HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt -o /example/data/mahoutout --tempDir /temp/mahouttemp
    ```

> [!NOTE]  
> 此工作可能需要幾分鐘的時間才能完成，並可能執行多項 MapReduce 工作。

## <a name="view-the-output"></a>檢視輸出

1. 工作完成後，使用以下命令來檢視所產生的輸出：

    ```bash
    hdfs dfs -text /example/data/mahoutout/part-r-00000
    ```

    輸出看起來會像下面這樣：

    ```output
    1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
    2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
    3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
    4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]
    ```

    第一欄是 `userID`。 '[' 和 ']' 中包含的值是 `movieId`:`recommendationScore`。

2. 您可以使用輸出和 moviedb.txt，來顯示更多建議的相關資訊。 首先，使用下列命令將檔案複製到本機上︰

    ```bash
    hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt
    hdfs dfs -get /HdiSamples/HdiSamples/MahoutMovieData/* .
    ```

    這個命令會將輸出資料和影片資料檔一起複製到目前目錄中名為 **recommendations.txt** 的檔案中。

3. 使用下列命令來建立 Python 指令碼，該指令碼會在建議輸出資料中查閱影片名稱：

    ```bash
    nano show_recommendations.py
    ```

    開啟編輯器時，請使用下列文字做為檔案的內容：

   ```python
   #!/usr/bin/env python

   import sys

   if len(sys.argv) != 5:
        print "Arguments: userId userDataFilename movieFilename recommendationFilename"
        sys.exit(1)

   userId, userDataFilename, movieFilename, recommendationFilename = sys.argv[1:]

   print "Reading Movies Descriptions"
   movieFile = open(movieFilename)
   movieById = {}
   for line in movieFile:
       tokens = line.split("|")
       movieById[tokens[0]] = tokens[1:]
   movieFile.close()

   print "Reading Rated Movies"
   userDataFile = open(userDataFilename)
   ratedMovieIds = []
   for line in userDataFile:
       tokens = line.split("\t")
       if tokens[0] == userId:
           ratedMovieIds.append((tokens[1],tokens[2]))
   userDataFile.close()

   print "Reading Recommendations"
   recommendationFile = open(recommendationFilename)
   recommendations = []
   for line in recommendationFile:
       tokens = line.split("\t")
       if tokens[0] == userId:
           movieIdAndScores = tokens[1].strip("[]\n").split(",")
           recommendations = [ movieIdAndScore.split(":") for movieIdAndScore in movieIdAndScores ]
           break
   recommendationFile.close()

   print "Rated Movies"
   print "------------------------"
   for movieId, rating in ratedMovieIds:
       print "%s, rating=%s" % (movieById[movieId][0], rating)
   print "------------------------"

   print "Recommended Movies"
   print "------------------------"
   for movieId, score in recommendations:
       print "%s, score=%s" % (movieById[movieId][0], score)
   print "------------------------"
   ```

    按下 **CTRL-X**、**Y**，最後再按 **Enter** 鍵儲存資料。

4. 執行 Python 指令碼。 以下命令假設您已在所有檔案的下載目錄中︰

    ```bash
    python show_recommendations.py 4 user-ratings.txt moviedb.txt recommendations.txt
    ```

    此命令會查看為使用者 ID 4 所產生的建議。

   * **user-ratings.txt** 檔案可用來擷取已評分的影片。

   * **和 moviedb.txt**是用來取出電影的名稱。

   * **建議使用 .txt**來抓取此使用者的電影建議。

     此命令的輸出類似下列文字︰

        ```output
        Seven Years in Tibet (1997), score=5.0
        Indiana Jones and the Last Crusade (1989), score=5.0
        Jaws (1975), score=5.0
        Sense and Sensibility (1995), score=5.0
        Independence Day (ID4) (1996), score=5.0
        My Best Friend's Wedding (1997), score=5.0
        Jerry Maguire (1996), score=5.0
        Scream 2 (1997), score=5.0
        Time to Kill, A (1996), score=5.0
        ```

## <a name="delete-temporary-data"></a>刪除暫存資料

Mahout 作業不會移除處理作業時所建立的暫存資料。 範例工作中指定 `--tempDir` 參數將暫存檔隔離到特定路徑中以方便刪除。 若要移除暫存檔案，請使用下列命令：

```bash
hdfs dfs -rm -f -r /temp/mahouttemp
```

> [!WARNING]  
> 如果您要再次執行該命令，您必須也刪除輸出目錄。 使用以下命令刪除此目錄：
>
> `hdfs dfs -rm -f -r /example/data/mahoutout`

## <a name="next-steps"></a>後續步驟

既然您已瞭解如何使用 Mahout，請探索在 HDInsight 上使用資料的其他方式：

* [搭配 HDInsight 使用 Apache Hive](hdinsight-use-hive.md)
* [搭配 HDInsight 使用 MapReduce](hdinsight-use-mapreduce.md)
