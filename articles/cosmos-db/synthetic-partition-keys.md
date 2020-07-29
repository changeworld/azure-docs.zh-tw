---
title: 在 Azure Cosmos DB 中建立綜合分割區索引鍵
description: 瞭解如何在 Azure Cosmos 容器中使用綜合分割區索引鍵，將資料和工作負載平均分散于分割區索引鍵上
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/03/2019
author: markjbrown
ms.author: mjbrown
ms.openlocfilehash: e8786c2d6e93c18a5bf9856a5555d6b528f842c5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75441227"
---
# <a name="create-a-synthetic-partition-key"></a>建立綜合分割區索引鍵

最佳做法是讓分割區索引鍵具有許多相異值，例如數百個或數千個。 目標是在與這些分割區索引鍵值相關聯的項目上平均散發您的資料和工作負載。 如果您的資料中不存在這類屬性，您可以建立*綜合分割*區索引鍵。 本檔說明為 Cosmos 容器產生綜合分割區索引鍵的幾個基本技術。

## <a name="concatenate-multiple-properties-of-an-item"></a>串連項目的多個屬性

您可以藉由將多個屬性值串連到單一人工 `partitionKey` 屬性來形成分割區索引鍵。 這些索引鍵稱為綜合索引鍵。 例如，請考慮下列範例文件：

```JavaScript
{
"deviceId": "abc-123",
"date": 2018
}
```

針對先前的文章，其中一個選項是將 /deviceId 或 /date 設為分割區索引鍵。 如果您想要根據裝置識別碼或日期分割您的容器，請使用此選項。 另一個選項是將這兩個值串連到一個綜合 `partitionKey` 屬性以用來作為分割區索引鍵。

```JavaScript
{
"deviceId": "abc-123",
"date": 2018,
"partitionKey": "abc-123-2018"
}
```

在即時案例中，您可以在資料庫中有數千個專案。 請定義用戶端邏輯來串連值，並將綜合索引鍵插入 Cosmos 容器中的專案，而不是手動新增綜合索引鍵。

## <a name="use-a-partition-key-with-a-random-suffix"></a>搭配隨機尾碼使用分割區索引鍵

另一個更平均散發工作負載的可行策略是在分割區索引鍵值結尾附加一個隨機數字。 當您以此方式散發項目時，您可以跨分割區執行平行的寫入作業。

其中一個範例是分割區索引鍵代表日期的情況。 您可以選擇介於1到400之間的亂數字，並將其串連為日期的尾碼。 這個方法會產生資料分割索引鍵值，例如  `2018-08-09.1` 、 `2018-08-09.2` 等等  `2018-08-09.400` 。 由於您是將分割區索引鍵隨機化，因此每天在容器上的寫入作業都會平均分散在多個分割區中。 此方法會產生更好的平行處理原則及整體更高的輸送量。

## <a name="use-a-partition-key-with-pre-calculated-suffixes"></a>搭配預先計算的尾碼使用分割區索引鍵 

隨機尾碼策略可以大幅改善寫入輸送量，但難以讀取特定專案。 您不知道撰寫專案時所使用的尾碼值。 為了讓您更輕鬆地讀取個別專案，請使用預先計算的尾碼策略。 請使用根據您要查詢的專案來計算的數位，而不是使用亂數字來散發資料分割之間的專案。

請考慮先前的範例，其中容器使用日期做為資料分割索引鍵。 現在假設每個專案都有  `Vehicle-Identification-Number` `VIN` 我們想要存取的（）屬性。 此外，假設您經常執行查詢，以根據日期來尋找專案 `VIN` 。 在您的應用程式將項目寫入到容器之前，它可以根據 VIN 來計算雜湊尾碼，並將它附加至分割區索引鍵日期。 計算可能會產生1到400之間平均分佈的數位。 此結果類似于隨機尾碼策略方法所產生的結果。 分割區索引鍵值先前是搭配計算結果串連的日期。

使用這個策略，寫入會在分割區索引鍵值之間以及在分割區之間平均分散。 您可以輕鬆地讀取特定專案和日期，因為您可以計算特定的分割區索引鍵值 `Vehicle-Identification-Number` 。 這個方法的優點是，您可以避免建立單一的熱分割區索引鍵，也就是採用所有工作負載的分割區索引鍵。 

## <a name="next-steps"></a>後續步驟

您可以在下列文章中深入了解分割概念：

* 深入了解[邏輯分割區](partition-data.md)。
* 深入了解如何[在 Azure Cosmos 容器和資料庫上佈建輸送量](set-throughput.md)。
* 了解如何[在 Azure Cosmos 容器上佈建輸送量](how-to-provision-container-throughput.md)。
* 了解如何[在 Azure Cosmos 資料庫上佈建輸送量](how-to-provision-database-throughput.md)。
