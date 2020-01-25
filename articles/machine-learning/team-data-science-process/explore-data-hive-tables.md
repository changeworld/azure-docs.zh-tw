---
title: 使用 Hive 查詢來探索 Hive 資料表中的資料 - Team Data Science Process
description: 使用範例 Hive 指令碼，可用來探索 HDInsight Hadoop 叢集之 Hive 資料表中的資料。
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 75dce2b5a83d13fe4a7d166595456e9a8d6324ba
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722164"
---
# <a name="explore-data-in-hive-tables-with-hive-queries"></a>使用 Hive 查詢來瀏覽 Hive 資料表的資料

本文提供 Hive 指令碼範例，可用來瀏覽 HDInsight Hadoop 叢集的 Hive 資料表中的資料。

此工作是 [Team Data Science Process](overview.md) 中的一個步驟。

## <a name="prerequisites"></a>必要條件
本文假設您已經：

* 建立 Azure 儲存體帳戶。 如需指示，請參閱[建立 Azure 儲存體帳戶](../../storage/common/storage-account-create.md)
* 佈建含有 HDInsight 服務的自訂 Hadoop 叢集。 如需指示，請參閱 [自訂適用於進階分析的 Azure HDInsight Hadoop 叢集](customize-hadoop-cluster.md)。
* 已將資料上傳至 Azure HDInsight Hadoop 叢集中的 Hive 資料表。 如果沒有，請遵循 [建立資料並載入 Hive 資料表](move-hive-tables.md) 中的指示，先將資料上傳至 Hive 資料表。
* 啟用叢集的遠端存取。 如需指示，請參閱 [存取 Hadoop 叢集的前端節點](customize-hadoop-cluster.md)。
* 如果您需要如何提交 Hive 查詢的指示，請參閱 [如何提交 Hive 查詢](move-hive-tables.md#submit)

## <a name="example-hive-query-scripts-for-data-exploration"></a>資料探索的 Hive 查詢指令碼範例
1. 取得每個分割區的觀察計數 `SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`
2. 取得每天的觀察計數 `SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);`
3. 取得類別資料行中的層級  
    `SELECT  distinct <column_name> from <databasename>.<tablename>`
4. 取得兩個類別資料行組合中的層級數目 `SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`
5. 取得數值資料行的分佈  
    `SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`
6. 聯結兩個資料表來擷取記錄
   
        SELECT
            a.<common_columnname1> as <new_name1>,
            a.<common_columnname2> as <new_name2>,
            a.<a_column_name1> as <new_name3>,
            a.<a_column_name2> as <new_name4>,
            b.<b_column_name1> as <new_name5>,
            b.<b_column_name2> as <new_name6>
        FROM
            (
            SELECT <common_columnname1>,
                <common_columnname2>,
                <a_column_name1>,
                <a_column_name2>,
            FROM <databasename>.<tablename1>
            ) a
            join
            (
            SELECT <common_columnname1>,
                <common_columnname2>,
                <b_column_name1>,
                <b_column_name2>,
            FROM <databasename>.<tablename2>
            ) b
            ON a.<common_columnname1>=b.<common_columnname1> and a.<common_columnname2>=b.<common_columnname2>

## <a name="additional-query-scripts-for-taxi-trip-data-scenarios"></a>計程車路線資料案例的其他查詢指令碼
[GitHub 存放庫](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts)中也會提供 [NYC 計程車車程資料](https://chriswhong.com/open-data/foil_nyc_taxi/)案例的專屬查詢範例。 這些查詢已經具備指定的資料結構描述，且準備好進行提交來執行。

