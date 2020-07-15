---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 54ee19f3e278b424384ff55d7065713584235df1
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86144217"
---
## <a name="set-up"></a>設定

### <a name="create-a-translator-resource"></a>建立翻譯工具資源

Azure 認知服務會由您訂閱的 Azure 資源呈現。 請使用 [Azure 入口網站](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)或 [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) 在本機電腦上建立翻譯工具的資源。 您也可以：

* 在 [Azure 入口網站](https://portal.azure.com/)中檢視現有資源。

從試用版訂用帳戶或資源取得金鑰後，請建立兩個[環境變數](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)：

* `TRANSLATOR_TEXT_SUBSCRIPTION_KEY` - 翻譯工具資源的訂用帳戶金鑰。
* `TRANSLATOR_TEXT_ENDPOINT` - 翻譯工具的全域端點。 使用 `https://api.cognitive.microsofttranslator.com/`。
