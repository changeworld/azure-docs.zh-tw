---
title: 使用 Apache Hive Azure HDInsight 分析 & 進程 JSON
description: 瞭解如何使用 JSON 檔，並使用 Azure HDInsight 中的 Apache Hive 來分析它們。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 9a7d3992ecd2c74947eaa1071b97b2032000c749
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92547600"
---
# <a name="process-and-analyze-json-documents-by-using-apache-hive-in-azure-hdinsight"></a>使用 Azure HDInsight 中的 Apache Hive 處理並分析 JSON 文件

了解如何使用 Azure HDInsight 中的 Apache Hive 中的處理和分析 JavaScript Object Notation (JSON) 檔案。 本文使用下列 JSON 檔：

```json
{
  "StudentId": "trgfg-5454-fdfdg-4346",
  "Grade": 7,
  "StudentDetails": [
    {
      "FirstName": "Peggy",
      "LastName": "Williams",
      "YearJoined": 2012
    }
  ],
  "StudentClassCollection": [
    {
      "ClassId": "89084343",
      "ClassParticipation": "Satisfied",
      "ClassParticipationRank": "High",
      "Score": 93,
      "PerformedActivity": false
    },
    {
      "ClassId": "78547522",
      "ClassParticipation": "NotSatisfied",
      "ClassParticipationRank": "None",
      "Score": 74,
      "PerformedActivity": false
    },
    {
      "ClassId": "78675563",
      "ClassParticipation": "Satisfied",
      "ClassParticipationRank": "Low",
      "Score": 83,
      "PerformedActivity": true
    }
  ]
}
```

檔案位於 `wasb://processjson@hditutorialdata.blob.core.windows.net/`。 如需關於搭配 HDInsight 使用 Azure Blob 儲存體的詳細資訊，請參閱[在 HDInsight 中使用 HDFS 相容的 Azure Blob 儲存體搭配 Apache Hadoop](../hdinsight-hadoop-use-blob-storage.md)。 您可以將檔案複製到叢集的預設容器。

在本文中，您會使用 Apache Hive 主控台。 如需如何開啟 Hive 主控台的指示，請參閱 [在 HDInsight 中搭配 Apache Hadoop 使用 Apache Ambari Hive View](apache-hadoop-use-hive-ambari-view.md)。

> [!NOTE]  
> HDInsight 4.0 中已不再提供 Hive 檢視。

## <a name="flatten-json-documents"></a>簡維 JSON 文件

下一節所列的方法需要 JSON 檔由單一資料列組成。 因此，您必須將 JSON 文件壓平合併成一個字串。 如果已壓平合併 JSON 文件，您就可以略過此步驟，直接進入與分析 JSON 資料相關的下一節。 若要壓平合併 JSON 文件，執行下列指令碼：

```sql
DROP TABLE IF EXISTS StudentsRaw;
CREATE EXTERNAL TABLE StudentsRaw (textcol string) STORED AS TEXTFILE LOCATION "wasb://processjson@hditutorialdata.blob.core.windows.net/";

DROP TABLE IF EXISTS StudentsOneLine;
CREATE EXTERNAL TABLE StudentsOneLine
(
  json_body string
)
STORED AS TEXTFILE LOCATION '/json/students';

INSERT OVERWRITE TABLE StudentsOneLine
SELECT CONCAT_WS(' ',COLLECT_LIST(textcol)) AS singlelineJSON
      FROM (SELECT INPUT__FILE__NAME,BLOCK__OFFSET__INSIDE__FILE, textcol FROM StudentsRaw DISTRIBUTE BY INPUT__FILE__NAME SORT BY BLOCK__OFFSET__INSIDE__FILE) x
      GROUP BY INPUT__FILE__NAME;

SELECT * FROM StudentsOneLine
```

原始 JSON 檔案位於 `wasb://processjson@hditutorialdata.blob.core.windows.net/`。 **StudentsRaw** Hive 資料表會指向未壓平合併的原始 JSON 檔。

StudentsOneLine  Hive 資料表會將資料儲存在 HDInsight 預設檔案系統的 /json/students/  路徑下。

**INSERT** 陳述式會將壓平合併的 JSON 資料填入 **StudentOneLine** 資料表。

