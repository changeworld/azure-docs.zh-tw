---
title: 為多租戶應用程式啟用自動使用者預配 - Azure AD
description: 支援自動設定的獨立軟體廠商指南
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522388"
---
# <a name="enable-automatic-user-provisioning-for-your-multi-tenant-application"></a>為多租戶應用程式啟用自動使用者預配

自動使用者預配是自動創建、維護和刪除目標系統中的使用者標識（如軟體即服務應用程式）的過程。

## <a name="why-enable-automatic-user-provisioning"></a>為什麼要啟用自動使用者預配？

在使用者首次登錄需要使用者預配之前，需要使用者記錄在應用程式中存在的應用程式。 作為服務提供者，您有優勢，客戶也受益匪淺。

### <a name="benefits-to-you-as-the-service-provider"></a>作為服務提供者，您受益匪淺

* 通過使用 Microsoft 標識平臺提高應用程式的安全性。

* 減少實際和感知的客戶努力來採用您的應用程式。

* 通過使用基於跨域身份管理系統 （SCIM） 的系統進行預配，降低與多個識別提供程式 （IdP） 集成以自動預配使用者的成本。

* 通過提供豐富的日誌來説明客戶解決使用者預配問題，從而降低支援成本。

* 提高 Azure [AD 應用庫中](https://azuremarketplace.microsoft.com/marketplace/apps)應用程式的可見度。

* 在應用教程頁面中獲取優先順序清單。

### <a name="benefits-to-your-customers"></a>給客戶帶來的好處

* 通過自動刪除更改角色或將組織留給應用程式的使用者對應用程式的訪問，提高安全性。

* 通過避免與手動預配相關的人為錯誤和重複工作，簡化應用程式的使用者管理。

* 降低託管和維護定制開發資源調配解決方案的成本。

## <a name="choose-a-provisioning-method"></a>選擇佈建方法

Azure AD 提供了多個集成路徑，可為應用程式啟用自動使用者預配。

* [Azure AD 預配服務](../app-provisioning/user-provisioning.md)管理使用者從 Azure AD 預配和從應用程式（出站預配）和從應用程式到 Azure AD（入站預配）的預配和取消預配。 該服務連接到應用程式提供的跨域標識管理系統 （SCIM） 使用者管理 API 終結點。

* 使用 Microsoft 圖形時，應用程式通過查詢[Microsoft 圖形](https://docs.microsoft.com/graph/)API 來管理從 Azure AD 到應用程式的使用者和組入站和出站預配。

* 如果應用程式使用 SAML 進行聯合，則可以啟用安全斷言標記語言（即時間（SAML JIT）使用者預配。 它使用 SAML 權杖中發送的聲明資訊來預配使用者。

為了説明確定要用於應用程式的集成選項，請參閱高級比較表，然後查看有關每個選項的更多詳細資訊。

| 自動預配啟用或增強的功能| Azure AD 預配服務 （SCIM 2.0）| 微軟圖形 API （OData v4.0）| SAML JIT |
|---|---|---|---|
| Azure AD 中的使用者和組管理| √| √| 僅限使用者 |
| 管理從本地活動目錄同步的使用者和組| √*| √*| 僅限使用者* |
| 在預配訪問 O365 資料（團隊、SharePoint、電子郵件、日曆、文檔等）期間訪問超出使用者和組的資料| X+| √| X |
| 根據商務規則創建、讀取和更新使用者| √| √| √ |
| 根據商務規則刪除使用者| √| √| X |
| 管理 Azure 門戶中所有應用程式的自動使用者預配| √| X| √ |
| 支援多個識別提供程式| √| X| √ |
| 支援客戶帳戶 （B2B）| √| √| √ |
| 支援非企業帳戶 （B2C）| X| √| √ |

<sup>*</sup>• 需要 Azure AD 連接設置才能將使用者從 AD 同步到 Azure AD。  
<sup>+</sup >• 使用 SCIM 進行預配並不妨礙您將應用程式與 MIcrosoft 圖形集成用於其他目的。

## <a name="azure-ad-provisioning-service-scim"></a>Azure AD 預配服務 （SCIM）

Azure AD 預配服務使用[SCIM，](https://aka.ms/SCIMOverview)這是許多標識提供程式 （IdP） 以及應用程式（例如 Slack、G Suite、Dropbox）支援的預配的行業標準。 如果要支援除 Azure AD 之外的 IdP，我們建議您使用 Azure AD 預配服務，因為任何符合 SCIM 的 IdP 都可以連接到 SCIM 終結點。 構建一個簡單的 /User 終結點，可以啟用預配，而無需維護自己的同步引擎。 

有關 Azure AD 預配服務使用者 SCIM 的詳細資訊，請參閱： 

* [瞭解有關 SCIM 標準的更多](https://aka.ms/SCIMOverview)

* [使用 System for Cross-Domain Identity Management (SCIM) 自動將使用者和群組從 Azure Active Directory 佈建到應用程式](../app-provisioning/use-scim-to-provision-users-and-groups.md)

* [瞭解 Azure AD SCIM 實現](../app-provisioning/use-scim-to-provision-users-and-groups.md)

## <a name="microsoft-graph-for-provisioning"></a>用於預配的微軟圖形

使用 Microsoft 圖形進行預配時，可以訪問圖形中提供的所有富使用者資料。 除了使用者和組的詳細資訊外，您還可以獲取其他資訊，如使用者的角色、經理和直接下屬、擁有和註冊的設備，以及[Microsoft 圖形](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)中提供的數百個其他資料片段。 

超過 1500 萬個組織和 90% 的財富 500 強公司使用 Azure AD，同時訂閱 Microsoft 雲服務，如 Office 365、Microsoft Azure、企業移動套件或 Microsoft 365。 您可以使用 Microsoft Graph 將應用與管理工作流（如員工入職（和終止）、設定檔維護等集成。 

瞭解有關使用 Microsoft 圖形進行預配的更多詳細資訊：

* [微軟圖形主頁](https://developer.microsoft.com/graph)

* [Microsoft Graph 概觀](https://docs.microsoft.com/graph/overview)

* [微軟圖形概述](https://docs.microsoft.com/graph/auth/)

* [開始使用微軟圖形](https://developer.microsoft.com/graph/get-started)

## <a name="using-saml-jit-for-provisioning"></a>使用 SAML JIT 進行預配

如果只想在首次登錄到應用程式時預配使用者，並且不需要自動取消預配使用者，SAML JIT 是一個選項。 您的應用程式必須支援 SAML 2.0 作為聯合協定才能使用 SAML JIT。

SAML JIT 使用 SAML 權杖中的聲明資訊在應用程式中創建和更新使用者資訊。 客戶可以根據需要在 Azure AD 應用程式中配置這些必需的聲明。 有時需要從應用程式端啟用 JIT 預配，以便客戶可以使用此功能。 SAML JIT 可用於創建和更新使用者，但不能刪除或停用應用程式中的使用者。

## <a name="next-steps"></a>後續步驟

* [為應用程式啟用單一登入](../manage-apps/isv-sso-content.md)

* [提交您的應用程式清單](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx)，並與 Microsoft 合作，在 Microsoft 網站上創建文檔。

* [加入微軟合作夥伴網路（免費），並創建您的市場計畫](https://partner.microsoft.com/en-us/explore/commercial)。
