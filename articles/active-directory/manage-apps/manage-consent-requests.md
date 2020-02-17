---
title: 管理應用程式的同意及評估同意要求-Azure AD
description: 瞭解如何在使用者同意已停用或受到限制時管理同意要求，以及如何評估對應用程式的租使用者系統管理員同意要求。
services: active-directory
author: psignoret
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/27/2019
ms.author: mimart
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0451fe18629a572c9b49f14924bfa50293f42a2b
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/15/2020
ms.locfileid: "77367843"
---
# <a name="managing-consent-to-applications-and-evaluating-consent-requests"></a>管理應用程式的同意及評估同意要求

Microsoft[建議](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#restrict-user-consent-operations)停用應用程式的終端使用者同意。 這會讓您組織的安全性和身分識別管理員小組進行決策制定程式。

在停用或限制使用者同意之後，有幾個重要的考慮可確保您的組織保持安全，同時仍然允許使用業務關鍵應用程式。 這些步驟非常重要，可將對組織支援小組和 IT 系統管理員的影響降至最低，同時防止在協力廠商應用程式中使用非受控帳戶。

## <a name="process-changes-and-education"></a>處理常式變更與教育

 1. 請考慮啟用[管理員同意工作流程（預覽）](configure-admin-consent-workflow.md) ，讓使用者直接從同意畫面要求系統管理員核准。

 2. 請確定所有系統管理員都瞭解[許可權和同意架構](../develop/consent-framework.md)、[同意提示](../develop/application-consent-experience.md)的運作方式，以及如何[評估整個租使用者系統管理員同意的要求](#evaluating-a-request-for-tenant-wide-admin-consent)。
 3. 檢查您組織的現有進程，讓使用者要求應用程式的系統管理員核准，並視需要進行更新。 如果處理常式已變更：
    * 更新相關的檔、監視、自動化等等。
    * 將程式變更傳達給所有受影響的使用者、開發人員、支援小組和 IT 系統管理員。

## <a name="auditing-and-monitoring"></a>審核和監視

1. [審核應用程式並授](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#audit-apps-and-consented-permissions)與您組織中的許可權，以確保先前沒有任何預期或可疑的應用程式被授與資料的存取權。

2. 請參閱偵測[並補救 Office 365 中違法的同意](https://docs.microsoft.com/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants)授與，以取得其他最佳作法，並保護要求 OAuth 同意的可疑應用程式。

3. 如果您的組織具有適當的授權：

    * [在 Microsoft Cloud App Security 中使用其他的 OAuth 應用程式審核功能](https://docs.microsoft.com/cloud-app-security/investigate-risky-oauth)。
    * 使用[Azure 監視器活頁簿來監視許可權和同意](../reports-monitoring/howto-use-azure-monitor-workbooks.md)相關的活動。 *同意深入*解析活頁簿會依失敗的同意要求數來提供應用程式的觀點。 這有助於設定應用程式的優先順序，讓系統管理員能夠檢查並決定是否要授與系統管理員同意。

### <a name="additional-considerations-for-reducing-friction"></a>減少摩擦的其他考慮

若要將已在使用中之受信任的商務關鍵應用程式的影響降至最低，請考慮主動授與系統管理員對具有大量使用者同意授與的應用程式的同意：

1. 根據登入記錄或同意授與活動，清查已新增至您組織的應用程式，並具有高使用量。 PowerShell[腳本](https://gist.github.com/psignoret/41793f8c6211d2df5051d77ca3728c09)可以用來快速且輕鬆地探索具有大量使用者同意授與的應用程式。

2. 評估尚未授與系統管理員同意的前幾名應用程式。

   > [!IMPORTANT]
   > 請先仔細評估應用程式，再授與整個租使用者的系統管理員同意，即使組織中有許多使用者已同意自己也一樣。

3. 針對每個已核准的應用程式，使用以下所述的其中一種方法，授與整個租使用者的系統管理員同意。

4. 針對每個核准的應用程式，請考慮[限制使用者存取](configure-user-consent.md)。

## <a name="evaluating-a-request-for-tenant-wide-admin-consent"></a>評估整個租使用者系統管理員同意的要求

授與整個租使用者的系統管理員同意是一種敏感性作業。  系統會代表整個組織授與許可權，並可包含嘗試高許可權作業的許可權。 例如，角色管理、所有信箱或所有網站的完整存取權，以及完整的使用者模擬。

在授與全租使用者系統管理員同意之前，您必須確定您信任應用程式和應用程式發行者，以取得您所授與的存取層級。 如果您不確信您瞭解控制應用程式的人員，以及應用程式要求許可權的原因，請不要*授與同意*。

下列清單提供在評估要求以授與系統管理員同意時要考慮的一些建議。

* **瞭解 Microsoft 身分識別平臺中的[許可權和同意架構](../develop/consent-framework.md)。**

* **瞭解[委派許可權和應用程式許可權](../develop/v2-permissions-and-consent.md#permission-types)之間的差異。**

   應用程式許可權可讓應用程式存取整個組織的資料，而不需要任何使用者互動。 委派的許可權可讓應用程式代表在某個時間點登入應用程式的使用者執行動作。

* **瞭解所要求的許可權。**

   應用程式所要求的許可權會列在[同意提示](../develop/application-consent-experience.md)中。 展開許可權標題將會顯示許可權的描述。 應用程式許可權的描述一般會以「沒有登入的使用者」結束。 委派許可權的描述一般會以「代表登入的使用者」結尾。 [Microsoft Graph 許可權參考] 中說明 Microsoft Graph API 的許可權-請參閱其他 Api 的檔，以瞭解它們所公開的許可權。

   如果您不了解所要求的許可權，請不要*授與同意*。

* **瞭解哪一個應用程式正在要求許可權，以及發行應用程式的物件。**

   請小心嘗試看起來像其他應用程式的惡意應用程式。

   如果您不確定應用程式或其發行者的合法性，請不要*授與同意*。 相反地，請改為尋找額外的確認（例如，直接從應用程式發行者）。

* **請確定要求的許可權與您在應用程式中預期的功能一致。**

   例如，提供 SharePoint 網站管理的應用程式可能需要委派存取權才能讀取所有網站集合，但不一定需要所有信箱的完整存取權，或目錄中的完整模擬許可權。

   如果您懷疑應用程式要求的許可權比所需的更多，請不要*授與同意*。 請洽詢應用程式發行者以取得更多詳細資料。

## <a name="granting-consent-as-an-administrator"></a>以系統管理員身分授與同意

### <a name="granting-tenant-wide-admin-consent"></a>授與全租使用者系統管理員同意

請參閱將租使用者系統[管理員同意授與應用程式](grant-admin-consent.md)，以取得逐步指示，說明如何使用 Azure AD PowerShell 或從同意提示本身，將租使用者的系統管理員同意授予 Azure 入口網站。

### <a name="granting-consent-on-behalf-of-a-specific-user"></a>代表特定使用者授與同意

系統管理員也可以使用[Microsft 圖形 API](https://docs.microsoft.com/graph/use-the-api) ，代表單一使用者同意委派的許可權，而不是授與整個組織的同意。 如需詳細資訊，請參閱[代表使用者取得存取權](https://docs.microsoft.com/graph/auth-v2-user)。

## <a name="limiting-user-access-to-applications"></a>限制使用者對應用程式的存取

即使已授與租使用者範圍的系統管理員同意，使用者對應用程式的存取仍然會受到限制。 如需有關如何要求將使用者指派給應用程式的詳細資訊，請參閱[指派使用者和群組的方法](methods-for-assigning-users-and-groups.md)。

如需更廣泛的總覽，包括如何處理其他複雜的案例，請參閱[使用 Azure AD 進行應用程式存取管理](what-is-access-management.md)。

## <a name="next-steps"></a>後續步驟

[可保護身分識別基礎結構的五個步驟](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#before-you-begin-protect-privileged-accounts-with-mfa)

[設定管理員同意工作流程](configure-admin-consent-workflow.md)

[設定終端使用者同意應用程式的方式](configure-user-consent.md)

[Microsoft 身分識別平臺中的許可權和同意](../develop/active-directory-v2-scopes.md)

[StackOverflow 上的 Azure AD](https://stackoverflow.com/questions/tagged/azure-active-directory)