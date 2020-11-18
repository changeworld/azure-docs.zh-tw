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
ms.date: 09/15/2020
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a8b013723707c4a3a087a90674227c3d41c5108
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94836932"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Azure Active Directory 中歐洲客戶的身分識別資料儲存體
在訂閱 Microsoft 線上服務（例如 Microsoft 365 和 Azure）時，會根據組織提供的位址，在地理位置 Azure AD 儲存身分識別資料。 如需識別資料儲存位置的資訊，您可以使用 Microsoft 信任中心的 [ [資料的所在位置？](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) ] 區段。

針對在歐洲提供位址的客戶，Azure AD 在歐洲資料中心內保存大部分的身分識別資料。 本檔提供 Azure AD services 儲存在歐洲以外的任何資料的相關資訊。

## <a name="microsoft-azure-ad-multi-factor-authentication"></a>Microsoft Azure AD Multi-Factor Authentication

針對雲端式 Azure AD Multi-Factor Authentication，在最接近使用者的資料中心內完成驗證。 Azure AD Multi-Factor Authentication 的資料中心存在於北美洲、歐洲和亞太地區。

* 使用電話的多重要素驗證是來自美國資料中心，由全域提供者路由傳送。
* 使用 SMS 的多重要素驗證是由全域提供者所路由傳送。
* 使用來自 EU 資料中心的 Microsoft Authenticator 應用程式推播通知的多重要素驗證要求，會在歐盟資料中心處理。
    * 裝置廠商專屬的服務，例如 Apple Push 通知，可能在歐洲以外。
* 使用來自 EU 資料中心之 OATH 代碼的多重要素驗證要求，會在歐盟進行驗證。

如需 Azure Multi-Factor Authentication Server (MFA Server) 和雲端式 Azure AD MFA 收集哪些使用者資訊的詳細資訊，請參閱 [azure Multi-Factor Authentication 使用者資料收集](../authentication/howto-mfa-reporting-datacollection.md)。

## <a name="password-based-single-sign-on-for-enterprise-applications"></a>企業應用程式以密碼為基礎的單一 Sign-On
 
如果客戶建立新的企業應用程式 (Azure AD 資源庫或非資源庫) ，並啟用密碼型 SSO，則應用程式登入 URL 和自訂捕獲登入欄位會儲存在美國。 如需這項功能的詳細資訊，請參閱 [設定密碼型單一登入](../manage-apps/configure-password-single-sign-on-non-gallery-applications.md)

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

## <a name="next-steps"></a>後續步驟
如需前述任何特性和功能的詳細資訊，請參閱下列文章：
- [何謂 Multi-Factor Authentication？](../authentication/concept-mfa-howitworks.md)

- [Azure AD 自助式密碼重設](../authentication/concept-sspr-howitworks.md)

- [什麼是 Azure Active Directory B2C？](../../active-directory-b2c/overview.md)

- [何謂 Azure AD B2B 共同作業？](../external-identities/what-is-b2b.md)

- [Azure Active Directory (AD) Domain Services](../../active-directory-domain-services/overview.md)