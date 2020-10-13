---
title: 啟用多租使用者應用程式的自動使用者布建-Azure AD
description: 用於啟用自動化布建的獨立軟體廠商指南
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 07/23/2019
ms.author: kenwith
ms.reviewer: zhchia
ms.openlocfilehash: 617128640c4f4ae4ce5ac32803e459a9a3eb448f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90706461"
---
# <a name="enable-automatic-user-provisioning-for-your-multi-tenant-application"></a>啟用多租使用者應用程式的自動使用者布建

自動使用者布建是自動化在目標系統中建立、維護和移除使用者身分識別的程式，例如您的軟體即服務應用程式。

## <a name="why-enable-automatic-user-provisioning"></a>為何要啟用自動使用者布建？

需要使用者記錄的應用程式必須存在於應用程式中，才能讓使用者的第一次登入需要使用者布建。 您是服務提供者的權益，並可讓您的客戶受益。

### <a name="benefits-to-you-as-the-service-provider"></a>以服務提供者的形式獲益

* 使用 Microsoft 身分識別平臺來提升應用程式的安全性。

* 減少實際和認知的客戶投入時間，以採用您的應用程式。

* 利用 System 進行跨網域身分識別管理 (SCIM) 型布建，降低與多個身分識別提供者整合的成本 (Idp) 來自動布建使用者。

* 提供豐富的記錄檔來協助客戶排解使用者布建問題，以降低支援成本。

