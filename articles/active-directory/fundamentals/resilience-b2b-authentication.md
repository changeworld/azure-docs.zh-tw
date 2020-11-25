---
title: 使用 Azure Active Directory 建立外部使用者驗證的復原能力
description: 適用于 IT 系統管理員和架構設計人員的指南，以建立外部使用者的復原驗證
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae8e24341c321fbfffb84d9b072abc4cf925aff3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919465"
---
# <a name="build-resilience-in-external-user-authentication"></a>在外部使用者驗證中建立恢復功能

[AZURE ACTIVE DIRECTORY b2b](https://docs.microsoft.com/azure/active-directory/external-identities/what-is-b2b) 共同作業 (Azure AD b2b) 是 [外部](https://docs.microsoft.com/azure/active-directory/external-identities/delegate-invitations) 身分識別的功能，可與其他組織和個人合作。 它可安全地將來賓使用者登入您的 Azure AD 租使用者，而不需要管理其認證。 外部使用者將其身分識別和認證與外部識別提供者整合 (IdP) ，因此他們不需要記住新的認證。 

## <a name="ways-to-authenticate-external-users"></a>驗證外部使用者的方式

您可以選擇對目錄進行外部使用者驗證的方法。 您可以使用 Microsoft Idp 或其他 Idp。

使用每個外部 IdP 時，您會相依于該 IdP 的可用性。 有一些連接到 Idp 的方法時，您可以採取一些動作來提高復原能力。

> [!NOTE] 
> Azure AD B2B 有內建的功能，可從任何 [Azure Active Directory](https://docs.microsoft.com/azure/active-directory) 租使用者或個人 [Microsoft 帳戶](https://account.microsoft.com/account)驗證任何使用者。 您不需要使用這些內建選項來進行任何設定。

### <a name="considerations-for-resilience-with-other-idps"></a>其他 Idp 的恢復功能考慮

使用外部 Idp 進行來賓使用者驗證時，您必須確保維護的某些設定可避免中斷。

| 驗證方法| 復原考慮 |
| - | - |
| 與 [Facebook](https://docs.microsoft.com/azure/active-directory/external-identities/facebook-federation) 或 [Google](https://docs.microsoft.com/azure/active-directory/external-identities/google-federation)等社交 idp 的同盟。| 您必須使用 IdP 維護您的帳戶，並設定您的用戶端識別碼和用戶端密碼。 |
| [與 SAML 和 WS-Federation 識別提供者的直接同盟](https://docs.microsoft.com/azure/active-directory/external-identities/direct-federation)| 您必須與 IdP 擁有者共同作業，以存取其端點（依存于這些端點）。 <br>您必須維護包含憑證和端點的中繼資料。 |
| [電子郵件單次密碼](https://docs.microsoft.com/azure/active-directory/external-identities/one-time-passcode)| 使用這個方法時，您會依賴 Microsoft 的電子郵件系統、使用者的電子郵件系統，以及使用者的電子郵件客戶程式。 |


 

## <a name="self-service-sign-up-preview"></a>自助式註冊 (預覽) 

除了傳送邀請或連結之外，您還可以啟用 [自助式註冊](https://docs.microsoft.com/azure/active-directory/external-identities/self-service-sign-up-overview)。  這可讓外部使用者要求應用程式的存取權。 您必須建立 [API 連接器](https://docs.microsoft.com/azure/active-directory/external-identities/self-service-sign-up-add-api-connector) ，並將其與使用者流程產生關聯。 您可以將定義使用者體驗的使用者流程與一或多個應用程式產生關聯。 

您可以使用 [API 連接器](https://docs.microsoft.com/azure/active-directory/external-identities/api-connectors-overview) ，將自助註冊使用者流程與外部系統 api 整合。 此 API 整合可用於 [自訂核准工作流程](https://docs.microsoft.com/azure/active-directory/external-identities/self-service-sign-up-add-approvals)、 [執行身分識別驗證](https://docs.microsoft.com/azure/active-directory/external-identities/code-samples-self-service-sign-up)，以及其他工作，例如覆寫使用者屬性。 使用 Api 需要您管理下列相依性。

* **API 連接器驗證**：設定連接器需要端點 URL、使用者名稱和密碼。 設定用來維護這些認證的處理常式，並與 API 擁有者合作，以確保您知道任何到期排程。

* **Api 連接器回應**：如果 api 無法使用，請在註冊流程中設計 api 連接器，以正常失敗。 檢查並提供 API 開發人員這些 [範例 api 回應](https://docs.microsoft.com/azure/active-directory/external-identities/self-service-sign-up-add-api-connector) ，以及 [疑難排解的最佳作法](https://docs.microsoft.com/azure/active-directory/external-identities/self-service-sign-up-add-api-connector)。 與 API 開發小組合作，測試所有可能的回應案例，包括接續、驗證錯誤和封鎖回應。 

## <a name="next-steps"></a>後續步驟
系統管理員和架構設計人員的復原能力資源
 
* [使用認證管理建立恢復功能](resilience-in-credentials.md)

* [以裝置狀態建立恢復功能](resilience-with-device-states.md)

* [使用連續存取評估來建立恢復功能 (CAE) ](resilience-with-continuous-access-evaluation.md)

* [打造混合式驗證的復原能力](resilience-in-hybrid.md)

* [使用應用程式 Proxy 來建立應用程式存取的復原能力](resilience-on-premises-access.md)

開發人員的復原能力資源

* [在您的應用程式中建立 IAM 復原](resilience-app-development-overview.md)

* [在您的 CIAM 系統中打造復原能力](resilience-b2c.md)
 