**SELECT** 陳述式應該只會傳回一個資料列。

以下是 **SELECT** 陳述式的輸出：

![HDInsight 壓平合併 JSON 檔](./media/using-json-in-hive/hdinsight-flatten-json.png)

## <a name="analyze-json-documents-in-hive"></a>在 Hive 中分析 JSON 文件

Hive 提供三種不同的機制，可在 JSON 文件上執行查詢。您也可以自行撰寫：

* 使用 get_json_object 使用者定義函式 (UDF)。
* 使用 json_tuple UDF。
* 使用自訂序列化/還原序列化程式 (SerDe)。
* 使用 Python 或其他語言撰寫您自己的 UDF。 如需有關搭配 Hive 執行您自己的 Python 程式碼的詳細資訊，請參閱[搭配 Apache Hive 和 Apache Pig 的 Python UDF](./python-udf-hdinsight.md)。

### <a name="use-the-get_json_object-udf"></a>使用 get_json_object UDF

Hive 提供一個稱為 [get_json_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) 的內建 UDF，可在執行時間查詢 json。 這個方法會採用兩個引數：資料表名稱和方法名稱。 方法名稱具有壓平合併的 JSON 檔和需要剖析的 JSON 欄位。 讓我們看看一個範例，看看這個 UDF 的運作方式。

下列查詢會傳回每個學生的姓氏與名字：

```sql
SELECT
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
FROM StudentsOneLine;
```

以下是在主控台視窗中執行此查詢時的輸出：

![Apache Hive 取得 json 物件 UDF](./media/using-json-in-hive/hdinsight-get-json-object.png)

get-json_object UDF 有幾項限制：

* 因為查詢中的每個欄位都需要重新剖析查詢，所以它會影響效能。
* **GET\_JSON_OBJECT()** 會傳回陣列的字串表示法。 若要將此陣列轉換成 Hive 陣列，您必須使用規則運算式來取代方括號「[」和「]」，此外您也要呼叫分割以取得陣列。

這項轉換是 Hive wiki 建議您使用 **json_tuple** 的原因。  

### <a name="use-the-json_tuple-udf"></a>使用 json_tuple UDF

Hive 所提供的另一個 UDF 稱為 [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple)，其效能優於 [get_ 的 json _object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object)。 這個方法會採用一組索引鍵和 JSON 字串。 然後傳回值的元組。 下列查詢會從 JSON 文件傳回學生識別碼以及年級：

```sql
SELECT q1.StudentId, q1.Grade
FROM StudentsOneLine jt
LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
  AS StudentId, Grade;
```

這個指令碼在 Hive 主控台的輸出：

![Apache Hive json 查詢結果](./media/using-json-in-hive/hdinsight-json-tuple.png)

`json_tuple`UDF 會使用 Hive 中的[橫向視圖](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView)語法，可讓 json \_ 元組將 UDT 函式套用到原始資料表的每個資料列，以建立虛擬資料表。 複雜 JSON 會重複使用 **橫向檢視** ，因此變得難以使用。 此外， **JSON_TUPLE** 無法處理嵌套 json。

### <a name="use-a-custom-serde"></a>使用自訂 SerDe

SerDe 是剖析巢狀 JSON 文件的最佳選擇。 它可讓您定義的 JSON 結構描述，然後您可以使用結構來剖析文件。 如需指示，請參閱[如何搭配 Microsoft Azure HDInsight 來使用自訂 JSON SerDe](https://web.archive.org/web/20190217104719/https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/)。

## <a name="summary"></a>總結

Hive 中您所選擇的 JSON 運算子類型取決於您的案例。 使用簡單的 JSON 檔和一個要查閱的欄位，選擇 Hive UDF **get_json_object** 。 如果您有多個要查閱的索引鍵，則可以使用 **json_tuple** 。 針對嵌套檔，請使用 **JSON SerDe** 。

## <a name="next-steps"></a>下一步

如需其他相關文章，請參閱：

* [在 HDInsight 中使用 Apache Hive 和 HiveQL 搭配 Apache Hadoop 來分析範例 Apache log4j 檔案](./hdinsight-use-hive.md)
* [使用 HDInsight 中的 Interactive Query 分析航班延誤資料](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)