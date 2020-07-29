---
title: 使用影片索引器管理多個租用戶 - Azure
description: 本文會建議使用影片索引器管理多個租用戶的不同整合選項。
services: media-services
documentationcenter: ''
author: ika-microsoft
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 05/15/2019
ms.author: ikbarmen
ms.openlocfilehash: 18f2cf3daa281400151ba223e1735e7138d97e8e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "76990499"
---
# <a name="manage-multiple-tenants"></a>管理多個租用戶

本文會討論使用影片索引器管理多個租用戶的不同選項。 選擇最適合您案例的方法：

* 每個租用戶的影片索引器帳戶
* 所有租用戶的單一影片索引器帳戶
* 每個租用戶的 Azure 訂用帳戶

## <a name="video-indexer-account-per-tenant"></a>每個租用戶的影片索引器帳戶

使用此架構時，會為每個租用戶建立一個影片索引器帳戶。 租用戶在持續層和計算層中完全隔離。  

![每個租用戶的影片索引器帳戶](./media/manage-multiple-tenants/video-indexer-account-per-tenant.png)

### <a name="considerations"></a>考量

* 客戶不會共用儲存體帳戶 (除非客戶手動設定)。
* 客戶不會共用計算 (保留單位)，也不會影響彼此的處理作業次數。
* 您可以透過刪除影片索引器帳戶，輕鬆地從系統中移除租用戶。
* 無法在租用戶之間共用自訂模型。

    請確定共用自訂模型沒有商務需求。
* 由於每個租用戶擁有多個影片索引器 (及相關的媒體服務) 帳戶，因此難以管理。

> [!TIP]
> 在[影片索引器開發人員入口網站](https://api-portal.videoindexer.ai/)中為您的系統建立系統管理員使用者，並使用授權 API 為您的租用戶提供相關的[帳戶存取權杖](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Account-Access-Token)。

## <a name="single-video-indexer-account-for-all-users"></a>所有使用者的單一影片索引器帳戶

使用此架構時，客戶須負責租用戶隔離。 所有租用戶都必須使用單一影片索引器帳戶和單一 Azure 媒體服務帳戶。 在上傳、搜尋或刪除內容時，客戶必須篩選該租用戶的適當結果。

![所有使用者的單一影片索引器帳戶](./media/manage-multiple-tenants/single-video-indexer-account-for-all-users.png)

使用此選項，可以依租用戶篩選模型，在租用戶之間共用或隔離自訂模型 (人員，語言和品牌)。

當[上傳影片](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?)時，您可以為每個租用戶指定不同的資料分割屬性。 這將允許[搜尋 API](https://api-portal.videoindexer.ai/docs/services/operations/operations/Search-videos?) 中的隔離。 藉由在搜尋 API 中指定分割區屬性，您將只會取得指定分割區的結果。 

### <a name="considerations"></a>考量

* 能夠在租用戶之間共用內容和自訂模型。
* 一個租用戶會影響其他租用戶的效能。
* 客戶必須在影片索引器之上建置複雜的管理層。

> [!TIP]
> 您可以使用[優先順序](upload-index-videos.md)屬性來排列租用戶作業的優先順序。

## <a name="azure-subscription-per-tenant"></a>每個租用戶的 Azure 訂用帳戶 

使用此架構時，每個租使用者都會有自己的 Azure 訂用帳戶。 針對每個使用者，您將在租用戶訂用帳戶中建立新的影片索引器帳戶。

![每個租用戶的 Azure 訂用帳戶](./media/manage-multiple-tenants/azure-subscription-per-tenant.png)

### <a name="considerations"></a>考量

* 這是啟用計費區隔的唯一選項。
* 此整合比每個租用戶的影片索引器帳戶具有更多的管理額外負荷。 如果不要求計費，建議使用本文中所述的選項之一。

## <a name="next-steps"></a>後續步驟

[概觀](video-indexer-overview.md)
