---
title: Azure 媒體服務 V2 和 V3 之間的功能差距 |Microsoft Docs
description: 本文說明 Azure 媒體服務 V2 與 v3 之間的功能差距。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 0f15c2bcd921c431dba1d1cce0454a6b2e752141
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98690314"
---
# <a name="feature-gaps-between-azure-media-services-v2-and-v3"></a>Azure 媒體服務 V2 和 V3 之間的功能差距

![遷移指南標誌](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![遷移步驟2](./media/migration-guide/steps-2.svg)

這部分的遷移指導方針提供 V2 和 V3 Api 之間差異的詳細資訊。

## <a name="feature-gaps-between-v2-and-v3-apis"></a>V2 和 V3 Api 之間的功能差距

V3 API 具有 V2 API 的下列功能間距。 V2 Api 中媒體編碼器標準的一些先進功能目前無法在 V3 中使用：

- 當輸入沒有音訊時插入無提示音訊播放軌，因為 Azure 媒體播放機不再需要。

- 在輸入沒有影片時插入影片播放軌。

- 使用轉碼的實況活動目前不支援透過 API 呼叫來插入中間資料流程和廣告標記。

- V2 將不再支援 Azure 媒體 Premium 編碼器。 如果您使用它進行8位 HEVC 編碼，請使用標準編碼器中的新 HEVC 支援。 
    - 我們已將音訊通道對應的支援新增至標準編碼器。  請參閱 [媒體服務編碼 Swagger 檔中的音訊](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json)。
    - 如果您使用的是協力廠商授權產品（例如 MXF 或 ProRes）的 advanced 功能或輸出格式，請使用 Telestream 中的 Azure 合作夥伴解決方案，此解決方案將會在 V2 淘汰時進行交易。 或者，您也可以使用想像通訊或 [Bitmovin](http://bitmovin.com)。

- V2 中的串流端點上的「可用性設定組」屬性已不再支援。 請參閱 V3 API 中的範例專案和 [高可用性 VOD](https://docs.microsoft.com/azure/media-services/latest/media-services-high-availability-encoding) 傳遞的指引。

- 在媒體服務 V3 中，無法指定 FairPlay IV。 雖然它不會影響使用媒體服務進行封裝和授權傳遞的客戶，但使用協力廠商 DRM 系統來傳遞 FairPlay 授權 (混合模式) 可能會有問題。

- V3 API 已移除用於保護待用資產的用戶端儲存體加密，並且會以待用資料的儲存體服務加密取代。 V3 Api 仍可繼續使用現有的儲存體加密資產，但不允許建立新的資產。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
