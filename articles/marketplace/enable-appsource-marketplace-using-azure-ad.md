---
title: 整合您的 Microsoft 商業市集供應項目與 Azure Active Directory
description: 使用 Azure Active Directory 來驗證您的 Microsoft AppSource 和 Azure Marketplace 供應項目。
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: 17cbfe92744ad96f2b5651b7e2f47a6443337068
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83658037"
---
# <a name="integrate-your-commercial-marketplace-listing-with-azure-active-directory"></a>整合您的商業市集清單與 Azure Active Directory

 本文說明整合商業市集清單或供應項目與 Azure Active Directory (Azure AD) 的需求。 Azure AD 是一種雲端身分識別服務，採用業界標準架構來啟用 Microsoft 帳戶的驗證。 [深入了解 Azure Active Directory](https://azure.microsoft.com/services/active-directory)。

## <a name="azure-ad-benefits"></a>Azure AD 的優點

Microsoft AppSource 和 Azure Marketplace 客戶會使用產品內體驗來搜尋店面清單目錄。 這些動作需要客戶登入產品。 Azure DNS 整合提供下列優點：

- 更快速的業務開發和最佳化客戶體驗
- 數百萬企業使用者適用的單一登入 (SSO)
- 在不同合作夥伴發佈的各種應用程式間提供一致的使用者登入體驗
- 行動和雲端應用程式適用的可調式跨平台驗證

## <a name="offers-that-require-azure-ad"></a>要求 Azure AD 的供應項目

各種商業市集[清單選項和供應項目類型](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type)對於 Azure AD 實作有不同的需求。 如需詳細資訊，請參閱下表。

| **供應項目類型**    | **是否需要 Azure AD SSO？**  |  |   |  |
| :------------------- | :-------------------|:-------------------|:-------------------|:-------------------|
|  | 與我連絡 | 試用版 | 試用產品 | 交易 |
| 虛擬機器 | N/A | 否 | 否 | 否 |
| Azure 應用程式 (解決方案範本)  | N/A | N/A | N/A | N/A |
| 受管理的應用程式  | N/A | N/A | N/A | 否 |
| SaaS  | 否 | 是 | 是 | 是 |
| 容器  | N/A | N/A | N/A | 否 |
| 諮詢服務  | 否 | N/A | N/A | N/A |

如需有關 Saas 技術需求的詳細資訊，請參閱 [SaaS 應用程式供應項目發佈指南](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide)。

## <a name="azure-ad-integration"></a>Azure AD 整合

- 如需如何將 Azure AD 整合到您的清單以啟用單一登入的相關資訊，請參閱[開發人員適用的 Azure Active Directory]( https://docs.microsoft.com/azure/active-directory/develop/)。
- 如需關於 Azure AD 單一登入的詳細資料，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="enable-a-trial-listing"></a>啟用試用版清單

將客戶設定自動化可以增加轉換的可能性。 當您的客戶選取您的試用版清單並重新導向至您的試用版環境時，您可以直接設定客戶，而無須進行額外的登入步驟。

在驗證期間，Azure AD 會將權杖傳送至您的應用程式或供應項目。 權杖所提供的使用者資訊可讓您在應用程式或供應項目中建立使用者帳戶。 若要進一步了解，請參閱[範例權杖](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)。

當您使用 Azure AD 在應用程式或試用版清單中啟用單鍵驗證時，您可以：

- 簡化客戶從 Marketplace 到試用版清單的體驗。
- 即使將使用者從 Marketplace 重新導向到您的網域或試用版環境，仍讓使用者感覺是在產品內部。
- 由於無須進行任何額外的登入步驟，因此可降低使用者在重新導向時放棄的可能性。
- 減少眾多 Azure AD 使用者的部署障礙。

## <a name="verify-azure-ad-integration"></a>驗證 Azure AD 整合

### <a name="multitenant-solutions"></a>多租用戶解決方案

使用 Azure AD 來支援下列動作：

- 在其中一個 Marketplace 店面中註冊您的應用程式。 如需詳細資訊，請檢視[應用程式註冊](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) 或 [AppSource 認證](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified)。
- 在 Azure AD 中啟用多租用戶支援功能，以獲得單鍵試用體驗。

如果您不熟悉 Azure AD 同盟單一登入的用法，請採取下列步驟：

1. 在 Marketplace 中註冊您的應用程式。
1. 使用 [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code) 或 [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) 來開發搭配 Azure AD 的 SSO。
1. 在 Azure AD 中啟用多租用戶支援功能，以提供單鍵試用體驗。

### <a name="single-tenant-solutions"></a>單一租用戶解決方案

使用 Azure AD 來支援下列其中一個動作：

- 使用 [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b) 將來賓使用者新增至目錄。
- 使用 [與我連絡] 發佈選項，為客戶手動設定試用版。
- 開發每個客戶的「試用產品」。
- 建置使用 SSO 的多租用戶範例示範應用程式。

## <a name="next-steps"></a>後續步驟

如果您還沒有這麼做，請 

- [了解](https://azuremarketplace.microsoft.com/sell)市集。

若要在合作夥伴中心註冊，請開始建立新供應項目或使用現有供應項目：

- [登入合作夥伴中心](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)，以建立或完成您的供應項目。
