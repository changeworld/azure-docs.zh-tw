---
title: 媒體服務 V2 與 v3 API 存取
description: 本文說明 Azure 媒體服務 V2 到 V3 之間的 API 存取差異。
services: media-services
documentationcenter: na
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 831bc737fbd54685cdc8358642bc37e7b2df2c42
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98953711"
---
# <a name="api-access-differences-between-azure-media-services-v2-to-v3-api"></a>Azure 媒體服務 V2 與 v3 API 之間的 API 存取差異

![遷移指南標誌](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![遷移步驟2](./media/migration-guide/steps-2.svg)

本文說明 Azure 媒體服務 V2 到 V3 之間的 API 存取差異。

## <a name="api-access"></a>API 存取

所有媒體服務帳戶都可以存取 V3 API。 不過，在將更新的程式碼套用至現有的 V2 帳戶之前，強烈建議您在全新的帳戶上進行遷移開發。 這是因為 V3 實體與 V2 不相容。 某些 V2 實體（例如資產）會向前相容于 V3。
如果您沒有混合 V2 和 V3 Api，然後嘗試返回 V2，則可以繼續使用現有的帳戶，但不建議這麼做。

V2 API 的存取權將可供使用，直到在2024中淘汰為止。

當您在進行遷移時，可以建立可繼續存取 V2 的 V3 帳戶。  您可以透過下列方式來建立帳戶：

- REST API 和較舊的版本
- 選取入口網站中的核取方塊。

> [!div class="mx-imgBorder"]
> [![在入口網站 ](./media/migration-guide/v-3-v-2-access-account-creation-small.png) 中建立帳戶](./media/migration-guide/v-3-v-2-access-account-creation.png#lightbox)

所有的 .NET、CLI 和其他 Sdk 都會以最新的 2020-05-01 API 為目標，因此請尋找或設定較舊的 API 版本。

> [!NOTE]
> 使用 2020-05-01 API 建立的新帳戶無法使用 V2 Api。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
