---
title: 在 Azure Cosmos DB 中創建綜合分區鍵
description: 瞭解如何在 Azure Cosmos 容器中使用合成分區鍵在分區鍵之間均勻分佈資料和工作負載
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/03/2019
author: markjbrown
ms.author: mjbrown
ms.openlocfilehash: e8786c2d6e93c18a5bf9856a5555d6b528f842c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75441227"
---
# <a name="create-a-synthetic-partition-key"></a>建立綜合分割區索引鍵

最佳做法是具有許多不同值的分區鍵，例如數百或數千。 目標是在與這些分割區索引鍵值相關聯的項目上平均散發您的資料和工作負載。 如果資料中不存在此類屬性，則可以構造*合成分區鍵*。 本文檔介紹了為 Cosmos 容器生成合成分區鍵的幾種基本技術。

## <a name="concatenate-multiple-properties-of-an-item"></a>串連項目的多個屬性

您可以藉由將多個屬性值串連到單一人工 `partitionKey` 屬性來形成分割區索引鍵。 這些索引鍵稱為綜合索引鍵。 例如，請考慮下列範例文件：

```JavaScript
{
"deviceId": "abc-123",
"date": 2018
}
```

針對先前的文章，其中一個選項是將 /deviceId 或 /date 設為分割區索引鍵。 如果要根據裝置識別碼 或日期對容器進行分區，請使用此選項。 另一個選項是將這兩個值串連到一個綜合 `partitionKey` 屬性以用來作為分割區索引鍵。

```JavaScript
{
"deviceId": "abc-123",
"date": 2018,
"partitionKey": "abc-123-2018"
}
```

在即時方案中，資料庫中可以有數千個專案。 不是手動添加合成鍵，而是定義用戶端邏輯以串聯值，並將合成鍵插入到 Cosmos 容器中的項中。

## <a name="use-a-partition-key-with-a-random-suffix"></a>搭配隨機尾碼使用分割區索引鍵

另一個更平均散發工作負載的可行策略是在分割區索引鍵值結尾附加一個隨機數字。 當您以此方式散發項目時，您可以跨分割區執行平行的寫入作業。

其中一個範例是分割區索引鍵代表日期的情況。 您可以選擇介於 1 和 400 之間的亂數，並將其作為尾碼串聯到日期。 此方法通過 生成分區鍵值， `2018-08-09.1`如`2018-08-09.2`、、等 `2018-08-09.400`。 由於您是將分割區索引鍵隨機化，因此每天在容器上的寫入作業都會平均分散在多個分割區中。 此方法會產生更好的平行處理原則及整體更高的輸送量。

## <a name="use-a-partition-key-with-pre-calculated-suffixes"></a>使用具有預計算尾碼的分區鍵 

隨機尾碼策略可以大大提高寫入輸送量，但很難讀取特定項。 您不知道編寫專案時使用的尾碼值。 為了便於閱讀各個專案，請使用預先計算的尾碼策略。 使用基於要查詢的內容計算的數位，而不是使用亂數字在分區之間分配項。

請考慮前面的示例，其中容器使用日期作為分區鍵。 現在假設每個專案都有我們想要訪問的 `Vehicle-Identification-Number`屬性。`VIN` 此外，假設您經常執行查詢以查找除日期之外的`VIN`項。 在您的應用程式將項目寫入到容器之前，它可以根據 VIN 來計算雜湊尾碼，並將它附加至分割區索引鍵日期。 計算可能會生成分佈均勻的 1 和 400 之間的數位。 此結果與隨機尾碼策略方法產生的結果類似。 分割區索引鍵值先前是搭配計算結果串連的日期。

使用這個策略，寫入會在分割區索引鍵值之間以及在分割區之間平均分散。 您可以輕鬆地讀取特定項和日期，因為您可以計算特定`Vehicle-Identification-Number`的分區鍵值。 此方法的好處是，您可以避免創建單個熱分區鍵，即佔用所有工作負載的分區鍵。 

## <a name="next-steps"></a>後續步驟

您可以在下列文章中深入了解分割概念：

* 瞭解有關[邏輯分區](partition-data.md)的更多詳細資訊。
* 深入了解如何[在 Azure Cosmos 容器和資料庫上佈建輸送量](set-throughput.md)。
* 了解如何[在 Azure Cosmos 容器上佈建輸送量](how-to-provision-container-throughput.md)。
* 了解如何[在 Azure Cosmos 資料庫上佈建輸送量](how-to-provision-database-throughput.md)。
