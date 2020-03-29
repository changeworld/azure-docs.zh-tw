---
title: 收集所需的參數
services: cognitive-services
author: IEvangelist
manager: nitinme
description: 所有認知服務容器的參數
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2019
ms.author: dapine
ms.openlocfilehash: bf4e8f06642a4a764794a75e2b49f15958796c61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "73465924"
---
## <a name="gathering-required-parameters"></a>收集所需的參數

所有認知服務容器都需要三個主要參數。 使用者授權合約 （EULA） 必須具有 的值。 `accept` 此外，還需要終結點 URL 和 API 金鑰。

### <a name="endpoint-uri-endpoint_uri"></a>端點 URI`{ENDPOINT_URI}`

**端點**URI 值在相應認知服務資源的 Azure 門戶*概述*頁上可用。 導航到 *"概述"* 頁，將滑鼠懸停在終結點上`Copy to clipboard`<span class="docon docon-edit-copy x-hidden-focus"></span>，並顯示一個圖示。 根據需要複製和使用。

![收集終結點 uri 供以後使用](../media/overview-endpoint-uri.png)

### <a name="keys-api_key"></a>鑰匙`{API_KEY}`

此金鑰用於啟動容器，並在相應認知服務資源的 Azure 門戶的"金鑰"頁上可用。 導航到 *"金鑰"* 頁，然後按一下該`Copy to clipboard`<span class="docon docon-edit-copy x-hidden-focus"></span>圖示。

![獲取兩個金鑰之一，供以後使用](../media/keys-copy-api-key.png)

> [!IMPORTANT]
> 這些訂閱金鑰用於訪問您的認知服務 API。 不要共用您的金鑰。 使用 Azure 金鑰保存庫安全地存儲它們。 我們還建議定期重新生成這些金鑰。 進行 API 呼叫只需要一個金鑰。 重新生成第一個鍵時，可以使用第二個鍵繼續訪問服務。