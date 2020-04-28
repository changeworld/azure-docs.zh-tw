---
title: 為多租使用者應用程式啟用自動使用者布建-Azure AD
description: 適用于啟用自動布建的獨立軟體廠商指南
services: active-directory
documentationcenter: azure
author: BarbaraSelden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/23/2019
ms.author: baselden
ms.reviewer: zhchia
ms.collection: active-directory
ms.openlocfilehash: 93e1d879f69a95fe7472ce530e0e9f38f3480f39
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77522388"
---
# <a name="enable-automatic-user-provisioning-for-your-multi-tenant-application"></a>為您的多租使用者應用程式啟用自動使用者布建

自動使用者布建是在目標系統（例如軟體即服務應用程式）中自動化建立、維護和移除使用者身分識別的程式。

## <a name="why-enable-automatic-user-provisioning"></a>為何要啟用自動使用者布建？

需要使用者記錄的應用程式會在使用者第一次登入需要使用者布建之前，存在於應用程式中。 身為服務提供者的權益與您的客戶有好處。

### <a name="benefits-to-you-as-the-service-provider"></a>作為服務提供者的權益

* 使用 Microsoft 身分識別平臺，提高應用程式的安全性。

* 減少實際和認知的客戶工作，以採用您的應用程式。

* 藉由使用系統進行跨網域身分識別管理（SCIM）型布建，降低與多個身分識別提供者（Idp）整合的成本，以自動布建使用者。

* 提供豐富的記錄來協助客戶疑難排解使用者布建問題，以降低支援成本。

