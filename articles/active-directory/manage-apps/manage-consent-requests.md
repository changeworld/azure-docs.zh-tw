---
title: 在 Azure Active Directory 中管理應用程式的同意和評估同意要求
description: 瞭解如何在使用者同意已停用或限制時管理同意要求，以及如何在 Azure Active Directory 中評估租使用者的系統管理員同意要求。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 12/27/2019
ms.author: kenwith
ms.reviewer: phsignor
ms.openlocfilehash: 3d95d2551f8e078f4252a19dc850345793c040d8
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2020
ms.locfileid: "89420450"
---
# <a name="managing-consent-to-applications-and-evaluating-consent-requests"></a>管理應用程式的同意和評估同意要求

Microsoft [建議](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#restrict-user-consent-operations) 停用使用者對應用程式的同意。 這會將決策制定程式集中在貴組織的安全性和身分識別管理員團隊。

在停用或限制使用者同意之後，有幾個重要的考慮可確保您的組織保持安全，同時仍然允許使用商務關鍵應用程式。 這些步驟非常重要，可將對貴組織的支援小組和 IT 系統管理員的影響降到最低，同時避免在協力廠商應用程式中使用未受管理的帳戶。

## <a name="process-changes-and-education"></a>流程變更和教育

 1. 請考慮啟用 [ (preview) 的系統管理員同意工作流程 ](configure-admin-consent-workflow.md) ，讓使用者直接從同意畫面要求系統管理員核准。

 2. 確定所有系統管理員都瞭解 [許可權和同意架構](../develop/consent-framework.md)、 [同意提示](../develop/application-consent-experience.md) 的運作方式，以及如何 [評估租使用者系統管理員同意的要求](#evaluating-a-request-for-tenant-wide-admin-consent)。
 3. 檢查您組織的現有進程，讓使用者要求系統管理員核准應用程式，並視需要進行更新。 如果處理常式已變更：
    * 更新相關的檔、監視、自動化等等。
    * 將流程變更傳達給所有受影響的使用者、開發人員、支援小組和 IT 系統管理員。

## <a name="auditing-and-monitoring"></a>稽核與監視

1. 在您的組織中[審核應用程式並授與許可權](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#audit-apps-and-consented-permissions)，以確保之前沒有任何預期或可疑的應用程式存取資料。

2. 針對要求 OAuth 同意的可疑應用程式，請參閱 [Office 365 中的偵測和補救非法同意](https://docs.microsoft.com/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants) 授與，以取得額外的最佳作法和防護措施。

3. 如果您的組織具有適當的授權：

    * 使用 [Microsoft Cloud App Security 中的額外 OAuth 應用程式審核功能](https://docs.microsoft.com/cloud-app-security/investigate-risky-oauth)。
    * 使用 [Azure 監視器活頁簿來監視許可權，並同意](../reports-monitoring/howto-use-azure-monitor-workbooks.md) 相關的活動。 *同意深入*解析活頁簿會依失敗的同意要求數目來提供應用程式的觀點。 這有助於讓系統管理員檢查應用程式的優先順序，並決定是否要授與他們系統管理員同意。

### <a name="additional-considerations-for-reducing-friction"></a>減少摩擦的其他考慮

若要將對已在使用中之受信任的商務關鍵應用程式的影響降至最低，請考慮主動將管理員同意授與具有大量使用者同意授權的應用程式：

1. 根據登入記錄或同意授與活動，清查已新增至您組織的應用程式，並以高使用量進行。 您可以使用 PowerShell [腳本](https://gist.github.com/psignoret/41793f8c6211d2df5051d77ca3728c09) 來快速且輕鬆地探索具有大量使用者同意授與的應用程式。

2. 評估尚未授與系統管理員同意的熱門應用程式。

   > [!IMPORTANT]
   > 在授與整個租使用者的系統管理員同意之前，請仔細評估應用程式，即使組織中有許多使用者已經同意。

3. 針對每個已核准的應用程式，使用下列其中一種方法來授與整個租使用者的系統管理員同意。

4. 針對每個已核准的應用程式，請考慮 [限制使用者存取](configure-user-consent.md)。

## <a name="evaluating-a-request-for-tenant-wide-admin-consent"></a>評估租使用者的系統管理員同意要求

授與整個租使用者的系統管理員同意是敏感性作業。  系統會代表整個組織授與許可權，並可包含嘗試高度特殊許可權作業的許可權。 例如，角色管理、所有信箱或所有網站的完整存取權，以及完整的使用者模擬。

在授與整個租使用者的系統管理員同意之前，您必須確定您信任應用程式和應用程式發行者，以取得您要授與的存取層級。 如果您不知道誰會控制應用程式，以及應用程式要求許可權的原因， *請勿授與同意*。

下列清單提供評估要求以授與系統管理員同意時所需考慮的一些建議。

* **瞭解 Microsoft 身分識別平臺中的 [許可權和同意架構](../develop/consent-framework.md) 。**

* **瞭解 [委派的許可權和應用程式許可權](../develop/v2-permissions-and-consent.md#permission-types)之間的差異。**

   應用程式許可權可讓應用程式存取整個組織的資料，而不需要任何使用者互動。 委派的許可權可讓應用程式代表在某個時間點登入應用程式的使用者執行動作。

* **瞭解所要求的許可權。**

   應用程式所要求的許可權會列在 [同意提示](../develop/application-consent-experience.md)中。 展開許可權標題將會顯示許可權的描述。 應用程式許可權的描述通常會以「沒有登入的使用者」結尾。 委派許可權的描述一般會以「代表已登入使用者的身分」結尾。 Microsoft Graph API 的許可權描述于 [Microsoft Graph 許可權參考](https://docs.microsoft.com/graph/permissions-reference) 中-請參閱其他 api 的檔，以瞭解其公開的許可權。

   如果您不了解要求的許可權， *請勿授與同意*。

* **瞭解哪些應用程式正在要求許可權，以及發佈應用程式的人員。**

   請小心，惡意應用程式嘗試看起來像其他應用程式。

   如果您不確定應用程式或其發行者的合法性， *請勿授與同意*。 相反地，請尋找其他確認 (例如，直接從應用程式發行者) 。

* **確定所要求的許可權與您預期從應用程式中的功能一致。**

   例如，提供 SharePoint 網站管理的應用程式可能需要委派存取權才能讀取所有網站集合，但不一定需要完整存取目錄中的所有信箱或完整模擬許可權。

   如果您懷疑應用程式要求的許可權超過其所需的許可權， *請勿授與同意*。 請聯絡應用程式發行者以取得更多詳細資料。

## <a name="granting-consent-as-an-administrator"></a>以系統管理員身分授予同意

### <a name="granting-tenant-wide-admin-consent"></a>授與整個租使用者的系統管理員同意
請參閱將租使用者的系統 [管理員同意授與應用程式](grant-admin-consent.md) ，以取得從 Azure 入口網站、使用 Azure AD PowerShell 或從同意提示本身授與租使用者系統管理員同意的逐步指示。

### <a name="granting-consent-on-behalf-of-a-specific-user"></a>代表特定使用者授與同意
系統管理員也可以使用 [MICROSOFT GRAPH API](https://docs.microsoft.com/graph/use-the-api) 來代表單一使用者同意委派的許可權，而不是授與整個組織的同意。 如需詳細資訊，請參閱 [代表使用者取得存取權](https://docs.microsoft.com/graph/auth-v2-user)。

## <a name="limiting-user-access-to-applications"></a>限制使用者對應用程式的存取
即使已授與租使用者的系統管理員同意，使用者對應用程式的存取仍會受到限制。 如需如何要求將使用者指派給應用程式的詳細資訊，請參閱 [指派使用者和群組的方法](methods-for-assigning-users-and-groups.md)。

如需更廣泛的總覽，包括如何處理其他複雜的案例，請參閱 [使用 Azure AD 進行應用程式存取管理](what-is-access-management.md)。

## <a name="disable-all-future-user-consent-operations-to-any-application"></a>停用任何應用程式的所有未來使用者同意作業
停用整個目錄的使用者同意會阻止使用者同意任何應用程式。 系統管理員仍然可以代表使用者同意。 若要進一步了解應用程式同意，以及您同意與否的可能原因，請參閱[了解使用者和系統管理員同意](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview)。

若要停用整個目錄中的所有未來使用者同意作業，請遵循下列步驟：
1.  開啟 [Azure 入口網站](https://portal.azure.com/)，以**全域管理員**身分登入。
2.  按一下左側主導覽功能表底部的 [所有服務]，以開啟 [Azure Active Directory 延伸模組]。
3.  在篩選搜尋方塊中輸入 **“Azure Active Directory**”，然後選取 [Azure Active Directory] 項目。
4.  選取導覽功能表中的 [ **使用者和群組** ]。
5.  選取 [ **使用者設定**]。
6.  將 [使用者可以允許應用程式存取其資料]**** 切換開關設為 [否]****，並按一下 [儲存]**** 按鈕，以停用所有未來的使用者同意作業。

## <a name="next-steps"></a>接下來的步驟
* [可保護身分識別基礎結構的五個步驟](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#before-you-begin-protect-privileged-accounts-with-mfa)
* [設定管理員同意工作流程](configure-admin-consent-workflow.md)
* [設定使用者同意應用程式的方式](configure-user-consent.md)
* [Microsoft 身分識別平台中的權限和同意](../develop/active-directory-v2-scopes.md)
