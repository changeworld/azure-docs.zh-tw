---
title: Azure AD 身份驗證&國家雲 |蔚藍
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262993"
---
# <a name="national-clouds"></a>國家/地區雲端

國家雲是 Azure 的物理隔離實例。 這些 Azure 區域的設計目的，是要確保符合地理界限內的資料落地、主權及合規性需求。

包括全域雲，Azure 活動目錄 （Azure AD） 部署在以下國家雲中：  

- Azure Government
- Azure Germany
- Azure China 21Vianet

國家雲是唯一的，並且與 Azure 全域環境是一個單獨的環境。 在為這些環境開發應用程式時，瞭解關鍵差異非常重要。 差異包括註冊應用程式、獲取權杖和配置終結點。

## <a name="app-registration-endpoints"></a>應用程式註冊端點

每個國家雲都有一個單獨的 Azure 門戶。 要將應用程式與 Microsoft 標識平臺集成到國家雲中，您需要在每個特定于環境的 Azure 門戶中分別註冊應用程式。

下表列出了用於為每個國家雲註冊應用程式的 Azure AD 終結點的基本 URL。

| 國家雲端 | Azure AD 入口網站端點 |
|----------------|--------------------------|
| 適用於美國政府的 Azure AD | `https://portal.azure.us` |
| Azure AD Germany | `https://portal.microsoftazure.de` |
| 由 21Vianet 營運的 Azure AD China | `https://portal.azure.cn` |
| Azure AD (全球服務) |`https://portal.azure.com` |

## <a name="azure-ad-authentication-endpoints"></a>Azure AD 驗證端點

所有國家雲端都會分別在每個環境中驗證使用者，而且有不同的驗證端點。

下表列出了用於獲取每個國家/地區雲的權杖的 Azure AD 終結點的基本 URL。

| 國家雲端 | Azure AD 身份驗證終結點 |
|----------------|-------------------------|
| 適用於美國政府的 Azure AD | `https://login.microsoftonline.us` |
| Azure AD Germany| `https://login.microsoftonline.de` |
| 由 21Vianet 營運的 Azure AD China | `https://login.chinacloudapi.cn` |
| Azure AD (全球服務)| `https://login.microsoftonline.com` |

可以使用相應的特定于區域的基本 URL 對 Azure AD 授權或權杖終結點形成請求。 例如，對於 Azure Germany：

  - 授權通用端點為 `https://login.microsoftonline.de/common/oauth2/authorize`。
  - 權杖通用端點為 `https://login.microsoftonline.de/common/oauth2/token`。

對於單租戶應用程式，將以前 URL 中的"常見"替換為租戶 ID 或名稱。 例如 `https://login.microsoftonline.de/contoso.com`。

## <a name="microsoft-graph-api"></a>Microsoft Graph API

要瞭解如何在國家雲環境中調用 Microsoft 圖形 API，請轉到[國家雲部署中的 Microsoft 圖形](https://developer.microsoft.com/graph/docs/concepts/deployments)。

> [!IMPORTANT]
> 全球服務的特定區域中的某些服務和功能可能並非在所有國家雲中可用。 要瞭解哪些服務可用，請訪問[按區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast)。

要瞭解如何使用 Microsoft 標識平臺構建應用程式，請按照 Microsoft[身份驗證庫 （MSAL） 教程](msal-national-cloud.md)操作。 具體來說，此應用程式將登錄使用者並獲得訪問權杖來調用 Microsoft Graph API。

## <a name="next-steps"></a>後續步驟

深入了解：

- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure 中國 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure Germany](https://docs.microsoft.com/azure/germany/)
- [Azure AD 身份驗證基礎知識](authentication-scenarios.md)
