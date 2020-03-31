---
title: 使用策略部署託管應用程式的關聯
description: 瞭解如何使用 Azure 策略服務部署託管應用程式的關聯。
author: msHich
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: hich
ms.openlocfilehash: ec0fe8f66ef2ad2458b4ffad0e848591793e5b05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650938"
---
# <a name="deploy-associations-for-a-managed-application-using-azure-policy"></a>使用 Azure 策略部署託管應用程式的關聯

Azure 策略可用於部署關聯以將資源關聯到託管應用程式。 在本文中，我們將介紹部署關聯的內置策略以及如何使用該策略。

## <a name="built-in-policy-to-deploy-associations"></a>部署關聯的內置策略

為託管應用程式部署關聯是一種內置策略，可用於部署關聯以將資源關聯到託管應用程式。 該策略接受三個參數：

- 託管應用程式 ID - 此 ID 是需要關聯資源的託管應用程式的資源識別碼。
- 要關聯的資源類型 - 這些資源類型是要與託管應用程式關聯的資源類型的清單。 您可以使用同一策略將多個資源類型關聯到託管應用程式。
- 關聯名稱首碼 - 此字串是要添加到要創建的關聯資源的名稱的首碼。 預設值為"部署策略"。

該策略使用 DeployIfNot 存在評估。 它在資來源提供者處理所選資源類型的創建或更新資源請求後運行，並且評估已返回成功狀態碼。 之後，使用範本部署部署關聯資源。
有關關聯的詳細資訊，請參閱[Azure 自訂提供程式資源載入](../custom-providers/concepts-resource-onboarding.md)

## <a name="how-to-use-the-deploy-associations-built-in-policy"></a>如何使用部署關聯內置策略 

### <a name="prerequisites"></a>Prerequisites
如果託管應用程式需要訂閱的許可權才能執行操作，則不授予許可權，關聯資源的策略部署將無法工作。

### <a name="policy-assignment"></a>原則指派
要使用內置策略，請創建策略分配並為託管應用程式策略分配部署關聯。 成功分配策略後，策略將標識不合規資源並部署這些資源的關聯。

![分配內置策略](media/concepts-built-in-policy/assign-builtin-policy-managedapp.png)

## <a name="getting-help"></a>取得說明

如果您對 Azure 自訂資來源提供者開發有疑問，請嘗試在[堆疊溢位](https://stackoverflow.com/questions/tagged/azure-custom-providers)上詢問它們。 類似的問題可能已有人回答，因此請先查看再張貼問題。 新增標記 ```azure-custom-providers``` 以取得快速回應！

## <a name="next-steps"></a>後續步驟

在本文中，您瞭解了如何使用內置策略來部署關聯。 請參閱以下文章以瞭解更多資訊：

- [概念：Azure 自訂提供程式資源載入](../custom-providers/concepts-resource-onboarding.md)
- [教程：使用自訂提供程式進行資源載入](../custom-providers/tutorial-resource-onboarding.md)
- [教程：在 Azure 中創建自訂操作和資源](../custom-providers/tutorial-get-started-with-custom-providers.md)
- [快速入門：創建自訂資來源提供者並部署自訂資源](../custom-providers/create-custom-provider.md)
- [如何：將自訂操作添加到 Azure REST API](../custom-providers/custom-providers-action-endpoint-how-to.md)
- [如何：將自訂資源添加到 Azure REST API](../custom-providers/custom-providers-resources-endpoint-how-to.md)
