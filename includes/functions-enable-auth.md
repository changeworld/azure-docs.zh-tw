---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/21/2020
ms.author: glenga
ms.openlocfilehash: 380878fedaa2f7ea6160c3c4801c8dece6e1a9ff
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648933"
---
#### <a name="enable-app-service-authenticationauthorization"></a>啟用 App Service 驗證/授權

App Service 平台可讓您使用 Azure Active Directory (AAD) 及數個協力廠商身分識別提供者來驗證用戶端。 您可以使用此策略為函式實作自訂授權規則，並可使用來自函式程式碼的使用者資訊。 若要深入了解，請參閱 [Azure App Service 中的驗證與授權](../articles/app-service/overview-authentication-authorization.md)和[使用用戶端身分識別](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#working-with-client-identities)。

#### <a name="use-azure-api-management-apim-to-authenticate-requests"></a>使用「Azure API 管理」(APIM) 來驗證要求

APIM 為傳入要求提供多種 API 安全性選項。 若要深入了解，請參閱 [API 管理驗證原則](../articles/api-management/api-management-authentication-policies.md)。 備妥 APIM 之後，您可以設定讓函數應用程式只接受來自您 APIM 執行個體 IP 位址的要求。 若要深入了解，請參閱 [IP 位址限制](../articles/azure-functions/ip-addresses.md#ip-address-restrictions)。
