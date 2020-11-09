---
title: 快速入門：建立第一個 Bing 自訂搜尋執行個體
titleSuffix: Azure Cognitive Services
description: 使用本快速入門來建立可搜尋您所定義網域和網頁的自訂 Bing 執行個體。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: 313b216fd3e279e473790f7b82e17243b6df6846
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100009"
---
# <a name="quickstart-create-your-first-bing-custom-search-instance"></a>快速入門：建立您的第一個 Bing 自訂搜尋執行個體

> [!WARNING]
> Bing 搜尋 API 將從認知服務移至 Bing 搜尋服務。 從 **2020 年 10 月 30 日** 開始，所有 Bing 搜尋的新執行個體都必須依照[這裡](https://aka.ms/cogsvcs/bingmove)所述的程序進行佈建。
> 使用認知服務佈建的 Bing 搜尋 API 將在未來三年受到支援，或支援到您的 Enterprise 合約結束為止 (視何者先發生)。
> 如需移轉指示，請參閱 [Bing 搜尋服務](https://aka.ms/cogsvcs/bingmigration)。

若要使用 Bing 自訂搜尋，必須建立可定義檢視或網路配量的自訂搜尋執行個體。 此執行個體包含您要搜尋的公用網域、網站和網頁，以及您可能需要的任何順位調整。 

若要建立執行個體，請使用 [Bing 自訂搜尋入口網站](https://customsearch.ai)。 

![Bing 自訂搜尋入口網站的圖片](media/blockedCustomSrch.png)

## <a name="prerequisites"></a>必要條件

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-a-custom-search-instance"></a>建立自訂搜尋執行個體

建立 Bing 自訂搜尋執行個體：

1. 按一下 [Bing 自訂搜尋入口網站](https://customsearch.ai)網頁上的 [開始使用]，並使用您的 Microsoft 帳戶登入。

2. 按一下 [新執行個體]，然後輸入描述性名稱。 您可以隨時變更執行個體的名稱。
 
3. 在 [搜尋體驗] 的 [使用中] 索引標籤下，輸入您要在搜尋中包括的一或多個網站的 URL。 

    > [!NOTE]
    > Bing 自訂搜尋執行個體只會傳回公用且經過 Bing 編製索引之網域和網頁的結果。

4. 您可以使用 Bing 自訂搜尋入口網站的右側輸入查詢，並檢查搜尋執行個體所傳回的搜尋結果。 如果未傳回任何結果，請嘗試輸入其他 URL。  

5. 按一下 [發佈]，將變更發佈至生產環境中，並更新執行個體的端點。

6.  按一下 [端點] 下方的 [生產] 索引標籤，複製您的 **自訂組態識別碼** 。 您需要將此識別碼附加到呼叫中的 `customconfig=` 查詢參數，藉此呼叫自訂搜尋 API。


## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [快速入門：呼叫 Bing 自訂搜尋端點](./call-endpoint-csharp.md)
