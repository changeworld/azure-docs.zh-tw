---
title: 快速入門 - 建立和管理存取權杖
titleSuffix: An Azure Communication Services quickstart
description: 了解如何使用 Azure 通訊服務系統管理用戶端程式庫來管理身分識別和存取權杖。
author: matthewrobertson
manager: jken
services: azure-communication-services
ms.author: marobert
ms.date: 08/20/2020
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: a76000ecacdf78196ec1b80a60940484f6421641
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "90943884"
---
# <a name="quickstart-create-and-manage-access-tokens"></a>快速入門：建立和管理存取權杖

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

使用通訊服務系統管理用戶端程式庫來佈建和管理您的存取權杖，以開始使用 Azure 通訊服務。 存取權杖可讓您的聊天和通話用戶端程式庫直接針對 Azure 通訊服務進行驗證。 這些權杖會在您實作的伺服器端權杖佈建服務上產生。 然後，會將其用來初始化用戶端裝置上的通訊服務用戶端程式庫。

請注意，在本教學課程影像中所見的任何價格僅供範例之用。

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/user-access-token-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/user-access-token-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/user-access-token-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/user-access-token-java.md)]
::: zone-end

應用程式的輸出會描述已完成的每個動作：
<!---cSpell:disable --->
```console
Azure Communication Services - Access Tokens Quickstart

Issued a access token with 'voip' scope for identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050:
<token signature here>

Issued a access token with 'chat' scope for identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050:
<token signature here>

Successfully deleted the identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050

Deleted the identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050
```
<!---cSpell:enable --->

## <a name="clean-up-resources"></a>清除資源

如果您想要清除和移除通訊服務訂閱，則可以刪除資源或資源群組。 刪除資源群組也會刪除其關聯的任何其他資源。 深入了解如何[清除資源](./create-communication-resource.md#clean-up-resources)。


## <a name="next-steps"></a>後續步驟

在此快速入門中，您已了解如何：

> [!div class="checklist"]
> * 管理身分識別
> * 發行存取權杖
> * 使用通訊服務系統管理用戶端程式庫


> [!div class="nextstepaction"]
> [將語音電話新增至您的應用程式](./voice-video-calling/getting-started-with-calling.md)

您可能也會想要：

 - [了解驗證](../concepts/authentication.md)
 - [將聊天新增至您的應用程式](./chat/get-started.md)
 - [了解用戶端與根伺服器架構](../concepts/client-and-server-architecture.md)
