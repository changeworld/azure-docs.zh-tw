---
title: 使用 Azure Active Directory 來啟用 Microsoft AppSource 和 Azure Marketplace 清單 | Azure
description: 使用 Azure Active Directory 在 Azure Marketplace 和 AppSource 中為應用程式和服務發行者啟用清單類型。
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/12/2018
ms.author: dsindona
ms.openlocfilehash: 45855038e60dcdc3be4f98cfdceed69df5e8c946
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80286314"
---
# <a name="enable-an-appsource-and-marketplace-listing-by-using-azure-active-directory"></a>使用 Azure Active Directory 來啟用 AppSource 和 Marketplace 清單

 Azure Active Directory (Azure AD) 是一種雲端身分識別服務，可以 Microsoft 帳戶進行驗證。 Azure AD 使用業界標準架構。 [瞭解有關 Azure 活動目錄 的更多內容](https://azure.microsoft.com/services/active-directory)。

## <a name="azure-ad-benefits"></a>Azure AD 的優點

Microsoft AppSource 和 Azure Marketplace 客戶會使用產品內體驗來搜尋清單目錄。 這些動作需要客戶登入產品。 Azure DNS 整合提供下列優點：

- 更快速的業務開發和最佳化客戶體驗
- 數百萬企業使用者適用的單一登入 (SSO)
- 在不同合作夥伴發佈的各種應用程式間提供一致的使用者登入體驗
- 行動和雲端應用程式適用的可調式跨平台驗證

## <a name="offers-that-require-azure-ad"></a>要求 Azure AD 的供應項目

適用於 AppSource 及 Azure Marketplace 的各種[列出選項和供應項目類型](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type)對於 Azure AD 實作有不同的需求。 如需詳細資訊，請參閱下表︰

| **優惠類型**    | **是否需要 Azure AD SSO？**  |  |   |  |
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

- 如需如何將 Azure AD 整合到您的清單以啟用單一登入的相關資訊，請參閱[開發人員適用的 Azure Active Directory]( https://aka.ms/aaddev)。
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
1. 使用[OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code)或[OpenID 連接](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code)使用 Azure AD 開發 SSO。
1. 在 Azure AD 中啟用多租用戶支援功能，以提供單鍵試用體驗。

### <a name="single-tenant-solutions"></a>單一租用戶解決方案

使用 Azure AD 來支援下列其中一個動作：

- 使用 [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b) 將來賓使用者新增至目錄。
- 使用 [與我連絡]**** 發佈選項，為客戶手動設定試用版。
- 開發每個客戶的「試用產品」。
- 建置使用 SSO 的多租用戶範例示範應用程式。

## <a name="next-steps"></a>後續步驟

- 請確定您已[在 Azure Marketplace 中註冊](https://azuremarketplace.microsoft.com/sell)。
- 有關如何創建或完成產品/服務的詳細資訊[，請參閱如何創建合作夥伴中心帳戶](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)。
