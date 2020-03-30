---
title: 使用 Azure AD 管理對應用程式的存取 | Microsoft Docs
description: 描述 Azure Active Directory 如何讓組織可以指定每個使用者擁有存取權的應用程式。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 489b15423add03d69070bc32057af97396a85309
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409061"
---
# <a name="managing-access-to-apps"></a>管理應用程式的存取

在應用程式整合到您的組織的身分識別系統之後，進行中的存取管理、使用方式評估和報告持續成為一項挑戰。 在許多情況下，IT 系統管理員或技術服務人員都需要持續積極地為您管理應用程式存取權。 有時候，指派是由一般或分區 IT 小組執行。 通常，指派決策要委派給商務決策人員，在 IT 進行指派之前需要其核准。  其他組織投資於與現有的自動化身分識別與存取管理系統的整合，像是角色型存取控制 (RBAC) 或屬性型存取控制 (ABAC)。 整合與規則開發往往需要特製且所費不貲。 監視或報告任一管理方式是自己單獨、昂貴且複雜的投資。

## <a name="how-does-azure-active-directory-help"></a>Azure Active Directory 有何助益？

Azure AD 對於已設定的應用程式支援廣泛的存取管理，讓組織得以透過委派並納入系統管理員管理，輕易地達成正確的存取原則，範圍包括自動、屬性式指派 (ABAC 或 RBAC 案例)。 有了 Azure AD，您可以輕鬆地達成複雜的原則，結合單一應用程式的多個管理模型並且甚至可以在具有相同對象的應用程式之間重覆使用管理規則。

利用 Azure AD，使用方式和指派報告可完全整合，讓系統管理員輕鬆地報告指派狀態、指派錯誤和平均使用量。

### <a name="assigning-users-and-groups-to-an-app"></a>將使用者和組分配給應用

Azure AD 的應用程式指派著重於兩種主要的指派模式：

* **個別指派** ：具備目錄全域管理員權限的 IT 系統管理員可以選取個別使用者帳戶，並授與它們應用程式存取權限。

* **基於組的分配（需要 Azure AD 高級 P1 或 P2）** 具有目錄全域管理員許可權的 IT 管理員可以將組分配給應用程式。 特定使用者的存取權決於在使用者嘗試存取應用程式時，使用者是否為群組的成員。 換句話說，系統管理員可以有效地建立指派規則，說明「獲指派群組的任何現有成員可存取應用程式」。 使用這個指派選項，系統管理員可以受益於 Azure AD 群組管理選項，包括 [屬性型動態群組](../fundamentals/active-directory-groups-create-azure-portal.md)、外部系統群組 (例如：內部部署 Active Directory 或工作日)、系統管理員管理或自助管理的群組。 單一群組可以輕鬆地指派給多個應用程式，確保與指派同質性的應用程式可以共用指派規則，降低整體管理複雜度。 請注意，目前對應用程式的群組式指派並不支援巢狀群組成員資格。

系統管理員可以使用這些兩種指派模式，達成任何需要的指派管理方法。

### <a name="requiring-user-assignment-for-an-app"></a>要求應用的使用者分配

