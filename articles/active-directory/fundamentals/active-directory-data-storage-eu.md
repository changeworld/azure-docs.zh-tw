---
title: 歐洲客戶的標識資料存儲 - Azure AD
description: 了解 Azure Active Directory 將其歐洲客戶的身分識別相關資料儲存於何處。
services: active-directory
author: msaburnley
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 03/04/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7360b11f41cc08c2beb3ffa227e1658881798502
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422991"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Azure Active Directory 中歐洲客戶的身分識別資料儲存體
標識資料由 Azure AD 存儲在地理位置中，具體取決於組織在訂閱 Microsoft 線上服務（如 Office 365 和 Azure）時提供的位址。 有關標識資料存儲位置的資訊，您可以使用 Microsoft 信任中心的"[資料位於何處"](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)部分。

對於在歐洲提供位址的客戶，Azure AD 將大部分標識資料保存在歐洲資料中心內。 本文檔提供有關 Azure AD 服務在歐洲境外存儲的任何資料的資訊。

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>微軟 Azure 多重要素驗證 （MFA）
    
- 所有使用電話或 SMS 的雙重身份驗證都源自美國資料中心，並且也由全球供應商路由。
- 使用 Microsoft 身份驗證器應用的推送通知源自美國資料中心。 此外，設備供應商特定的服務也可能發揮作用，這些服務可能在歐洲以外。
- OATH 代碼一律會在美國進行驗證。 

有關 Azure 多重要素驗證伺服器 （MFA 伺服器） 和基於雲的 Azure MFA 收集的使用者資訊的詳細資訊，請參閱[Azure 多重要素驗證使用者資料收集](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-reporting-datacollection)。

## <a name="microsoft-azure-active-directory-b2c-azure-ad-b2c"></a>Microsoft Azure Active Directory B2C (Azure AD B2C)

Azure AD B2C 策略配置資料和金鑰容器存儲在美國資料中心中。 這些不包含任何使用者個人資料。 如需原則組態的詳細資訊，請參閱 [Azure Active Directory B2C：內建原則](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies)一文。

## <a name="microsoft-azure-active-directory-b2b-azure-ad-b2b"></a>Microsoft Azure Active Directory B2B (Azure AD B2B) 
    
Azure AD B2B 存儲具有兌換連結的邀請，並重定向美國資料中心中的 URL 資訊。 此外，取消訂閱接收 B2B 邀請的使用者的電子郵件地址也存儲在美國資料中心中。

## <a name="microsoft-azure-active-directory-domain-services-azure-ad-ds"></a>Microsoft Azure Active Directory Domain Services (Azure AD DS)

Azure AD DS 會將使用者資料儲存在與客戶選取的 Azure 虛擬網路相同的位置。 因此，如果網路不在歐洲境內，則資料會複寫並儲存在歐洲以外。

## <a name="federation-in-microsoft-exchange-server-2013"></a>微軟交換伺服器 2013 中的聯合
    
- 應用程式識別碼 （AppID） - Azure 活動目錄身份驗證系統為標識 Exchange 組織而生成的唯一編號。
- 已批准的已聯合域清單
- 應用程式的權杖簽名公開金鑰 

有關 Microsoft Exchange 伺服器中聯合的詳細資訊，請參閱[聯合：交換 2013 説明](https://docs.microsoft.com/exchange/federation-exchange-2013-help)文章。


## <a name="other-considerations"></a>其他考量

與 Azure AD 集成的服務和應用程式可以訪問標識資料。 評估您用於確定特定服務和應用程式如何處理標識資料的每個服務和應用程式，以及它們是否符合公司的資料存儲要求。

若想進一步了解 Microsoft 服務的資料存放處，請參閱 Microsoft 信任中心的 [您的資料所在位置](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) 區段。

## <a name="next-steps"></a>後續步驟
如需前述任何特性和功能的詳細資訊，請參閱下列文章：
- [何謂 Multi-Factor Authentication？](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)

- [Azure AD 自助服務密碼重設](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-overview)

- [什麼是 Azure Active Directory B2C？](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)

- [何謂 Azure AD B2B 共同作業？](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)

- [Azure Active Directory (AD) Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview)
