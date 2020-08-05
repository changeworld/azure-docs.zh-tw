---
title: 使用 Azure AD 管理應用程式的存取
description: 描述 Azure Active Directory 如何讓組織可以指定每個使用者擁有存取權的應用程式。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/16/2017
ms.author: kenwith
ms.openlocfilehash: 1b19f4aae7bf7477dbe5950f2d4df31e2de81372
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/04/2020
ms.locfileid: "87562560"
---
# <a name="managing-access-to-apps"></a>管理應用程式的存取

在應用程式整合到您的組織的身分識別系統之後，進行中的存取管理、使用方式評估和報告持續成為一項挑戰。 在許多情況下，IT 系統管理員或技術服務人員都需要持續積極地為您管理應用程式存取權。 有時候，指派是由一般或分區 IT 小組執行。 通常，指派決策要委派給商務決策人員，在 IT 進行指派之前需要其核准。  其他組織投資於與現有的自動化身分識別與存取管理系統的整合，像是角色型存取控制 (RBAC) 或屬性型存取控制 (ABAC)。 整合與規則開發往往需要特製且所費不貲。 監視或報告任一管理方式是自己單獨、昂貴且複雜的投資。

## <a name="how-does-azure-active-directory-help"></a>Azure Active Directory 有何助益？

Azure AD 對於已設定的應用程式支援廣泛的存取管理，讓組織得以透過委派並納入系統管理員管理，輕易地達成正確的存取原則，範圍包括自動、屬性式指派 (ABAC 或 RBAC 案例)。 有了 Azure AD，您可以輕鬆地達成複雜的原則，結合單一應用程式的多個管理模型並且甚至可以在具有相同對象的應用程式之間重覆使用管理規則。

利用 Azure AD，使用方式和指派報告可完全整合，讓系統管理員輕鬆地報告指派狀態、指派錯誤和平均使用量。

### <a name="assigning-users-and-groups-to-an-app"></a>將使用者和群組指派給應用程式

Azure AD 的應用程式指派著重於兩種主要的指派模式：

* **個別指派** ：具備目錄全域管理員權限的 IT 系統管理員可以選取個別使用者帳戶，並授與它們應用程式存取權限。

* **以群組為基礎的指派 (需要 Azure AD Premium P1 或 P2)** ：具備目錄全域管理員權限的 IT 管理員可以將群組指派給應用程式。 特定使用者的存取權決於在使用者嘗試存取應用程式時，使用者是否為群組的成員。 換句話說，系統管理員可以有效地建立指派規則，說明「獲指派群組的任何現有成員可存取應用程式」。 使用這個指派選項，系統管理員可以受益於 Azure AD 群組管理選項，包括 [屬性型動態群組](../fundamentals/active-directory-groups-create-azure-portal.md)、外部系統群組 (例如：內部部署 Active Directory 或工作日)、系統管理員管理或自助管理的群組。 單一群組可以輕鬆地指派給多個應用程式，確保與指派同質性的應用程式可以共用指派規則，降低整體管理複雜度。 請注意，目前對應用程式的群組式指派並不支援巢狀群組成員資格。

系統管理員可以使用這些兩種指派模式，達成任何需要的指派管理方法。

### <a name="requiring-user-assignment-for-an-app"></a>需要將使用者指派給應用程式

