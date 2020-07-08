---
title: Azure AD 驗證與國家雲端 | Azure
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
ms.custom: aaddev,references_regions
ms.openlocfilehash: 20a158aac6a03dfe0bd8929de9fa66ea215aa29c
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.contentlocale: zh-TW
ms.lasthandoff: 05/29/2020
ms.locfileid: "84194126"
---
# <a name="national-clouds"></a>國家雲端

國家雲端是實際隔離的 Azure 執行個體。 這些 Azure 區域的設計目的，是要確保符合地理界限內的資料落地、主權及合規性需求。

包含全球雲端在內，Active Directory (Azure AD) 會部署至下列國家雲端：  

- Azure Government
- Azure Germany
- Azure China 21Vianet

國家雲端是唯一的，且是獨立於 Azure 全球的環境。 針對這類環境開發應用程式時，請務必留意幾點關鍵差異， 包括註冊應用程式、取得權杖，以及設定端點。

## <a name="app-registration-endpoints"></a>應用程式註冊端點

每個國家雲端都有個別的 Azure 入口網站。 若要在國家雲端中整合應用程式與 Microsoft 身分識別平台，您必須分別在各個專屬該環境的 Azure 入口網站中註冊應用程式。

下表所列的是用於在各個國家雲端註冊應用程式的 Azure AD 端點基底 URL。

| 國家雲端 | Azure AD 入口網站端點 |
|----------------|--------------------------|
| 適用於美國政府的 Azure AD | `https://portal.azure.us` |
| Azure AD Germany | `https://portal.microsoftazure.de` |
| 由 21Vianet 營運的 Azure AD China | `https://portal.azure.cn` |
| Azure AD (全球服務) |`https://portal.azure.com` |

## <a name="azure-ad-authentication-endpoints"></a>Azure AD 驗證端點

所有國家雲端都會分別在每個環境中驗證使用者，而且有不同的驗證端點。

下表所列的是 Azure AD 端點基底 URL，可用於取得各個國家雲端的權杖。

| 國家雲端 | Azure AD 驗證端點 |
|----------------|-------------------------|
| 適用於美國政府的 Azure AD | `https://login.microsoftonline.us` |
| Azure AD Germany| `https://login.microsoftonline.de` |
| 由 21Vianet 營運的 Azure AD China | `https://login.chinacloudapi.cn` |
| Azure AD (全球服務)| `https://login.microsoftonline.com` |

您可以使用合適的區域專屬基底 URL，形成對 Azure AD 授權或權杖端點的要求。 例如，對於 Azure Germany：

  - 授權通用端點為 `https://login.microsoftonline.de/common/oauth2/v2.0/authorize`。
  - 權杖通用端點為 `https://login.microsoftonline.de/common/oauth2/v2.0/token`。

對於單一租用戶應用程式，請將先前 URL 的 common 取代為租用戶識別碼或名稱。 例如 `https://login.microsoftonline.de/contoso.com`。

## <a name="microsoft-graph-api"></a>Microsoft Graph API

若要了解如何在國家雲端環境中呼叫 Microsoft Graph API，請移至[國家雲端部署中的 Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/deployments) (英文)。

> [!IMPORTANT]
> 某些全球服務特定區域中的服務和功能，可能無法在全部國家雲端中提供。 若要了解提供的服務有哪些，請移至[依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast)。

若要了解如何使用 Microsoft 身分識別平台建立應用程式，請遵循 [Microsoft 驗證程式庫 (MSAL) 教學課程](msal-national-cloud.md)的说明。 具體來說，此應用程式會讓使用者登入，並取得存取權杖來呼叫 Microsoft Graph API。

## <a name="next-steps"></a>後續步驟

深入了解：

- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure China 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure Germany](https://docs.microsoft.com/azure/germany/)
- [Azure AD 驗證基本資訊](authentication-scenarios.md)