* 在[Azure AD 應用程式庫](https://azuremarketplace.microsoft.com/marketplace/apps)中提高應用程式的可見度。

* 取得應用程式教學課程頁面中的優先順序清單。

### <a name="benefits-to-your-customers"></a>受益于您的客戶

* 自動移除應用程式的存取權，讓變更角色的使用者或將組織留給您的應用程式，以提高安全性。

* 避免人為錯誤以及與手動布建相關的重複性工作，以簡化應用程式的使用者管理。

* 降低裝載及維護自訂開發的布建解決方案的成本。

## <a name="choose-a-provisioning-method"></a>選擇佈建方法

Azure AD 提供數個整合路徑來為您的應用程式啟用自動使用者布建。

* Azure AD 布建[服務](../app-provisioning/user-provisioning.md)會管理布建和解除布建使用者，從 Azure AD 應用程式（輸出布建），以及從您的應用程式到 Azure AD （輸入布建）。 此服務會連接到系統，以供您的應用程式所提供的跨網域身分識別管理（SCIM）使用者管理 API 端點使用。

* 使用[Microsoft Graph](https://docs.microsoft.com/graph/)時，您的應用程式會藉由查詢 Microsoft Graph API，來管理使用者和群組的輸入和輸出布建，使其無法從 Azure AD 到您的應用程式。

* 如果您的應用程式使用 SAML 進行同盟，就可以啟用安全性聲明標記語言的即時（SAML JIT）使用者布建。 它會使用 SAML 權杖中傳送的宣告資訊來布建使用者。

若要協助判斷您的應用程式要使用哪一個整合選項，請參閱高階比較表，然後查看每個選項的詳細資訊。

| 自動布建已啟用或增強的功能| Azure AD 布建服務（SCIM 2.0）| Microsoft Graph API （OData v4.0）| SAML JIT |
|---|---|---|---|
| Azure AD 中的使用者和群組管理| √| √| 僅限使用者 |
| 管理從內部部署 Active Directory 同步處理的使用者和群組| √*| √*| 僅限使用者 * |
| 在布建存取 O365 資料（小組、SharePoint、電子郵件、行事曆、檔等）期間，存取使用者和群組以外的資料| X+| √| X |
| 根據商務規則建立、讀取和更新使用者| √| √| √ |
| 根據商務規則刪除使用者| √| √| X |
| 從 Azure 入口網站管理所有應用程式的自動使用者布建| √| X| √ |
| 支援多個身分識別提供者| √| X| √ |
| 支援來賓帳戶（B2B）| √| √| √ |
| 支援非企業帳戶（B2C）| X| √| √ |

<sup>*</sup>–需要 Azure AD Connect 設定，才能將使用者從 AD 同步到 Azure AD。  
<sup>+</sup >–使用 SCIM 進行布建時，不會妨礙您將應用程式與 MIcrosoft Graph 整合以供其他用途。

## <a name="azure-ad-provisioning-service-scim"></a>Azure AD 布建服務（SCIM）

Azure AD 布建服務會使用[SCIM](https://aka.ms/SCIMOverview)，這是許多身分識別提供者（idp）以及應用程式（例如，時差、g Suite、Dropbox）所支援之布建的業界標準。 如果除了 Azure AD 之外，您還想要支援 Idp，建議您使用 Azure AD 布建服務，因為任何 SCIM 相容的 IdP 都可以連接到您的 SCIM 端點。 建立簡單的/User 端點，您可以啟用布建，而不必維護自己的同步處理引擎。 

如需有關 Azure AD 布建服務使用者如何 SCIM 的詳細資訊，請參閱： 

* [深入瞭解 SCIM standard](https://aka.ms/SCIMOverview)

* [使用 System for Cross-Domain Identity Management (SCIM) 自動將使用者和群組從 Azure Active Directory 佈建到應用程式](../app-provisioning/use-scim-to-provision-users-and-groups.md)

* [瞭解 Azure AD SCIM 的執行](../app-provisioning/use-scim-to-provision-users-and-groups.md)

## <a name="microsoft-graph-for-provisioning"></a>布建的 Microsoft Graph

當您使用 Microsoft Graph 進行布建時，可以存取 Graph 中所有可用的豐富使用者資料。 除了使用者和群組的詳細資料之外，您也可以提取其他資訊，例如使用者的角色、管理員和直屬員工、擁有與已註冊的裝置，以及[Microsoft Graph](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)中提供的數百個其他資料片段。 

在訂閱 Microsoft 雲端服務（例如 Office 365、Microsoft Azure、企業行動化套件或 Microsoft 365）的同時，有超過15000000個組織和90% 的財富500公司使用 Azure AD。 您可以使用 Microsoft Graph，將您的應用程式與系統管理工作流程（例如員工上線（和終止）、設定檔維護等等）整合。 

深入瞭解如何使用 Microsoft Graph 進行布建：

* [Microsoft Graph 首頁](https://developer.microsoft.com/graph)

* [Microsoft Graph 概觀](https://docs.microsoft.com/graph/overview)

* [Microsoft Graph Auth 總覽](https://docs.microsoft.com/graph/auth/)

* [開始使用 Microsoft Graph](https://developer.microsoft.com/graph/get-started)

## <a name="using-saml-jit-for-provisioning"></a>使用 SAML JIT 進行布建

如果您只想要在第一次登入應用程式時布建使用者，而不需要自動取消布建使用者，則可選擇使用 SAML JIT。 您的應用程式必須支援 SAML 2.0 做為同盟通訊協定，才能使用 SAML JIT。

SAML JIT 會使用 SAML 權杖中的宣告資訊，在應用程式中建立和更新使用者資訊。 客戶可以視需要在 Azure AD 應用程式中設定這些必要的宣告。 有時候，必須從應用程式端啟用 JIT 布建，讓客戶可以使用這項功能。 SAML JIT 適用于建立和更新使用者，但無法刪除或停用應用程式中的使用者。

## <a name="next-steps"></a>後續步驟

* [為您的應用程式啟用單一登入](../manage-apps/isv-sso-content.md)

* 向 Microsoft[提交您的應用程式清單](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx)和合作夥伴，以在 microsoft 網站上建立檔。

* [加入 Microsoft 合作夥伴網路（免費），並建立您的進入市場計畫](https://partner.microsoft.com/en-us/explore/commercial)。
