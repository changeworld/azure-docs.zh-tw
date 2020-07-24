---
title: Azure API 管理中的版本 |Microsoft Docs
description: 瞭解 Azure API 管理中版本的概念。
services: api-management
documentationcenter: ''
author: johndowns
ms.service: api-management
ms.topic: article
ms.date: 06/12/2020
ms.author: jodowns
ms.custom: fasttrack-new
ms.openlocfilehash: 578bb511175d88a1507af9520265a1acd068b27c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87096198"
---
# <a name="versions-in-azure-api-management"></a>Azure API 管理中的版本

版本可讓您將相關的 Api 群組提供給開發人員。 您可以使用版本安全地處理 API 中的重大變更。 用戶端可以選擇在準備好時使用新的 API 版本，而現有的用戶端則會繼續使用較舊的版本。 版本會透過版本識別碼（也就是您選擇的任何字串值）來區別，而版本配置則可讓用戶端識別他們想要使用的 API 版本。

在大部分的情況下，可以將每個 API 版本視為自己的獨立 API。 兩個不同的 API 版本可能會有不同的作業集和不同的原則。

有了版本，您可以：

- 同時發佈多個 API 版本。
- 使用路徑、查詢字串或標頭來區分版本。
- 使用您想要用來識別版本的任何字串值，這可能是數位、日期或名稱。
- 在開發人員入口網站上，將您的 API 版本群組在一起。
- 採用現有（未建立版本） API，並建立新版本的，而不中斷現有的用戶端。

[遵循我們的逐步解說，開始使用版本。](./api-management-get-started-publish-versions.md)

## <a name="versioning-schemes"></a>版本配置

不同的 API 開發人員有不同的版本控制需求。 Azure API 管理不會規定版本控制的單一方法，而是提供數個選項。

### <a name="path-based-versioning"></a>以路徑為基礎的版本控制

使用路徑版本設定配置時，必須將版本識別碼包含在任何 API 要求的 URL 路徑中。

例如， `https://apis.contoso.com/products/v1` 和 `https://apis.contoso.com/products/v2` 可能會參考相同的 `products` API，但分別為版本 `v1` 和 `v2` 。

使用標頭型版本設定時，API 要求 URL 的格式為： `https://{yourDomain}/{apiName}/{versionIdentifier}/{operationId}` 。

### <a name="header-based-versioning"></a>以標頭為基礎的版本控制

使用標頭版本設定配置時，必須在 HTTP 要求標頭中包含任何 API 要求的版本識別碼。 您可以指定 HTTP 要求標頭的名稱。

例如，您可能會建立名為的自訂標頭 `Api-Version` ，而用戶端可以 `v1` `v2` 在此標頭的值中指定或。

### <a name="query-string-based-versioning"></a>以查詢字串為基礎的版本控制

使用查詢字串版本控制配置時，必須在任何 API 要求的查詢字串參數中包含版本識別碼。 您可以指定查詢字串參數的名稱。

使用以查詢字串為基礎的版本控制時，API 要求 URL 的格式為： `https://{yourDomain}/{apiName}/{operationId}?{queryStringParameterName}={versionIdentifier}` 。

例如， `https://apis.contoso.com/products?api-version=v1` 和 `https://apis.contoso.com/products/api-version=v2` 可能會參考相同的 `products` API，但分別為版本 `v1` 和 `v2` 。

## <a name="original-versions"></a>原始版本

如果您將版本新增至未設定版本的 API， `Original` 將會自動建立版本，而且將會回應預設 URL，而不會指定版本識別碼。 `Original`版本可確保任何現有的呼叫端都不會因為新增版本的程式而中斷。 如果您建立新的 API，並在開始時啟用版本，則 `Original` 不會建立版本。

## <a name="how-versions-are-represented"></a>版本的表示方式

Azure API 管理會維護稱為*版本設定*的資源，這代表單一邏輯 API 的一組版本。 當您使用 Azure 入口網站來管理版本時，您看不到所設定的版本，但如果您使用 PowerShell、Resource Manager 範本或 Azure Resource Manager API 與您的 API 管理服務互動，則可以直接查看和管理版本集。 版本集包含已建立版本之 API 的顯示名稱，以及用來將要求導向至指定版本的版本[配置](#versioning-schemes)。

API 的每個版本都會保留為自己的 API 資源，然後與版本集建立關聯。 版本集可能包含具有非常不同作業或原則的 Api，這反映了您可能會在 API 版本之間進行重大變更。

### <a name="migrating-a-non-versioned-api-to-a-versioned-api"></a>將未建立版本的 API 遷移至已建立版本的 API

當您使用 Azure 入口網站在現有的 API 上啟用版本控制時，您的 API 管理資源會進行下列變更：

 * 隨即建立新的版本集。
 * 現有的版本會保留並[設定為 `Original` API 版本](#original-versions)。 API 已連結至版本集，但不需要指定版本識別碼。
 * 新版本會建立為新的 API，並連結至版本集。 這個新的 API 必須使用版本設定配置和識別碼來存取。

## <a name="versions-and-revisions"></a>版本和修訂

版本和修訂是不同的功能。 每個版本都可以有多個修訂，就像未建立版本的 API 一樣。 您可以使用未使用版本的修訂，或另一種方式。 通常版本是用來將 API 版本與重大變更分開，而修訂可以用於 API 的次要和非重大變更。

如果您的修訂有重大變更，或您想要正式地將修訂轉換成 Beta/test 版本，您可以從修訂建立版本。 使用 Azure 入口網站，按一下 [修訂] 索引標籤上 [修訂] 內容功能表上的 [從修訂建立版本]。

## <a name="developer-portal"></a>開發人員入口網站

[開發人員入口網站](./api-management-howto-developer-portal.md)會分別列出 API 的每個版本。

![API 管理開發人員入口網站，顯示已建立版本的 Api 清單](media/api-management-versions/portal-list.png)

API 的詳細資料也會顯示該 API 的所有版本清單。 `Original`不含版本識別碼的版本隨即顯示。

![API 管理開發人員入口網站，其中顯示 API 的詳細資料，以及該 API 的版本清單](media/api-management-versions/portal-details.png)

> [!TIP]
> 您必須先將 API 版本新增至產品，才能在開發人員入口網站上看到它們。
