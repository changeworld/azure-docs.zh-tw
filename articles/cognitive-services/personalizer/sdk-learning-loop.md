---
title: 快速入門：使用 SDK 建立和使用學習迴圈 - 個人化工具
description: 本快速入門將說明如何使用用戶端 SDK 來建立和管理您的知識庫。
ms.topic: quickstart
ms.date: 01/15/2020
zone_pivot_groups: programming-languages-set-six
ms.openlocfilehash: e09476bc084465cf08087a3200d8b7d663b0685e
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122819"
---
# <a name="quickstart-personalizer-client-library"></a>快速入門：個人化工具用戶端程式庫

使用個人化工具服務顯示此 Python 快速入門中的個人化內容。

開始使用個人化工具用戶端程式庫。 請遵循下列步驟來安裝套件，並試用基本工作的程式碼範例。

 * 排名 API - 根據您就內容和關係提供的相關即時資訊，從內容項目中選取最佳項目。
 * 獎勵 API - 您可以根據您的業務需求決定獎勵分數，然後使用此 API 將其傳送到個人化工具。 該分數可以是單一值，例如 1 表示良好、0 表示不良，或根據您的業務需求建立的演算法。

::: zone pivot="programming-language-csharp"
[!INCLUDE [Get intent with C# SDK](./includes/quickstart-sdk-csharp.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Get intent with Python SDK](./includes/quickstart-sdk-python.md)]
::: zone-end

::: zone pivot="programming-language-nodejs"
[!INCLUDE [Get intent with Node.js SDK](./includes/quickstart-sdk-nodejs.md)]
::: zone-end

## <a name="clean-up-resources"></a>清除資源

如果您想要清除和移除認知服務訂用帳戶，則可以刪除資源或資源群組。 刪除資源群組也會刪除其關聯的任何其他資源。

* [入口網站](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
>[個人化工具的運作方式](how-personalizer-works.md)

* [什麼是個人化工具？](what-is-personalizer.md)
* [個人化工具可以應用在何處？](where-can-you-use-personalizer.md)
* [疑難排解](troubleshooting.md)
* 此範例的原始程式碼可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py) 上找到。
