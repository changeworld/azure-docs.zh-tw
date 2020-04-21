---
title: Azure Synapse Analytics 中的 Apache Spark - 核心概念
description: 本文將介紹 Azure Synapse Analytics 中的 Apache Spark，以及不同的概念。
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 3cf654e77bf68c5194a0213d4452242b5c44e234
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419532"
---
# <a name="apache-spark-in-azure-synapse-analytics-core-concepts"></a>Azure Synapse Analytics 中的 Apache Spark 核心概念

Apache Spark 是一個平行處理架構，可支援記憶體內部處理，以大幅提升巨量資料分析應用程式的效能。 Azure Synapse Analytics 中的 Apache Spark 是 Microsoft 在雲端中的其中一種 Apache Spark 實作。 

Azure Synapse 可讓您輕鬆地在 Azure 中建立和設定 Spark 功能。 Azure Synapse 會提供此處所述 Spark 功能的不同執行方式。

## <a name="spark-pools-preview"></a>Spark 集區 (預覽)

Spark 集區 (預覽) 會建立在 Azure 入口網站中。 其定義是，當 Spark 集區具現化時，會用來建立可處理資料的 Spark 執行個體。 建立 Spark 集區時，其只會以中繼資料的形式存在；不會耗用或執行任何資源，或向您收取資源費用。 Spark 集區具有一系列控制 Spark 執行個體特性的屬性，這些特性包括但不限於名稱、大小、縮放行為、存留時間。

由於建立 Spark 集區沒有相關聯的費用或資源成本，因此可以使用任意數目的不同設定來建立任何數量的集區。 您也可以對 Spark 集區套用權限，讓使用者只能存取部分集區，而不能存取其他集區。

最佳做法是建立較小的 Spark 集區來進行開發和偵錯，然後建立更大的集區來執行生產工作負載。

您可以在這裡了解如何建立 Spark 集區並查看其所有屬性：[開始使用 Synapse Analytics 中的 Spark 集區](apache-spark-notebook-create-spark-use-sql.md#create-an-apache-spark-pool)

## <a name="spark-instances"></a>Spark 執行個體

當您連線到 Spark 集區、建立工作階段及執行作業時，Spark 執行個體會隨即建立。 因為有多個使用者可存取單一 Spark 集區，所以會為每個連線的使用者建立新的 Spark 執行個體。 

當您提交第二個作業時，如果集區中有容量，現有的 Spark 執行個體也有容量，則現有的執行個體將會處理作業；如果不是如此，而是集區層級上有容量，則會建立新的 Spark 執行個體。

## <a name="examples"></a>範例

### <a name="example-1"></a>範例 1

- 您會建立名為 SP1 的 Spark 集區；其叢集大小固定為 20 個節點。
- 您會提交使用 10 個節點的筆記本作業 J1，此時會建立一個 Spark 執行個體 SI1 來處理作業。
- 您現在會提交另一個使用 10 個節點的作業 J2，因為集區中仍然有容量，而該執行個體 (J2) 會由 SI1 處理。
- 如果 J2 要求 11 個節點，則 SP1 或 SI1 中沒有容量。 在此情況下，如果 J2 來自筆記本，則作業將會遭到拒絕；如果 J2 來自批次作業，則會將其排入佇列。

### <a name="example-2"></a>範例 2

- 您會建立名為 SP2 的 Spark 集區；其具有 10–20 個節點的自動調整功能
- 您會提交使用 10 個節點的筆記本作業 J1，此時會建立一個 Spark 執行個體 SI1 來處理作業。
- 您現在會提交另一個使用 10 個節點的作業 J2，因為集區中仍然有容量，而該執行個體會增加到 20 個節點，並且處理 J2。

### <a name="example-3"></a>範例 3

- 您會建立名為 SP1 的 Spark 集區；其叢集大小固定為 20 個節點。
- 您會提交使用 10 個節點的筆記本作業 J1，此時會建立一個 Spark 執行個體 SI1 來處理作業。
- 另一個使用者 U2 會提交一個使用 10 個節點的作業 J3，此時會建立新的 Spark 執行個體 SI2 來處理作業。
- 您現在會提交另一個使用 10 個節點的作業 J2，因為集區中仍然有容量，而執行個體 J2 會由 SI1 處理。

## <a name="next-steps"></a>後續步驟

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Apache Spark 文件](https://spark.apache.org/docs/2.4.4/)
