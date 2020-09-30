---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: ede1fb4bd2a9a6e6536959053e3ca4d8e4a82f87
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91327340"
---
>[!NOTE]
> 在 2019 年 7 月 1 日之後建立的資源端點使用下面顯示的自訂子網域格式。 如需詳細資訊和完整的區域端點清單，請參閱[認知服務的自訂子網域名稱](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains)。 

Azure 認知服務會由您訂閱的 Azure 資源呈現。 使用 [Azure 入口網站](../../cognitive-services-apis-create-account.md)為筆跡辨識器建立資源。

建立資源之後，請透過在 [Azure 入口網站](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade)上開啟您的資源並按一下 [快速入門]，以取得您的端點與金鑰。

建立兩個[環境變數](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)：

* `INK_RECOGNITION_SUBSCRIPTION_KEY` - 用於驗證您要求的訂用帳戶金鑰。 

* `INK_RECOGNITION_ENDPOINT` - 您資源的端點。 它看起來像下面這樣： <br> `https://<your-custom-subdomain>.api.cognitive.microsoft.com`   
