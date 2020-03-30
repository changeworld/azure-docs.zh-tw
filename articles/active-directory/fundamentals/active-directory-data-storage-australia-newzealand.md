---
title: 澳大利亞客戶的標識資料存儲 - Azure AD
description: 瞭解 Azure 活動目錄在何處存儲其澳大利亞客戶的身份相關資料。
services: active-directory
author: msaburnley
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0dc659f3d580646c1e86e5e97940268311e5546f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460529"
---
# <a name="identity-data-storage-for-australian-and-new-zealand-customers-in-azure-active-directory"></a>Azure 活動目錄中為澳大利亞和紐西蘭客戶的標識資料存儲

標識資料由 Azure AD 存儲在地理位置中，具體取決於組織在訂閱 Microsoft 線上服務（如 Office 365 和 Azure）時提供的位址。 有關標識客戶資料的存儲位置的資訊，您可以使用 Microsoft 信任中心的"[資料位於何處"](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)部分。

> [!NOTE]
> 與 Azure AD 集成的服務和應用程式可以訪問標識客戶資料。 評估您用於確定特定服務和應用程式如何處理身份客戶資料的每個服務和應用程式，以及它們是否符合公司的資料存儲要求。 若想進一步了解 Microsoft 服務的資料存放處，請參閱 Microsoft 信任中心的 您的資料所在位置 區段。

對於在澳大利亞和紐西蘭提供位址並使用 Azure AD 免費版的客戶，Azure AD 會將 PII 資料保存在澳大利亞資料中心內。 

所有其他 Azure AD 高級服務將客戶資料存儲在全球資料中心中。 要查找服務的資料中心，請參閱[Azure 活動目錄 – 資料位於何處？](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>微軟 Azure 多重要素驗證 （MFA）

Azure AD 中的 MFA 服務將標識客戶資料存儲在全球資料中心中休息。 要瞭解有關基於雲的 Azure MFA 和 Azure MFA 伺服器收集和存儲的使用者資訊的詳細資訊，請參閱[Azure 多重要素驗證使用者資料收集](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-data-residency)。 如果客戶使用 MFA，其資料將存儲在澳大利亞資料中心之外。 

## <a name="next-steps"></a>後續步驟
如需前述任何特性和功能的詳細資訊，請參閱下列文章：
- [何謂 Multi-Factor Authentication？](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)
