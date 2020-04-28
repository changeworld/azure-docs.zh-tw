---
title: 設計用於資料修改的 Azure 資料表儲存體 |Microsoft Docs
description: 在 Azure 資料表儲存體中設計資料修改的資料表。
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: c95be7afae5c0a84c06b691c8225f32f2aa68260
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75771541"
---
# <a name="design-for-data-modification"></a>資料修改的設計
本文著重於最佳化插入、更新和刪除的設計考量。 在某些情況下，您必須在查詢最佳化的設計與資料修改最佳化的設計之間評估取捨，如同您在設計關聯式資料庫時一般 (雖然在關聯式資料庫中用來管理設計取捨的方法有所不同)。 資料表設計模式一節會說明資料表服務的一些詳細設計模式，並強調說明一些相關取捨。 在實務上，您會發現許多針對查詢實體而最佳化的設計也適用於修改實體。  

## <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>最佳化插入、更新和刪除作業的效能
若要更新或刪除實體，您必須能夠使用 **PartitionKey** 和 **RowKey** 值加以識別。 在這方面，您選擇用來修改實體的 **PartitionKey** 和 **RowKey**，應遵循您在支援點查詢時進行選擇的類似準則，因為您想要盡可能有效率地識別實體。 您不應使用效率不佳的資料分割或資料表掃描來尋找實體以探索 **PartitionKey** 和 **RowKey** 值，而導致您必須加以更新或刪除。  

資料表設計模式一節中的下列模式可因應最佳化效能或插入、更新和刪除作業：  

* [大量刪除模式](table-storage-design-patterns.md#high-volume-delete-pattern) - 藉由將所有要同時刪除的實體儲存在其本身的個別資料表中，讓您能夠刪除大量的實體；您可以藉由刪除資料表來刪除實體。  
* [資料序列模式](table-storage-design-patterns.md#data-series-pattern) - 將完整資料序列儲存在單一實體中，以盡可能減少您提出的要求數。  
* [寬型實體模式](table-storage-design-patterns.md#wide-entities-pattern) - 使用多個實際的實體來儲存具有超過 252 個屬性的邏輯實體。  
* [大型實體模式](table-storage-design-patterns.md#large-entities-pattern) - 使用 Blob 儲存體來儲存大型屬性值。  

## <a name="ensure-consistency-in-your-stored-entities"></a>確保預存實體中的一致性
另一個會在您選擇最佳化資料修改的索引鍵時造成影響的主要因素，是如何使用不可部分完成的交易確保一致性。 只有對於儲存在相同資料分割中的實體，才能使用 EGT 來運作。  

[資料表設計模式](table-storage-design-patterns.md) 一文中的下列模式說明管理一致性：  

* [內部資料分割次要索引模式](table-storage-design-patterns.md#intra-partition-secondary-index-pattern)-使用不同的**RowKey**值（在相同的資料分割中）儲存每個實體的多個複本，以使用不同的**RowKey**值來啟用快速且有效率的查閱和替代排序次序。  
* [間資料分割次要索引模式](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) - 在個別資料分割或個別資料表中為每個實體儲存多個複本且使用不同 RowKey 值，透過使用不同的 **RowKey** 值，就能快速有效率地查閱和替代排序次序。  
* [最終一致的交易模式](table-storage-design-patterns.md#eventually-consistent-transactions-pattern) - 使用 Azure 佇列，跨資料分割界限或儲存體系統界限啟用最終一致的行為。
* [索引實體模式](table-storage-design-patterns.md#index-entities-pattern)-維護索引實體，以啟用有效率的搜尋，以傳回實體清單。  
* [反正規化模式](table-storage-design-patterns.md#denormalization-pattern) - 將相關資料結合在單一實體中，讓您透過單點查詢擷取所有您所需的資料。  
* [資料序列模式](table-storage-design-patterns.md#data-series-pattern) - 將完整資料序列儲存在單一實體中，以盡可能減少您提出的要求數。  

如需實體群組交易的詳細資訊，請參閱[實體群組交易](table-storage-design.md#entity-group-transactions)一節。  

## <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>確保您針對有效率的修改所做的設計有助於提升查詢效率
在許多情況下，效率查詢的設計都可造就有效的修改，但是您務必要評估這對您的特定案例是否適用。 [資料表設計模式](table-storage-design-patterns.md) 一文中的某些模式會在查詢和修改實體之間明確評估取捨，您應一律將各種作業類型的數目納入考量。  

[資料表設計模式](table-storage-design-patterns.md) 一文中的下列模式可因應如何在有效查詢的設計與有效資料修改的設計之間進行取捨：  

* [複合索引鍵模式](table-storage-design-patterns.md#compound-key-pattern) - 使用複合 **RowKey** 值，讓用戶端可透過單點查詢來查閱相關資料。  
* [記錄結尾模式](table-storage-design-patterns.md#log-tail-pattern) - 使用以反向的日期和時間順序排序的 *RowKey* 值，擷取最近加入資料分割的 **n** 個實體。  
