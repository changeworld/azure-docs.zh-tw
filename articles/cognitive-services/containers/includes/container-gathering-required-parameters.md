---
title: 收集所需的參數
services: cognitive-services
author: aahill
manager: nitinme
description: 所有認知服務容器的參數
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 85c67384c38376dc6f5701cf26e879d6e1b76c7f
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875026"
---
## <a name="gathering-required-parameters"></a>收集所需的參數

所有認知服務容器都需要三個主要參數。 最終使用者許可協定 (EULA) 必須具有的`accept`值。 此外,還需要終結點 URL 和 API 金鑰。

### <a name="endpoint-uri-endpoint_uri"></a>端點 URI`{ENDPOINT_URI}`

**端點**URI 值在相應認知服務資源的 Azure 門戶*概述*頁上可用。 導覽到 *「概述」* 頁,將滑鼠懸停在`Copy to clipboard`<span class="docon docon-edit-copy x-hidden-focus"></span>終結點上 ,並顯示一個圖示。 根據需要複製和使用。

![收集終結點 uri 供以後使用](../media/overview-endpoint-uri.png)

### <a name="keys-api_key"></a>鑰匙`{API_KEY}`

此密鑰用於啟動容器,並在相應認知服務資源的 Azure 門戶的「密鑰」頁上可用。 導航到 *「密鑰」* 頁,然後按`Copy to clipboard`<span class="docon docon-edit-copy x-hidden-focus"></span>一下該 圖示。

![取得兩個金鑰之一,供以後使用](../media/keys-copy-api-key.png)

> [!IMPORTANT]
> 這些訂閱金鑰用於存取您的認知服務API。 不要共用您的金鑰。 使用 Azure 金鑰保管庫安全地儲存它們。 我們還建議定期重新生成這些密鑰。 進行 API 調用只需要一個密鑰。 重新生成第一個鍵時,可以使用第二個鍵繼續訪問服務。