在某些類型的應用程式中，您可以選擇 [[需要將使用者指派給應用程式]](assign-user-or-group-access-portal.md#configure-an-application-to-require-user-assignment) 選項。 如此一來，除了您明確指派給應用程式的使用者，其他使用者一概禁止登入。 下列類型的應用程式支援此選項：

* 已設定同盟單一登入 (SSO) 與 SAML 型驗證的應用程式
* 使用 Azure Active Directory 預先驗證的應用程式 Proxy 應用程式
* 在 Azure AD 應用程式平台上建置的應用程式，在使用者或管理員同意應用程式之後，應用程式將使用 OAuth 2.0/OpenID Connect 驗證。

「不需要」指派使用者時，未指派的使用者在 [我的應用程式] 存取面板上看不到該應用程式，但仍可登入應用程式本身 (也稱為 SP 起始的登入)，或在應用程式的 [屬性] 頁面中使用 [使用者存取 URL] (也稱為 IDP 起始的登入)。

在某些應用程式中，應用程式的屬性中不提供需要指派使用者的選項。 在這些情況下，您可以使用 PowerShell 在服務主體上設定 appRoleAssignmentRequired 屬性。

### <a name="determining-the-user-experience-for-accessing-apps"></a>決定使用者存取應用程式的體驗

Azure AD 為組織中的終端使用者提供[幾種可自訂的應用程式部署方式](end-user-experiences.md)：

* Azure AD 我的應用程式存取面板
* Office 365 應用程式啟動程式
* 直接登入同盟應用程式 (service-pr)
* 同盟、密碼或現有應用程式的深層連結

您可以決定指派給企業應用程式的使用者，是否可以在存取面板和 Office 365 應用程式啟動器中看到此應用程式。

## <a name="example-complex-application-assignment-with-azure-ad"></a>範例：Azure AD 的複雜應用程式指派
考慮 Salesforce 之類的應用程式。 在許多組織中，Salesforce 主要是供行銷和銷售團隊使用。 通常，行銷小組的成員對 Salesforce 具有高特殊權限存取權，而銷售小組的成員具有限制存取權。 在許多情況下，為數眾多的資訊工作者對應用程式的存取都會受到限制。 這些規則的例外狀況會使情況更加複雜。 它通常是行銷或銷售領導小組的特權，用來授與使用者存取或在這些一般規則以外個別變更其角色。

利用 Azure AD，可以預先設定 Salesforce 之類的應用程式使用單一登入 (SSO) 和自動化佈建。 一旦設定了應用程式，系統管理員可以採取一次性動作來建立及指派適當的群組。 在此範例中，系統管理員可以執行下列指派：

* [動態群組](../fundamentals/active-directory-groups-create-azure-portal.md) ，以自動代表行銷和銷售小組的所有成員使用部門或角色之類的屬性：
  
  * 行銷群組的所有成員會全部指派給 Salesforce 中的「行銷」角色
  * 銷售小組群組的所有成員會全部指派給 Salesforce 中的「銷售」角色。 進一步細分可使用多個群組，其代表指派到不同 Saleforce 角色的區域銷售小組。

* 若要啟用例外狀況機制，可以為每個角色建立自助群組。 例如，可以將「Salesforce 行銷例外狀況」群組建立為自助群組。 群組可以指派給 Salesforce 行銷角色，而行銷領導小組則可成為擁有者。 行銷領導小組的成員可以加入或移除使用者、設定聯結原則或甚至核准或拒絕個別使用者加入的要求。 此機制透過不需要經過擁有者或成員特殊訓練的資訊工作者適當的體驗來支援。

在此情況下，所有指派的使用者會自動佈建至 Salesforce，因為當他們加入至不同群組時，他們的角色指派會在 Salesforce 中更新。 使用者就能夠透過 Microsoft 應用程式存取面板、Office Web 用戶端或甚至是藉由瀏覽至其組織的 Salesforce 登入頁面來探索及存取 Salesforce。 系統管理員能夠使用 Azure AD 報告輕鬆檢視使用方式和指派狀態。

管理員可以利用 [Azure AD 條件式存取](../conditional-access/concept-conditional-access-users-groups.md)，為特定角色設定存取原則。 這些原則可以包括是否允許公司環境外部的存取，甚至是 Multi-Factor Authentication 或裝置需求，以在各種情況下達成存取。

## <a name="access-to-microsoft-applications"></a>存取 Microsoft 應用程式

Microsoft 應用程式 (如 Office 365 Exchange、SharePoint、Yammer 等) 在指派與管理方面，比第三方 SaaS 應用程式或與 Azure AD 整合以單一登入的其他應用程式略為困難。

使用者有三種方式可取得 Microsoft 所發佈應用程式的存取權。

- 對於 Office 365 或其他付費套件中的應用程式，會透過**指派授權**直接指派至使用者帳戶，或使用我們的群組授權指派功能來透過群組，將存取權授與使用者。
- 對於 Microsoft 或第三方發佈給任何人免費使用的應用程式，使用者可能透過[使用者同意](configure-user-consent.md)而取得存取權。 這表示使用者可透過 Azure AD 公司或學校帳戶登入應用程式，並允許應用程式存取其帳戶中一組有限的資料。
- 對於 Microsoft 或第三方發佈給任何人免費使用的應用程式，使用者也可能透過[管理員同意](manage-consent-requests.md)而取得存取權。 這表示系統管理員已決定組織中的每個人都能使用該應用程式，因此系統管理員使用全域管理員帳戶身分登入應用程式，並將存取權授與組織中的每個人。

有些應用程式會結合這些方法。 例如，某些 Microsoft 應用程式雖然是 Office 365 訂用帳戶的一部分，但仍需要同意。

使用者可以透過 Office 365 入口網站存取 Office 365 應用程式。 您也可以在目錄的 [使用者設定] 中使用 [[Office 365 可見性切換]](hide-application-from-user-portal.md)，以決定在 **[我的應用程式]** 存取面板中顯示或隱藏 Office 365 應用程式。 

對於企業應用程式，您可以透過 Azure 入口網站，[指派使用者](assign-user-or-group-access-portal.md)給特定的 Microsoft 應用程式，或者，如果無法使用入口網站選項，您也可以透過 PowerShell 來指派。

## <a name="next-steps"></a>後續步驟
* [使用條件式存取來保護應用程式](../conditional-access/concept-conditional-access-cloud-apps.md)
* [自助式群組管理/SSAA](../users-groups-roles/groups-self-service-management.md)
