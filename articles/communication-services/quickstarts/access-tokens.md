---
title: 快速入門 - 建立和管理存取權杖
titleSuffix: An Azure Communication Services quickstart
description: 了解如何使用 Azure 通訊服務系統管理用戶端程式庫來管理身分識別和存取權杖。
author: tomaschladek
manager: nmurav
services: azure-communication-services
ms.author: tchladek
ms.date: 08/20/2020
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: b67d0808643797d88628b626403c1b9d97cf1cad
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94506221"
---
# <a name="quickstart-create-and-manage-access-tokens"></a>快速入門：建立和管理存取權杖

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

使用通訊服務系統管理用戶端程式庫，以開始使用 Azure 通訊服務。 此程式庫可讓您建立身分識別和管理您的存取權杖。 身分識別會在 Azure 通訊服務中代表您應用程式的實體 (例如，使用者或裝置)。 存取權杖可讓您的聊天和通話用戶端程式庫直接針對 Azure 通訊服務進行驗證。 建議您在伺服器端服務上產生存取權杖。 存取權杖隨後會用來初始化用戶端裝置上的通訊服務用戶端程式庫。

在本教學課程的影像中所見的任何價格，皆僅供示範之用。

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

Created an identity: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-19e0-2727-80f5-8b3a0d003502

Issued a access token with 'voip' scope for identity with ID: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-19e0-2727-80f5-8b3a0d003502:
<token signature here>

Successfully deleted the identity with ID: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-19e0-2727-80f5-8b3a0d003502

Deleted the identity with ID: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-19e0-2727-80f5-8b3a0d003502
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
 