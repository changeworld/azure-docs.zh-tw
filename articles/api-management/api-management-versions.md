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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87096198"
---
# <a name="versions-in-azure-api-management"></a>Azure API 管理中的版本

版本可讓您向開發人員展示相關的 Api 群組。 您可以使用版本安全地處理 API 中的重大變更。 用戶端可以選擇在準備就緒時使用新的 API 版本，而現有的用戶端會繼續使用較舊的版本。 版本會透過版本識別碼來區分， (是您選擇) 的任何字串值，而版本設定配置可讓用戶端識別他們想要使用的 API 版本。

在大部分的情況下，每個 API 版本都可以被視為獨立的 API。 兩個不同的 API 版本可能會有不同組的作業和不同的原則。

使用版本，您可以：

- 同時發佈多個版本的 API。
- 使用路徑、查詢字串或標頭來區分版本。
- 使用您想要用來識別版本的任何字串值，也就是數位、日期或名稱。
- 顯示開發人員入口網站上群組在一起的 API 版本。
- 採用現有 (未建立版本的) API，並在不中斷現有用戶端的情況下建立新的版本。

[遵循我們的逐步解說，開始使用版本。](./api-management-get-started-publish-versions.md)

## <a name="versioning-schemes"></a>版本控制架構

不同的 API 開發人員有不同的版本控制需求。 Azure API 管理不會規定進行版本設定的單一方法，而是會提供數個選項。

### <a name="path-based-versioning"></a>以路徑為基礎的版本控制

使用路徑版本設定配置時，版本識別碼必須包含在任何 API 要求的 URL 路徑中。

例如， `https://apis.contoso.com/products/v1` 和 `https://apis.contoso.com/products/v2` 可以分別參考相同的 `products` API 和版本 `v1` `v2` 。

使用標頭型版本控制時，API 要求 URL 的格式為： `https://{yourDomain}/{apiName}/{versionIdentifier}/{operationId}` 。

### <a name="header-based-versioning"></a>以標頭為基礎的版本控制

使用標頭版本設定配置時，版本識別碼必須包含在任何 API 要求的 HTTP 要求標頭中。 您可以指定 HTTP 要求標頭的名稱。

例如，您可以建立名為的自訂標頭 `Api-Version` ，而且用戶端可以指定 `v1` `v2` 此標頭的值或。

### <a name="query-string-based-versioning"></a>以字串為基礎的查詢版本控制

使用查詢字串版本設定配置時，版本識別碼必須包含在任何 API 要求的查詢字串參數中。 您可以指定查詢字串參數的名稱。

使用以查詢字串為基礎的版本控制時，API 要求 URL 的格式為： `https://{yourDomain}/{apiName}/{operationId}?{queryStringParameterName}={versionIdentifier}` 。

例如， `https://apis.contoso.com/products?api-version=v1` 和 `https://apis.contoso.com/products/api-version=v2` 可以分別參考相同的 `products` API 和版本 `v1` `v2` 。

## <a name="original-versions"></a>原始版本

如果您將版本新增至未建立版本的 API，則會 `Original` 自動建立版本，並會在未指定版本識別碼的情況下，于預設 URL 上回應。 `Original`版本可確保任何現有的呼叫端都不會因新增版本的程式而中斷。 如果您建立新的 API，並在一開始就啟用了版本，則 `Original` 不會建立版本。

## <a name="how-versions-are-represented"></a>如何表示版本

Azure API 管理會維護稱為 *版本設定*的資源，代表單一邏輯 API 的一組版本。 當您使用 Azure 入口網站來管理版本時，您看不到版本設定，但如果您使用 PowerShell、Resource Manager 範本或 Azure Resource Manager API 與 API 管理服務互動，您可以直接查看和管理版本集。 版本設定包含已建立版本之 API 的顯示名稱，以及用來將要求導向至指定版本的 [版本控制配置](#versioning-schemes) 。

API 的每個版本都會保留為其本身的 API 資源，然後與版本集合相關聯。 版本設定可能包含具有非常不同作業或原則的 Api，這反映了您可能會在 API 版本之間進行重大變更的事實。

### <a name="migrating-a-non-versioned-api-to-a-versioned-api"></a>將未建立版本的 API 遷移至已建立版本的 API

當您使用 Azure 入口網站啟用現有 API 的版本控制時，會對您的 API 管理資源進行下列變更：

 * 建立新的版本集。
 * 現有的版本會維護並 [設定為 `Original` API 版本](#original-versions)。 API 已連結至版本集，但不需要指定版本識別碼。
 * 新版本會建立為新的 API，並連結至版本集。 您必須使用版本設定配置和識別碼來存取這個新的 API。

## <a name="versions-and-revisions"></a>版本和修訂

版本和修訂是不同的功能。 每個版本都可以有多個修訂，就像未建立版本的 API 一樣。 您可以使用不使用版本的修訂，也可以使用其他方式。 通常版本是用來分隔 API 版本與重大變更，而修訂可以用於 API 的次要和非重大變更。

如果您發現修訂有重大變更，或者您想要將修訂正式轉換成 Beta/test 版本，您可以從修訂建立版本。 使用 Azure 入口網站，在 [修訂] 索引標籤的 [修訂] 內容功能表上，按一下 [從修訂建立版本]。

## <a name="developer-portal"></a>開發人員入口網站

[開發人員入口網站](./api-management-howto-developer-portal.md)會分別列出 API 的每個版本。

![API 管理開發人員入口網站，顯示已建立版本的 Api 清單](media/api-management-versions/portal-list.png)

API 的詳細資料也會顯示該 API 所有版本的清單。 不 `Original` 含版本識別碼的版本隨即顯示。

![API 管理開發人員入口網站會顯示 API 的詳細資料，以及該 API 的版本清單](media/api-management-versions/portal-details.png)

> [!TIP]
> API 版本必須先新增至產品，才能在開發人員入口網站上看到。
