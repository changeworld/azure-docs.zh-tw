---
title: Azure Cosmos DB 的自動化效能、成本、安全性建議
description: 瞭解如何根據您的工作負載模式，來查看 Azure Cosmos DB 的自訂效能、成本、安全性和其他建議。
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/28/2020
ms.reviewer: sngun
ms.openlocfilehash: d9eb55030c7ec52f9b2ac79fbab19944f0a3e190
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93087820"
---
# <a name="automated-recommendations-for-azure-cosmos-db"></a>Azure Cosmos DB 的自動化建議
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

所有的雲端服務（包括 Azure Cosmos DB）都會定期更新，並提供新功能、功能和增強功能。 您的應用程式必須隨時掌握最新的效能和安全性更新。 Azure 入口網站提供自訂的建議，可讓您將應用程式的效能最大化。 Azure Cosmos DB 的諮詢引擎會持續分析您 Azure Cosmos DB 資源的使用量歷程記錄，並根據您的工作負載模式提供建議。 這些建議對應于分割、索引、網路、安全性等領域。這些自訂的建議可協助您改善應用程式的效能。

## <a name="view-recommendations"></a>檢視建議

您可以透過下列方式來查看 Azure Cosmos DB 的建議：

- 查看建議的其中一種方式是在 [通知] 索引標籤中。如果有新的建議，您將會看到一個消息列。 登入您的 [Azure 入口網站](https://portal.azure.com) ，然後流覽至您的 Azure Cosmos 帳戶。 在您的 Azure Cosmos 帳戶中，開啟 [ **通知** ] 窗格，然後選取 [ **建議** ] 索引標籤。您可以選取訊息和查看建議。  

   :::image type="content" source="./media/automated-recommendations/cosmos-db-pane-recommendations.png" alt-text="從 Azure Cosmos DB 窗格查看建議":::

- 您也可以透過不同的值區（例如成本、安全性、可靠性、效能及卓越營運）來分類 [Azure Advisor](../advisor/advisor-overview.md) 中的建議。 您可以選取特定的訂用帳戶，並依資源類型進行篩選，也就是 **Azure Cosmos DB 帳戶** 。  當您選取特定的建議時，它會顯示您可採取的動作來受益于您的工作負載。

   :::image type="content" source="./media/automated-recommendations/advisor-pane-recommendations.png" alt-text="從 Azure Cosmos DB 窗格查看建議":::

Azure Cosmos DB 窗格中所顯示的建議並非都可在 Azure Advisor 中使用，反之亦然。 這是因為它們是根據建議的類型，也就是在 Azure Advisor 窗格中，Azure Cosmos DB] 窗格或兩者。

目前 Azure Cosmos DB 支援下欄區域的建議。 這些建議都包含檔相關章節的連結，因此您可以輕鬆地採取後續步驟。

## <a name="sdk-usage-recommendations"></a>SDK 使用方式建議

在這個類別中，advisor 會偵測舊版 Sdk 的使用方式，並建議您升級至較新版本，以利用最新的錯誤修正和效能改進。 目前提供下列 SDK 特定建議：

|名稱  |描述  |
|---------|---------|
| 舊的 Spark 連接器 | 偵測舊版 Spark 連接器的使用方式，並建議升級。 |
| 舊版 .NET SDK | 偵測舊版 .NET SDK 的使用方式，並建議升級。 |
| 舊的 JAVA SDK | 偵測舊版 JAVA 連接器的使用方式，並建議升級。 |

## <a name="indexing-recommendations"></a>索引建議

在此類別中，advisor 會偵測索引編制模式、編制索引原則、索引路徑，並建議在目前的設定影響查詢效能時變更。 目前提供下列索引特定建議：

|名稱  |描述  |
|---------|---------|
| 延遲編制索引 | 偵測延遲編制索引模式的使用方式，並建議改用一致的索引編制模式。 Azure Cosmos DB 的延遲編制索引模式的用途有限，而且在某些情況下可能會影響查詢結果的有效期限，因此建議使用一致的索引模式。 |
| 複合索引| 偵測查詢可從中受益的帳戶，並建議使用它們。 複合索引可以大幅改善某些查詢的效能和輸送量耗用量。|
| 具有許多索引路徑的預設索引編制原則 | 偵測在預設索引編制中使用許多索引路徑執行的容器，並建議自訂編制索引原則。|
| 以高 RU/秒計費的查詢順序| 偵測具有高 RU/秒費用的查詢發出訂單，並建議您探索複合索引。|
| 沒有索引和高 RU/秒耗用量的 MongoDB 3.6 帳戶| 偵測 Azure Cosmos DB 適用于 MongoDB 的 API，其3.6 版的容器會以高 RU/s 的費率發出查詢，並建議新增索引。|

## <a name="cost-optimization-recommendations"></a>成本優化建議

在此類別中，advisor 會偵測 RU/秒使用量，並判斷您可以對您的資源進行某些變更或利用不同的定價模型，以將價格優化。 目前提供下列成本優化特定建議：

|名稱  |描述  |
|---------|---------|
| 保留容量 | 偵測您的 RU/秒使用率，並建議可從中獲益的使用者保留實例。 |
| 非作用中容器 | 偵測尚未使用超過30天的容器，並建議減少這類容器的輸送量，或將其刪除。|
| 具有高輸送量的新訂用帳戶 | 偵測到帳戶的新訂用帳戶，每天都有極高的 RU/秒，並為他們提供通知。 這項通知是為了讓新客戶知道 Azure Cosmos DB 在布建的輸送量型模型和非耗用量型模型上運作。 |

## <a name="migration-recommendations"></a>遷移建議

在這個類別中，advisor 會偵測到您使用的是舊版功能，建議您進行遷移，讓您可以利用 Azure Cosmos DB 的大規模擴充性和其他優勢。 以下是目前可用的遷移特定建議：

|名稱  |描述  |
|---------|---------|
| 非分割的容器 | 偵測接近其最大儲存空間限制的固定大小容器，並建議將它們遷移至已分割的容器。|

## <a name="query-usage-recommendations"></a>查詢使用方式建議

在這個類別中，advisor 會偵測查詢執行，並識別查詢效能可透過某些變更進行調整。 目前提供下列查詢使用方式建議：

|名稱  |描述  |
|---------|---------|
| 具有固定頁面大小的查詢 | 偵測以固定頁面大小發出的查詢，並建議使用-1 (沒有頁面大小的限制) 而不是定義特定的值。 此選項可減少取出所有結果所需的網路來回行程次數。 |

## <a name="next-steps"></a>下一步

* [調整 Azure Cosmos DB 中的查詢效能](sql-api-query-metrics.md)
* 針對使用 Azure Cosmos DB 時的[查詢問題進行疑難排解](troubleshoot-query-performance.md)
