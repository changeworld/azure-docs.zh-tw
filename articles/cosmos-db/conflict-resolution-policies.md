---
title: Azure Cosmos DB 中的衝突解決類型和解決原則
description: 本文描述 Azure Cosmos DB 中的衝突類別和衝突解決原則。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: c3b1cf01cbaa8de8ec33bbf9c19ee991ae898d58
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82182747"
---
# <a name="conflict-types-and-resolution-policies"></a>衝突類型和解決原則

如果您的 Azure Cosmos DB 帳戶設定了多個寫入區域，就適用衝突和衝突解決原則。

針對使用多個寫入區域設定的 Azure Cosmos 帳戶，當寫入器同時更新多個區域中的相同專案時，可能會發生更新衝突。 更新衝突可以是下列三種類型：

* **插入衝突**：當應用程式在兩個或多個區域中同時插入具有相同唯一索引的兩個或多個專案時，可能會發生這些衝突。 例如，ID 屬性可能會發生此衝突。

* **取代衝突**：當應用程式在兩個或多個區域中同時更新相同的專案時，可能會發生這些衝突。

* **刪除衝突**：當應用程式同時刪除某個區域中的專案，並在另一個區域中更新時，可能會發生這些衝突。

## <a name="conflict-resolution-policies"></a>衝突解決原則

Azure Cosmos DB 提供彈性的原則導向機制來解決寫入衝突。 您可以從 Azure Cosmos 容器上的兩個衝突解決原則中進行選取：

* **最後寫入為准（LWW）**：此解決原則預設會使用系統定義的時間戳記屬性。 它是以時間同步化時鐘通訊協定為基礎。 如果您使用 SQL API，您可以指定要用於衝突解決的任何其他自訂數值屬性（例如您自己的時間戳記概念）。 自訂數值屬性也稱為「*衝突解決路徑*」。 

  如果兩個或更多項目在插入或取代作業時發生衝突，則包含衝突解決路徑最高值的項目會成為「優先」項目。 如果多個項目的衝突解決路徑值都相同，則由系統決定「優先」項目。 所有區域保證都會彙整出單一優先項目，且認可項目的版本最後都會相同。 如果涉及刪除衝突，則刪除的版本一律優先於插入或取代衝突。 不論衝突解決路徑的值為何，都會發生這個結果。

  > [!NOTE]
  > [上次寫入 Wins] 是預設的衝突解決原則，並使用下列 Api 的時間戳記 `_ts` ： SQL、MongoDB、Cassandra、Gremlin 和 Table。 自訂數值屬性僅適用于 SQL API。

  若要深入了解，請參閱[使用 LWW 衝突解決原則的範例](how-to-manage-conflicts.md)。

* **自訂**：此解決原則是針對應用程式定義的衝突協調的語義所設計。 當您在 Azure Cosmos 容器上設定此原則時，您也需要註冊*合併預存*程式。 在伺服器上的資料庫交易下偵測到衝突時，就會自動叫用此程式。 系統只針對合併程序的執行提供一次保證 (為認可通訊協定的一部分)。  

  如果您使用自訂解析選項來設定容器，但無法在容器上註冊合併程式，或合併程式在執行時間擲回例外狀況，則衝突會寫入*衝突*摘要。 然後您的應用程式需要手動解決衝突摘要中的衝突。 若要深入了解，請參閱[如何使用自訂解決原則及如何使用衝突摘要的範例](how-to-manage-conflicts.md)。

  > [!NOTE]
  > 自訂衝突解決原則只適用於 SQL API 帳戶。

## <a name="next-steps"></a>後續步驟

瞭解如何設定衝突解決原則：

* [如何在您的應用程式中設定多宿主](how-to-multi-master.md)
* [如何管理衝突解決原則](how-to-manage-conflicts.md)
* [如何從衝突摘要讀取](how-to-manage-conflicts.md#read-from-conflict-feed)
