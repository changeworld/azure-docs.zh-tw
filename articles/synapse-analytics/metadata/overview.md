---
title: 共用中繼資料模型
description: Azure Synapse Analytics 可讓不同的工作區計算引擎在其無伺服器 Apache Spark 集區 (預覽) 、無伺服器 SQL 集區 (預覽) 和專用 SQL 集區之間共用資料庫和資料表。
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.openlocfilehash: 64c19f3331be8ffda433207da88ebf22c546ee4e
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93324665"
---
# <a name="azure-synapse-analytics-shared-metadata"></a>Azure Synapse Analytics 共用中繼資料

Azure Synapse Analytics 可讓不同的工作區計算引擎在其無伺服器 Apache Spark 集區 (預覽) 和無伺服器 SQL 集區 (預覽) 之間共用資料庫和資料表。

[!INCLUDE [preview](../includes/note-preview.md)]

共用支援所謂的新式資料倉儲模式，並可讓工作區 SQL 引擎存取以 Spark 建立的資料庫和資料表。 也可讓 SQL 引擎建立自己的物件，而不會與其他引擎共用。

## <a name="support-the-modern-data-warehouse"></a>支援新式資料倉儲

共用中繼資料資料模型會以下列方式支援新式資料倉儲模式：

1. 資料湖中的資料會透過 Spark 來有效率地準備及結構化，其方法是將準備的資料儲存在可能位於數個資料庫中的 Parquet 備份資料表中 (可能已分割)。

2. Spark 建立的資料庫及其所有資料表都會顯示在任何 Azure Synapse 工作區 Spark 集區執行個體中，而且可以從任何 Spark 作業中使用。 這項功能會受限於[權限](#security-model-at-a-glance)，因為工作區中所有 Spark 集區都會共用相同的基礎目錄中繼存放區。

3. Spark 建立的資料庫及其受 Parquet 支援的資料表會在工作區無伺服器 SQL 集區引擎中顯示。 [資料庫](database.md)會在無伺服器 SQL 集區中繼資料中自動建立，而由 Spark 作業建立的[外部和受控資料表](table.md)，則會在對應資料庫的 `dbo` 結構描述中，以外部資料表的形式存在無伺服器 SQL 集區中繼資料中來供以存取。 

<!--[INSERT PICTURE]-->

<!--__Figure 1 -__ Supporting the Modern Data Warehouse Pattern with shared metadata-->

物件同步不會即時進行。 物件會稍微延遲幾秒鐘，才會出現在 SQL 內容中。 物件出現之後，即可進行查詢，但無法由有存取權的 SQL 引擎進行更新或變更。

## <a name="shared-metadata-objects"></a>共用的中繼資料物件

Spark 可讓您建立資料庫、外部資料表、受控資料表和檢視。 由於 Spark 檢視需要 Spark 引擎來處理 Spark SQL 陳述式的定義，無法由 SQL 引擎處理，因此只有資料庫及使用 Parquet 儲存格式的內含外部資料表和受控資料表會與工作區 SQL 引擎共用。 Spark 檢視只會在 Spark 集區執行個體之間共用。

## <a name="security-model-at-a-glance"></a>安全性模型一覽

Spark 資料庫和資料表，以及其在 SQL 引擎中的同步代表項目，都會在基礎儲存層級受到保護。 當查詢提交者有權使用的任何引擎查詢資料表時，查詢提交者的安全性主體將會傳遞至基礎檔案。 權限會在檔案系統層級上進行檢查。

如需詳細資訊，請參閱 [Azure Synapse Analytics 共用資料庫](database.md)。

## <a name="change-maintenance"></a>變更維護

如果使用 Spark 刪除或變更中繼資料物件，則系統會取出變更並傳播至無伺服器 SQL 集區引擎。 同步並非即時進行，變更會在短暫延遲之後反映在 SQL 引擎中。

## <a name="next-steps"></a>後續步驟

- [深入了解 Azure Synapse Analytics 的共用中繼資料資料庫](database.md)
- [深入了解 Azure Synapse Analytics 的共用中繼資料資料表](table.md)

