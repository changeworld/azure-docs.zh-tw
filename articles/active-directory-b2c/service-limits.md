---
title: Azure AD B2C 服務限制和限制
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C 服務的服務限制和限制的參考。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/29/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b980624ebf55c3666bcc7dd1d60f681d788c95ae
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98987038"
---
# <a name="azure-active-directory-b2c-service-limits-and-restrictions"></a>Azure Active Directory B2C 服務限制和限制

本文包含 Azure Active Directory B2C (Azure AD B2C) 服務的使用條件約束和其他服務限制。

## <a name="end-userconsumption-related-limits"></a>終端使用者/耗用量的相關限制

下列終端使用者相關的服務限制適用于 Azure AD B2C 所支援的所有驗證和授權通訊協定，包括 SAML、Open ID Connect、OAuth2 和 ROPC。

|類別 |限制    |
|---------|---------|
|每個 Azure AD B2C 租使用者之每個 IP 位址的要求數目       |6000/5 分鐘          |
|每個 Azure AD B2C 租使用者的要求總數     |12000/分鐘          |

要求的數目可能會根據 Azure AD B2C 使用者旅程期間發生的目錄讀取和寫入次數而有所不同。 例如，從目錄中讀取的簡單登入旅程包含1個要求。 如果登入旅程也必須更新目錄，這項作業會被視為額外的要求。

## <a name="azure-ad-b2c-configuration-limits"></a>Azure AD B2C 設定限制

下表列出 Azure AD B2C 服務中的系統管理設定限制。

|類別  |限制  |
|---------|---------|
|每個 Azure AD B2C 租使用者的應用程式數目   |250           |
|每個 Azure AD B2C 租使用者的原則數目       |200          |
|原則檔案大小上限      |400 KB          |
|每個應用程式的範圍數目        |1000          |
|每位使用者<sup>1</sup>的自訂屬性數目       |100         |
|每個應用程式的重新導向 Url 數目       |100         |
|每個應用程式的登出 Url 數目        |1          |
|每個屬性的字串限制      |250個字元          |
|每個訂用帳戶的 B2C 租使用者數目      |20         |
|自訂原則中的 [繼承](custom-policy-overview.md#inheritance-model) 層級     |10         |
|每個 Azure AD B2C 租使用者的原則數目      |200         |
|原則檔案大小上限      |400 KB         |

<sup>1</sup> 另請參閱 [Azure AD 服務限制和限制](../active-directory/enterprise-users/directory-service-limits-restrictions.md)。

## <a name="next-steps"></a>下一步

- 瞭解[Microsoft Graph 的節流指導](/graph/throttling.md)方針 
- 瞭解 [Azure AD B2C 應用程式的驗證差異](../active-directory/develop/supported-accounts-validation.md)













