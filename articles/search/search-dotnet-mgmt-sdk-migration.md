---
title: 升級至 Azure 搜尋服務 .NET 管理 SDK
titleSuffix: Azure Cognitive Search
description: 從先前的版本升級至 Azure 搜尋服務 .NET 管理 SDK。 瞭解新功能，以及遷移所需的程式碼變更。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: 8648347eb48081389cf360fa949b31bbd0b8c71e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88936702"
---
# <a name="upgrading-versions-of-the-azure-search-net-management-sdk"></a>升級 Azure 搜尋服務 .NET 管理 SDK 的版本

本文說明如何遷移至 Azure 搜尋服務 .NET 管理 SDK 的後續版本，用來布建或取消布建搜尋服務、調整容量，以及管理 API 金鑰。

管理 Sdk 的目標是管理 REST API 的特定版本。 如需概念和作業的詳細資訊，請參閱 [搜尋管理 (REST) ](/rest/api/searchmanagement/)。

## <a name="versions"></a>版本

| SDK 版本 | 對應的 REST API 版本 | 功能新增或行為變更 |
|-------------|--------------------------------|-------------------------------------|
| [3.0](https://www.nuget.org/packages/Microsoft.Azure.Management.Search/3.0.0) | api 版本 = 2020-30-20 | 新增端點安全性 (IP 防火牆和與 [Azure Private Link](../private-link/private-endpoint-overview.md) 的整合)  |
| [2.0](https://www.nuget.org/packages/Microsoft.Azure.Management.Search/2.0.0) | api 版本 = 2019-10-01 | 可用性改良。 已停止)  (GET 的 [清單查詢金鑰](/rest/api/searchmanagement/querykeys/listbysearchservice) 的重大變更。 |
| [1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.Search/1.0.1) | api 版本 = 2015-08-19  | 第一個版本 |

## <a name="how-to-upgrade"></a>如何升級

1. `Microsoft.Azure.Management.Search`使用 nuget 封裝管理員主控台或以滑鼠右鍵按一下您的專案參考，然後選取 [管理 NuGet 套件 ...]，以更新您的 nuget 參考。在 Visual Studio。

1. 一旦 NuGet 下載新的封裝和其相依性，請重建您的專案。 視程式碼的結構而定，它可能會成功重建，在這種情況下您已完成。

1. 如果建置失敗，原因可能是您已實作某些已有所變更的 SDK 介面 (例如，為了進行單元測試)。 若要解決此問題，您必須執行較新的方法，例如 `BeginCreateOrUpdateWithHttpMessagesAsync` 。

1. 修正任何組建錯誤之後，您可以對應用程式進行變更，以利用新功能。 

## <a name="upgrade-to-30"></a>升級至3。0

3.0 版會藉由限制對 IP 範圍的存取，以及選擇性地與不應該在公用網際網路上顯示之搜尋服務的 Azure Private Link 整合，來新增私用端點保護。

### <a name="new-apis"></a>新的 API

| API | 類別| 詳細資料 |
|-----|--------|------------------|
| [NetworkRuleSet](/rest/api/searchmanagement/services/createorupdate#networkruleset) | IP 防火牆 | 將服務端點的存取許可權制為允許的 IP 位址清單。 請參閱 [設定 IP 防火牆](service-configure-firewall.md) 的概念和入口網站指示。 |
| [共用 Private Link 資源](/rest/api/searchmanagement/sharedprivatelinkresources) | 私人連結 | 建立搜尋服務所要使用的共用私人連結資源。  |
| [私人端點連接](/rest/api/searchmanagement/privateendpointconnections) | 私人連結 | 透過私人端點建立及管理與搜尋服務的連線。 請參閱建立概念和入口網站指示的 [私人端點](service-create-private-endpoint.md) 。|
| [Private Link 資源](/rest/api/searchmanagement/privatelinkresources/) | 私人連結 | 若為具有私人端點連線的搜尋服務，請取得在相同虛擬網路中使用的所有服務清單。 如果您的搜尋解決方案包含從 Azure 資料來源提取的索引子 (Azure 儲存體、Cosmos DB、Azure SQL) ，或使用認知服務或 Key Vault，則所有這些資源在虛擬網路中都應該有端點，而且此 API 應該會傳回清單。 |
| [將 publicnetworkaccess 新增](/rest/api/searchmanagement/services/createorupdate#publicnetworkaccess)| 私人連結 | 這是建立或更新服務要求的屬性。 停用時，private link 是唯一的存取樣式。 |

### <a name="breaking-changes"></a>重大變更

您無法再對 [清單查詢金鑰](/rest/api/searchmanagement/querykeys/listbysearchservice) 要求使用 GET。 在先前的版本中，您可以使用 GET 或 POST，在此版本中，而在所有版本中向前移動，僅支援 POST。 

## <a name="upgrade-to-20"></a>升級至2。0

第2版的 Azure 搜尋服務 .NET 管理 SDK 是次要升級，因此變更您的程式碼應該只需要最少的工作。SDK 的變更完全是用戶端的變更，以改善 SDK 本身的可用性。 這些變更包括下列幾項：

* `Services.CreateOrUpdate` 和其非同步版本現在會自動輪詢佈建 `SearchService`，且在服務佈建完成之前不會傳回。 這可讓您不必自行撰寫這類輪詢程式碼。

* 如果您仍要手動輪詢服務佈建，則可以使用新的 `Services.BeginCreateOrUpdate` 方法，也可以使用它的其中一個非同步版本。

* 新方法 `Services.Update` 和其非同步版本都已新增至 SDK。 這些方法會使用 HTTP PATCH 來支援累加式的服務更新。 例如，您現在可以將僅包含所需 `partitionCount` 和 `replicaCount` 屬性 `SearchService` 的執行個體傳遞給這些方法，來調整服務。 目前仍支援呼叫 `Services.Get`、修改傳回的 `SearchService`，並將其傳遞至 `Services.CreateOrUpdate` 的舊方法，但您不再必須使用此方法。 

## <a name="next-steps"></a>接下來的步驟

如果您遇到問題，最適合張貼問題的論壇是 [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cognitive-search?tab=Newest)。 如果您發現錯誤，您可以在 [Azure .NET SDK GitHub 儲存機制](https://github.com/Azure/azure-sdk-for-net/issues)中提出問題。 請務必使用 [搜尋] 來標記問題標題。