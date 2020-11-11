---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: c68e5b7ab24e2d7e7f30ddc356ae3c4382137507
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369067"
---
>[!NOTE]
> 在 2019 年 7 月 1 日之後建立的資源端點使用下面顯示的自訂子網域格式。 如需詳細資訊和完整的區域端點清單，請參閱[認知服務的自訂子網域名稱](../../cognitive-services-custom-subdomains.md)。 

Azure 認知服務會由您訂閱的 Azure 資源呈現。 使用 [Azure 入口網站](../../cognitive-services-apis-create-account.md)為筆跡辨識器建立資源。

建立資源之後，請透過在 [Azure 入口網站](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade)上開啟您的資源並按一下 [快速入門]，以取得您的端點與金鑰。

建立兩個[環境變數](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)：

* `INK_RECOGNITION_SUBSCRIPTION_KEY` - 用於驗證您要求的訂用帳戶金鑰。 

* `INK_RECOGNITION_ENDPOINT` - 您資源的端點。 它看起來像下面這樣： <br> `https://<your-custom-subdomain>.api.cognitive.microsoft.com`