* 在 [Azure AD 應用](https://azuremarketplace.microsoft.com/marketplace/apps)程式資源庫中，提高應用程式的可見度。

* 取得應用程式教學課程頁面中的優先順序清單。

### <a name="benefits-to-your-customers"></a>讓您的客戶受益

* 針對變更角色或離開組織至您應用程式的使用者，自動移除應用程式的存取權，以提高安全性。

* 藉由避免人為錯誤和與手動布建相關的重複性工作，簡化應用程式的使用者管理。

* 降低裝載和維護自訂開發的布建解決方案的成本。

## <a name="choose-a-provisioning-method"></a>選擇佈建方法

Azure AD 提供數個整合路徑來為您的應用程式啟用自動使用者布建。

* Azure AD 布建 [服務](../app-provisioning/user-provisioning.md) 可管理將 Azure AD 使用者布建和解除布建到您的應用程式 (輸出布建) ，以及從您的應用程式 Azure AD (輸入布建) 。 此服務會連線到系統，以進行跨網域身分識別管理 (SCIM) 應用程式所提供的使用者管理 API 端點。

* 使用 [Microsoft Graph](/graph/)時，您的應用程式會藉由查詢 Microsoft Graph API，來管理從 Azure AD 到您應用程式的使用者和群組的輸入和輸出布建。

* 如果您的應用程式使用 SAML 進行同盟，則可以啟用安全性聲明標記語言及時 (SAML JIT) 使用者布建。 它會使用 SAML 權杖中傳送的宣告資訊來布建使用者。

若要協助判斷您的應用程式所使用的整合選項，請參閱高階比較表，然後查看每個選項的詳細資訊。

| 自動布建所啟用或增強的功能| Azure AD 布建服務 (SCIM 2.0) | Microsoft Graph API (OData v 4.0) | SAML JIT |
|---|---|---|---|
| Azure AD 中的使用者和群組管理| √| √| 僅限使用者 |
| 管理從內部部署 Active Directory 同步處理的使用者和群組| √*| √*| 僅限使用者 * |
| 在布建存取 Microsoft 365 資料 (團隊、SharePoint、電子郵件、行事曆、檔等）期間，存取使用者和群組以外的資料。 ) | X+| √| X |
| 根據商務規則建立、讀取和更新使用者| √| √| √ |
| 根據商務規則刪除使用者| √| √| X |
| 從 Azure 入口網站管理所有應用程式的自動使用者布建| √| X| √ |
| 支援多個身分識別提供者| √| X| √ |
|  (B2B) 支援來賓帳戶| √| √| √ |
|  (B2C) 支援非企業帳戶| X| √| √ |

<sup>*</sup> –需要 Azure AD Connect 設定才能將 AD 中的使用者同步處理到 Azure AD。  
<sup>+</sup >–使用 SCIM 進行布建不會讓您將應用程式與 Microsoft Graph 整合，以供其他用途使用。

## <a name="azure-ad-provisioning-service-scim"></a>Azure AD 布建服務 (SCIM) 

Azure AD 布建服務會使用 [SCIM](https://aka.ms/SCIMOverview)，這是許多身分識別提供者所支援之布建的業界標準 (idp) 和應用程式 (例如，可供使用、g Suite、Dropbox) 。 如果您除了 Azure AD 之外，還想要支援 Idp，則建議您使用 Azure AD 布建服務，因為任何符合 SCIM 規範的 IdP 都可以連線到您的 SCIM 端點。 建立簡單的/User 端點，您可以啟用布建，而不需要維護您自己的同步處理引擎。 

如需 Azure AD 布建服務使用者如何 SCIM 的詳細資訊，請參閱： 

* [深入瞭解 SCIM standard](https://aka.ms/SCIMOverview)

* [使用 System for Cross-Domain Identity Management (SCIM) 自動將使用者和群組從 Azure Active Directory 佈建到應用程式](../app-provisioning/use-scim-to-provision-users-and-groups.md)

* [瞭解 Azure AD SCIM 的執行](../app-provisioning/use-scim-to-provision-users-and-groups.md)

## <a name="microsoft-graph-for-provisioning"></a>布建 Microsoft Graph

當您使用 Microsoft Graph 進行布建時，您可以存取圖形中所有豐富的使用者資料。 除了使用者和群組的詳細資料之外，您還可以提取額外的資訊，例如使用者的角色、管理員和直屬員工、擁有和註冊的裝置，以及 [Microsoft Graph](/graph/api/overview?view=graph-rest-1.0)中提供的數百個其他資料片段。 

超過15000000的組織和90% 的財富500公司在訂閱 Microsoft 雲端服務（例如 Microsoft 365、Microsoft Azure 或企業行動力套件）時，會使用 Azure AD。 您可以使用 Microsoft Graph 來整合應用程式與系統管理工作流程，例如員工上線 (和終止) 、設定檔維護等等。 

深入瞭解如何使用 Microsoft Graph 進行布建：

* [Microsoft Graph 首頁](https://developer.microsoft.com/graph)

* [Microsoft Graph 概觀](/graph/overview)

* [Microsoft Graph Auth 總覽](/graph/auth/)

* [開始使用 Microsoft Graph](https://developer.microsoft.com/graph/get-started)

## <a name="using-saml-jit-for-provisioning"></a>使用 SAML JIT 進行布建

如果您只想要在第一次登入您的應用程式時布建使用者，且不需要自動取消布建使用者，則可以選擇使用 SAML JIT。 您的應用程式必須支援 SAML 2.0 作為同盟通訊協定，才能使用 SAML JIT。

SAML JIT 會使用 SAML 權杖中的宣告資訊，在應用程式中建立和更新使用者資訊。 客戶可以視需要在 Azure AD 應用程式中設定這些必要的宣告。 有時候，必須從應用程式端啟用 JIT 布建，讓客戶可以使用這項功能。 SAML JIT 適用于建立和更新使用者，但無法刪除或停用應用程式中的使用者。

## <a name="next-steps"></a>後續步驟

* [為您的應用程式啟用單一登入](../manage-apps/isv-sso-content.md)

* [提交您的應用程式清單](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx) ，並與 microsoft 合作，以在 microsoft 網站上建立檔。

* [加入 Microsoft 合作夥伴網路 (免費) ，並建立您的「移至市場」方案](https://partner.microsoft.com/explore/commercial)。
