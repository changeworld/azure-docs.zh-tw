---
title: '& 國家雲端 Azure AD 驗證 |Azure'
titleSuffix: Microsoft identity platform
description: 了解國家雲端的應用程式註冊和驗證端點。
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: negoe
ms.reviewer: negoe,celested
ms.custom: aaddev
ms.openlocfilehash: 20a053369149dc29d6485c49bb091a75bb9fb591
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78377189"
---
# <a name="national-clouds"></a>國家雲端

國家雲端是實體獨立的 Azure 實例。 這些 Azure 區域的設計目的，是要確保符合地理界限內的資料落地、主權及合規性需求。

包含全域雲端，Azure Active Directory （Azure AD）會部署在下列國家/地區雲端中：  

- Azure Government
- Azure Germany
- Azure China 21Vianet

國家/地區雲端是唯一的，也是 Azure global 的不同環境。 針對這些環境開發應用程式時，請務必留意主要差異。 差異包括註冊應用程式、取得權杖，以及設定端點。

## <a name="app-registration-endpoints"></a>應用程式註冊端點

每一個國家雲端都有個別的 Azure 入口網站。 若要在國家雲端整合應用程式與 Microsoft 身分識別平臺，您必須在每個特定于環境的 Azure 入口網站中，分別註冊您的應用程式。

下表列出用來為每個國家雲端註冊應用程式之 Azure AD 端點的基底 Url。

| 國家雲端 | Azure AD 入口網站端點 |
|----------------|--------------------------|
| 適用於美國政府的 Azure AD | `https://portal.azure.us` |
| Azure AD Germany | `https://portal.microsoftazure.de` |
| 由 21Vianet 營運的 Azure AD China | `https://portal.azure.cn` |
| Azure AD (全球服務) |`https://portal.azure.com` |

## <a name="azure-ad-authentication-endpoints"></a>Azure AD 驗證端點

所有國家雲端都會分別在每個環境中驗證使用者，而且有不同的驗證端點。

下表列出用來取得每個國家雲端權杖之 Azure AD 端點的基底 Url。

| 國家雲端 | Azure AD 驗證端點 |
|----------------|-------------------------|
| 適用於美國政府的 Azure AD | `https://login.microsoftonline.us` |
| Azure AD Germany| `https://login.microsoftonline.de` |
| 由 21Vianet 營運的 Azure AD China | `https://login.chinacloudapi.cn` |
| Azure AD (全球服務)| `https://login.microsoftonline.com` |

您可以使用適當的區域特定基底 URL，以 Azure AD 授權或權杖端點來形成要求。 例如，對於 Azure Germany：

  - 授權通用端點為 `https://login.microsoftonline.de/common/oauth2/authorize`。
  - 權杖通用端點為 `https://login.microsoftonline.de/common/oauth2/token`。

對於單一租使用者應用程式，請將先前 Url 中的「通用」取代為您的租使用者識別碼或名稱。 例如 `https://login.microsoftonline.de/contoso.com`。

## <a name="microsoft-graph-api"></a>Microsoft Graph API

若要瞭解如何在國家雲端環境中呼叫 Microsoft Graph Api，請移至[國家雲端部署](https://developer.microsoft.com/graph/docs/concepts/deployments)中的 Microsoft Graph。

> [!IMPORTANT]
> 全域服務特定區域中的某些服務和功能可能無法在所有國家雲端中使用。 若要瞭解有哪些服務可供使用，請移至[依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast)。

若要瞭解如何使用 Microsoft 身分識別平臺來建立應用程式，請遵循[Microsoft 驗證程式庫（MSAL）教學](msal-national-cloud.md)課程。 具體而言，此應用程式將會登入使用者，並取得存取權杖來呼叫 Microsoft Graph API。

## <a name="next-steps"></a>後續步驟

深入了解：

- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure China 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure Germany](https://docs.microsoft.com/azure/germany/)
- [Azure AD 驗證基本概念](authentication-scenarios.md)
