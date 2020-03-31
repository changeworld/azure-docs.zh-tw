---
title: Azure Cosmos DB 中的衝突解決類型和解決原則
description: 本文描述 Azure Cosmos DB 中的衝突類別和衝突解決原則。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: a8ee72f46e1789088e779c10a0824262469ffde8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75441977"
---
# <a name="conflict-types-and-resolution-policies"></a>衝突類型和解決原則

如果您的 Azure Cosmos DB 帳戶設定了多個寫入區域，就適用衝突和衝突解決原則。

對於配置了多個寫入區域的 Azure Cosmos 帳戶，當編寫器在多個區域中同時更新同一項時，可能會發生更新衝突。 更新衝突可以是以下三種類型：

* **插入衝突**：當應用程式同時在兩個或多個區域中插入具有相同唯一索引的兩個或多個項時，可能會發生這些衝突。 例如，此衝突可能發生與 ID 屬性。

* **替換衝突**：當應用程式在兩個或多個區域中同時更新同一項時，可能會發生這些衝突。

* **刪除衝突**：當應用程式同時刪除一個區域中的項並在另一個區域中更新專案時，可能會發生這些衝突。

## <a name="conflict-resolution-policies"></a>衝突解決原則

Azure Cosmos DB 提供了一個靈活的策略驅動機制來解決寫入衝突。 您可以從 Azure Cosmos 容器上的兩個衝突解決策略中選擇：

* **上次寫入勝利 （LWW）：** 預設情況下，此解析策略使用系統定義的時間戳記屬性。 它是以時間同步化時鐘通訊協定為基礎。 如果使用 SQL API，則可以指定任何其他自訂數值屬性（例如，您自己的時間戳記概念）用於衝突解決。 自訂數值屬性也稱為*衝突解決路徑*。 

  如果兩個或更多項目在插入或取代作業時發生衝突，則包含衝突解決路徑最高值的項目會成為「優先」項目。 如果多個項目的衝突解決路徑值都相同，則由系統決定「優先」項目。 所有區域保證都會彙整出單一優先項目，且認可項目的版本最後都會相同。 如果涉及刪除衝突，則刪除的版本一律優先於插入或取代衝突。 無論衝突解決路徑的值如何，都會出現此結果。

  > [!NOTE]
  > 上次寫入勝利是預設的衝突解決策略，並使用時間戳`_ts`用於以下 API：SQL、MongoDB、Cassandra、Gremlin 和表。 自訂數值屬性僅適用于 SQL API。

  若要深入了解，請參閱[使用 LWW 衝突解決原則的範例](how-to-manage-conflicts.md)。

* **自訂**：此解析策略專為應用程式定義的語義而設計，用於衝突對賬。 在 Azure Cosmos 容器上設置此策略時，還需要註冊*合併預存程序*。 當在伺服器上的資料庫事務下檢測到衝突時，將自動調用此過程。 系統只針對合併程序的執行提供一次保證 (為認可通訊協定的一部分)。  

  如果使用自訂解析選項配置容器，並且無法在容器上註冊合併過程，或者合併過程在運行時引發異常，則衝突將寫入*衝突源*。 然後您的應用程式需要手動解決衝突摘要中的衝突。 若要深入了解，請參閱[如何使用自訂解決原則及如何使用衝突摘要的範例](how-to-manage-conflicts.md)。

  > [!NOTE]
  > 自訂衝突解決原則只適用於 SQL API 帳戶。

## <a name="next-steps"></a>後續步驟

瞭解如何配置衝突解決策略：

* [如何在應用程式中配置多主機](how-to-multi-master.md)
* [如何管理衝突解決策略](how-to-manage-conflicts.md)
* [如何從衝突源中讀取](how-to-manage-conflicts.md#read-from-conflict-feed)