對於某些類型的應用程式，您可以選擇[要求將使用者分配給應用程式](assign-user-or-group-access-portal.md#configure-an-application-to-require-user-assignment)。 通過這樣做，可以防止每個人登錄，除了您顯式分配給應用程式的使用者。 以下類型的應用程式支援此選項：

* 為基於 SAML 的身份驗證為聯合單一登入 （SSO） 配置的應用程式
* 使用 Azure 活動目錄預身份驗證的應用程式代理應用程式
* 在 Azure AD 應用程式平臺上構建的應用程式，在使用者或管理員同意該應用程式後使用 OAuth 2.0 / OpenID 連接身份驗證。某些企業應用程式對允許誰登錄提供了額外的控制。

當*不需要*使用者分配時，未分配的使用者在其"我的應用"訪問面板上看不到應用，但他們仍然可以登錄到應用程式本身（也稱為 SP 啟動的登錄），也可以在應用程式**的屬性**頁（也稱為 IDP 啟動的登錄）中使用**使用者訪問 URL。**

對於某些應用程式，在應用程式的屬性中不可用需要使用者分配的選項。 在這些情況下，可以使用 PowerShell 在服務主體上設置 appRoleAssignment需要屬性。

### <a name="determining-the-user-experience-for-accessing-apps"></a>確定訪問應用的使用者體驗

Azure AD 提供了[幾種可自訂的方式將應用程式部署到](end-user-experiences.md)組織中的最終使用者：

* Azure AD 我的應用訪問面板
* Office 365 應用程式啟動程式
* 直接登錄到聯合應用（服務 pr）
* 同盟、密碼或現有應用程式的深層連結

您可以確定分配給企業應用的使用者是否可以在訪問面板和 Office 365 應用程式啟動器中看到它。

## <a name="example-complex-application-assignment-with-azure-ad"></a>示例：使用 Azure AD 的複雜應用程式分配
考慮 Salesforce 之類的應用程式。 在許多組織中，Salesforce 主要是供行銷和銷售團隊使用。 通常，行銷小組的成員對 Salesforce 具有高特殊權限存取權，而銷售小組的成員具有限制存取權。 在許多情況下，為數眾多的資訊工作者對應用程式的存取都會受到限制。 這些規則的例外狀況會使情況更加複雜。 它通常是行銷或銷售領導小組的特權，用來授與使用者存取或在這些一般規則以外個別變更其角色。

利用 Azure AD，可以預先設定 Salesforce 之類的應用程式使用單一登入 (SSO) 和自動化佈建。 一旦設定了應用程式，系統管理員可以採取一次性動作來建立及指派適當的群組。 在此範例中，系統管理員可以執行下列指派：

* [動態群組](../fundamentals/active-directory-groups-create-azure-portal.md) ，以自動代表行銷和銷售小組的所有成員使用部門或角色之類的屬性：
  
  * 行銷群組的所有成員會全部指派給 Salesforce 中的「行銷」角色
  * 銷售小組群組的所有成員會全部指派給 Salesforce 中的「銷售」角色。 進一步細分可使用多個群組，其代表指派到不同 Saleforce 角色的區域銷售小組。

* 若要啟用例外狀況機制，可以為每個角色建立自助群組。 例如，可以將「Salesforce 行銷例外狀況」群組建立為自助群組。 群組可以指派給 Salesforce 行銷角色，而行銷領導小組則可成為擁有者。 行銷領導小組的成員可以加入或移除使用者、設定聯結原則或甚至核准或拒絕個別使用者加入的要求。 此機制透過不需要經過擁有者或成員特殊訓練的資訊工作者適當的體驗來支援。

在此情況下，所有指派的使用者會自動佈建至 Salesforce，因為當他們加入至不同群組時，他們的角色指派會在 Salesforce 中更新。 使用者就能夠透過 Microsoft 應用程式存取面板、Office Web 用戶端或甚至是藉由瀏覽至其組織的 Salesforce 登入頁面來探索及存取 Salesforce。 系統管理員能夠使用 Azure AD 報告輕鬆檢視使用方式和指派狀態。

管理員可以使用[Azure AD 條件訪問](../active-directory-conditional-access-azure-portal.md)為特定角色設置訪問策略。 這些原則可以包括是否允許公司環境外部的存取，甚至是 Multi-Factor Authentication 或裝置需求，以在各種情況下達成存取。

## <a name="access-to-microsoft-applications"></a>訪問微軟應用程式

Microsoft 應用程式 (如 Office 365 Exchange、SharePoint、Yammer 等) 在指派與管理方面，比第三方 SaaS 應用程式或與 Azure AD 整合以單一登入的其他應用程式略為困難。

使用者有三種方式可取得 Microsoft 所發佈應用程式的存取權。

- 對於 Office 365 或其他付費套件中的應用程式，會透過**指派授權**直接指派至使用者帳戶，或使用我們的群組授權指派功能來透過群組，將存取權授與使用者。
- 對於 Microsoft 或協力廠商自由發佈的供任何人使用的應用程式，使用者可以通過[使用者同意](configure-user-consent.md)獲得存取權限。 這意味著他們使用 Azure AD 工作或學校帳戶登錄到應用程式，並允許其訪問其帳戶上某些有限的資料集。
- 對於 Microsoft 或協力廠商自由發佈的供任何人使用的應用程式，使用者也可以通過[管理員同意](manage-consent-requests.md)獲得存取權限。 這表示系統管理員已決定組織中的每個人都能使用該應用程式，因此系統管理員使用全域管理員帳戶身分登入應用程式，並將存取權授與組織中的每個人。

某些應用程式合併這些方法。 例如，某些 Microsoft 應用程式是 Office 365 訂閱的一部分，但仍需要同意。

使用者可以通過其 Office 365 門戶訪問 Office 365 應用程式。 您還可以在"我的應用程式訪問"面板中顯示或隱藏 Office 365 應用程式，並在目錄中的 **"使用者"設置**中[切換 Office 365 可見度](hide-application-from-user-portal.md)。 

與企業應用一樣，您可以通過 Azure 門戶[將使用者分配給](assign-user-or-group-access-portal.md)某些 Microsoft 應用程式，或者，如果門戶選項不可用，則通過使用 PowerShell。

## <a name="next-steps"></a>後續步驟
* [使用條件訪問保護應用](../active-directory-conditional-access-azure-portal.md)
* [自助式群組管理/SSAA](../users-groups-roles/groups-self-service-management.md)
