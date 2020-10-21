---
title: 在 dns 多區域性環境中診斷 Azure Cosmos Sdk 的可用性並進行疑難排解
description: 瞭解如何在多個區域環境中運作時的 Azure Cosmos SDK 可用性行為。
author: ealsur
ms.service: cosmos-db
ms.date: 10/20/2020
ms.author: maquaran
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: d43305040e7896a9d3a58929537f19c2bd1f526c
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92319372"
---
# <a name="diagnose-and-troubleshoot-the-availability-of-azure-cosmos-sdks-in-multiregional-environments"></a>在 dns 多區域性環境中診斷 Azure Cosmos Sdk 的可用性並進行疑難排解

本文說明當您看到特定區域發生連線問題或發生區域容錯移轉時，最新版 Azure Cosmos Sdk 的行為。

所有 Azure Cosmos Sdk 都會提供您自訂區域喜好設定的選項。 下列屬性用於不同的 Sdk：

* .NET V2 SDK 中的 [ConnectionPolicy. PreferredLocations](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations) 屬性。
* .NET V3 SDK 中的 [CosmosClientOptions. ApplicationRegion](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion) 或 [CosmosClientOptions. ApplicationPreferredRegions](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationpreferredregions) 屬性。
* JAVA V4 SDK 中的 [CosmosClientBuilder. preferredRegions](/java/api/com.azure.cosmos.cosmosclientbuilder.preferredregions) 方法。
* Python SDK 中的 [CosmosClient.preferred_locations](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient) 參數。
* JS SDK 中的 [CosmosClientOptions. ConnectionPolicy. preferredLocations](/javascript/api/@azure/cosmos/connectionpolicy#preferredlocations) 參數。

當您設定地區喜好設定時，用戶端將會連線到下表所述的區域：

|帳戶類型 |讀取 |寫入 |
|------------------------|--|--|
| 單一寫入區域 | 慣用區域 | 主要區域  |
| 多個寫入區域 | 慣用區域 | 慣用區域  |

如果您 **未設定慣用區域**，SDK 用戶端會預設為主要區域：

|帳戶類型 |讀取 |寫入 |
|------------------------|--|--|
| 單一寫入區域 | 主要區域 | 主要區域 |
| 多個寫入區域 | 主要區域  | 主要區域  |

> [!NOTE]
> 主要區域指的是[Azure Cosmos 帳戶區域清單](distribute-data-globally.md)中的第一個區域

在正常情況下，除非發生下列任何一種情況，否則 SDK 用戶端會連線到慣用的區域 (如果區域偏好設定) 或主要區域中 (如果沒有設定) 的喜好設定，則作業將會限制為該區域。

在這些情況下，使用 Azure Cosmos SDK 的用戶端會公開記錄，並在作業 **診斷資訊**中包含重試資訊：

* .NET V2 SDK 回應中的 *RequestDiagnosticsString* 屬性。
* .NET V3 SDK 中回應和例外狀況的 *診斷* 屬性。
* *GetDiagnostics ( # B1*方法來回應 JAVA V4 SDK 中的回應和例外狀況。

依喜好設定順序決定下一個區域時，SDK 用戶端會使用帳戶區域清單，將慣用區域的優先順序設定為 (如果有任何) 。

如需有關這些事件期間 SLA 保證的完整詳細資訊，請參閱 [可用性 sla](high-availability.md#slas-for-availability)。

## <a name="removing-a-region-from-the-account"></a><a id="remove-region"></a>從帳戶移除區域

當您從 Azure Cosmos 帳戶移除區域時，主動使用此帳戶的任何 SDK 用戶端都將會透過後端回應碼來偵測移除區域。 用戶端接著會將區域端點標示為無法使用。 用戶端會重試目前的作業，而且所有未來的作業都會依喜好設定的順序永久路由至下一個區域。

## <a name="adding-a-region-to-an-account"></a>將區域新增至帳戶

Azure Cosmos SDK 用戶端每隔5分鐘會讀取帳戶設定，並重新整理知道的區域。

如果您移除區域，並在稍後將其新增回帳戶，則如果新增的區域在 SDK 設定中的區域喜好設定順序比目前連線的區域更高，SDK 將會切換回以永久使用此區域。 偵測到新增的區域之後，會將所有未來的要求導向至該區域。

如果您將用戶端設定為最好連接至 Azure Cosmos 帳戶沒有的區域，則會忽略慣用的區域。 如果您稍後新增該區域，用戶端會偵測到該區域，並將其永久切換至該區域。

## <a name="fail-over-the-write-region-in-a-single-write-region-account"></a><a id="manual-failover-single-region"></a>在單一寫入區域帳戶中容錯移轉寫入區域

如果您起始目前寫入區域的容錯移轉，下一個寫入要求將會失敗，並出現已知的後端回應。 偵測到此回應時，用戶端會查詢帳戶以學習新的寫入區域，並繼續重試目前的作業，並將所有未來的寫入作業永久路由至新的區域。

## <a name="regional-outage"></a>區域中斷

如果帳戶為單一寫入區域，且在寫入作業期間發生區域中斷，則行為類似于 [手動容錯移轉](#manual-failover-single-region)。 針對讀取要求或多個寫入區域帳戶，其行為類似于 [移除區域](#remove-region)。

## <a name="session-consistency-guarantees"></a>會話一致性保證

使用 [會話一致性](consistency-levels.md#guarantees-associated-with-consistency-levels)時，用戶端必須保證它可以讀取自己的寫入。 在 [讀取區域偏好設定] 與 [寫入區域] 不同的單一寫入區域帳戶中，可能會發生使用者發出寫入，以及從本機區域讀取時，本機區域尚未收到「資料複寫」 (速度的燈光條件約束) 。 在這種情況下，SDK 會偵測讀取作業的特定失敗，並在主要區域上重試讀取，以確保會話一致性。

## <a name="transient-connectivity-issues-on-tcp-protocol"></a>TCP 通訊協定上的暫時性連接問題

在將 Azure Cosmos SDK 用戶端設定為使用 TCP 通訊協定的情況下，針對指定的要求，可能會在某些情況下，網路狀況會暫時影響特定端點的通訊。 這些暫時性網路狀況可能會以 TCP 超時呈現。 用戶端會在一段時間內，于相同端點上的本機重試要求。

如果使用者已設定一個具有多個區域的慣用區域清單，且 Azure Cosmos 帳戶為多個寫入區域或單一寫入區域，且此作業為讀取要求，則用戶端會從喜好設定清單中的下一個區域重試該單一作業。

## <a name="next-steps"></a>後續步驟

* 檢查 [可用性 sla](high-availability.md#slas-for-availability)。
* 使用最新的 [.NET SDK](sql-api-sdk-dotnet-standard.md)
* 使用最新的 [JAVA SDK](sql-api-sdk-java-v4.md)
* 使用最新的 [PYTHON SDK](sql-api-sdk-python.md)
* 使用最新的 [NODE SDK](sql-api-sdk-node.md)
