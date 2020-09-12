---
title: 適用于歐洲客戶的身分識別資料儲存體-Azure AD
description: 了解 Azure Active Directory 將其歐洲客戶的身分識別相關資料儲存於何處。
services: active-directory
author: ajburnle
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 03/04/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 874df2cc17e291cb6811ca07d01237f01b523860
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89565033"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Azure Active Directory 中歐洲客戶的身分識別資料儲存體
在訂閱 Microsoft 線上服務（例如 Microsoft 365 和 Azure）時，會根據組織提供的位址，在地理位置 Azure AD 儲存身分識別資料。 如需識別資料儲存位置的資訊，您可以使用 Microsoft 信任中心的 [ [資料的所在位置？](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) ] 區段。

針對在歐洲提供位址的客戶，Azure AD 在歐洲資料中心內保存大部分的身分識別資料。 本檔提供 Azure AD services 儲存在歐洲以外的任何資料的相關資訊。

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Microsoft Azure MFA) 的多重要素驗證 (
    
- 所有使用電話或 SMS 的雙因素驗證都是來自美國資料中心，也是由全域提供者路由傳送。
- 使用 Microsoft Authenticator 應用程式的推播通知源自美國資料中心。 此外，裝置廠商的特定服務也可能會在歐洲以外的地方推出。
- OATH 代碼一律會在美國進行驗證。 

如需 Azure Multi-Factor Authentication Server (MFA Server) 和雲端式 Azure MFA 收集哪些使用者資訊的詳細資訊，請參閱 [azure Multi-Factor Authentication 使用者資料收集](../authentication/howto-mfa-reporting-datacollection.md)。

## <a name="microsoft-azure-active-directory-b2c-azure-ad-b2c"></a>Microsoft Azure Active Directory B2C (Azure AD B2C)

Azure AD B2C 原則設定資料和金鑰容器會儲存在美國資料中心。 這些不包含任何使用者的個人資料。 如需原則組態的詳細資訊，請參閱 [Azure Active Directory B2C：內建原則](../../active-directory-b2c/user-flow-overview.md)一文。

## <a name="microsoft-azure-active-directory-b2b-azure-ad-b2b"></a>Microsoft Azure Active Directory B2B (Azure AD B2B) 
    
Azure AD B2B 會在美國資料中心內使用兌換連結和重新導向 URL 資訊來儲存邀請。 此外，取消訂閱接收 B2B 邀請之使用者的電子郵件地址也會儲存在美國資料中心。

## <a name="microsoft-azure-active-directory-domain-services-azure-ad-ds"></a>Microsoft Azure Active Directory Domain Services (Azure AD DS)

Azure AD DS 會將使用者資料儲存在與客戶選取的 Azure 虛擬網路相同的位置。 因此，如果網路不在歐洲境內，則資料會複寫並儲存在歐洲以外。

## <a name="federation-in-microsoft-exchange-server-2013"></a>Microsoft Exchange Server 2013 中的同盟
    
- 應用程式識別碼 (AppID) -Azure Active Directory authentication 系統產生的唯一數位，以識別 Exchange 組織。
- 應用程式的已核准同盟網域清單
- 應用程式的權杖簽署公開金鑰 

如需有關 Microsoft Exchange server 同盟的詳細資訊，請參閱 [同盟： Exchange 2013](/exchange/federation-exchange-2013-help) 說明文章。


## <a name="other-considerations"></a>其他考量

與 Azure AD 整合的服務和應用程式可以存取身分識別資料。 評估您用來決定該特定服務和應用程式如何處理身分識別資料的每個服務和應用程式，以及它們是否符合您公司的資料儲存需求。

若想進一步了解 Microsoft 服務的資料存放處，請參閱 Microsoft 信任中心的 [您的資料所在位置](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) 區段。

## <a name="next-steps"></a>接下來的步驟
如需前述任何特性和功能的詳細資訊，請參閱下列文章：
- [何謂 Multi-Factor Authentication？](../authentication/concept-mfa-howitworks.md)

- [Azure AD 自助式密碼重設](../authentication/concept-sspr-howitworks.md)

- [什麼是 Azure Active Directory B2C？](../../active-directory-b2c/overview.md)

- [何謂 Azure AD B2B 共同作業？](../external-identities/what-is-b2b.md)

- [Azure Active Directory (AD) Domain Services](../../active-directory-domain-services/overview.md)