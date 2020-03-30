---
title: 管理對應用程式的同意和評估同意請求 - Azure AD
description: 瞭解如何在禁用或限制使用者同意時管理同意請求，以及如何評估租戶範圍管理員同意應用程式的請求。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77367843"
---
# <a name="managing-consent-to-applications-and-evaluating-consent-requests"></a>管理對申請的同意和評估同意請求

Microsoft[建議](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#restrict-user-consent-operations)禁用最終使用者對應用程式的同意。 這將將決策過程集中到組織的安全和身份管理員團隊。

在禁用或限制最終使用者同意後，有幾個重要注意事項可確保您的組織保持安全，同時仍允許使用關鍵商務應用程式。 這些步驟對於最大限度地減少對組織的支援小組和 IT 管理員的影響至關重要，同時防止在協力廠商應用程式中使用非託管帳戶。

## <a name="process-changes-and-education"></a>流程變更和教育

 1. 請考慮啟用[管理員同意工作流（預覽），](configure-admin-consent-workflow.md)以允許使用者直接從同意螢幕要求管理員批准。

 2. 確保所有管理員瞭解[許可權和同意框架](../develop/consent-framework.md)、[同意提示](../develop/application-consent-experience.md)如何工作，以及如何[評估租戶範圍管理員同意的請求](#evaluating-a-request-for-tenant-wide-admin-consent)。
 3. 查看組織的現有流程，以便使用者要求管理員批准應用程式，並在必要時進行更新。 如果進程發生更改：
    * 更新相關文檔、監視、自動化等。
    * 將流程更改傳達給所有受影響的使用者、開發人員、支援小組和 IT 管理員。

## <a name="auditing-and-monitoring"></a>稽核與監視

1. [審核應用中並授予組織中的許可權](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#audit-apps-and-consented-permissions)，以確保以前沒有授予任何不必要的或可疑的應用程式對資料的訪問。

2. 查看[Office 365 中的"檢測和補救非法同意授予"，](https://docs.microsoft.com/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants)以針對請求 OAuth 同意的可疑應用程式的其他最佳做法和保障措施。

3. 如果您的組織具有適當的許可證：

    * 在[微軟雲應用程式安全中使用其他 OAuth 應用程式審核功能](https://docs.microsoft.com/cloud-app-security/investigate-risky-oauth)。
    * 使用[Azure 監視器活頁簿監視許可權和同意](../reports-monitoring/howto-use-azure-monitor-workbooks.md)相關活動。 "*同意見解"* 活頁簿按失敗的同意請求數量提供應用視圖。 這有助於確定管理員查看和決定是否授予其管理員同意的應用程式的優先順序。

### <a name="additional-considerations-for-reducing-friction"></a>減少摩擦的其他注意事項

為了儘量減少對已使用的業務關鍵型應用程式的影響，請考慮主動向具有大量使用者同意授予的應用程式授予管理員同意：

1. 根據登錄日誌或同意授予活動，對已添加到組織中的使用率高的應用進行盤點。 PowerShell[腳本](https://gist.github.com/psignoret/41793f8c6211d2df5051d77ca3728c09)可用於快速輕鬆地發現具有大量使用者同意授予的應用程式。

2. 評估尚未獲得管理員同意的頂級應用程式。

   > [!IMPORTANT]
   > 在授予租戶範圍的管理同意之前，請仔細評估應用程式，即使組織中的許多使用者已自行同意。

3. 對於已批准的每個應用程式，請使用下面記錄的方法之一授予租戶範圍的管理同意。

4. 對於每個已批准的應用程式，請考慮[限制使用者訪問](configure-user-consent.md)。

## <a name="evaluating-a-request-for-tenant-wide-admin-consent"></a>評估租戶範圍的管理同意請求

授予租戶範圍的管理同意是一項敏感的操作。  許可權將代表整個組織授予，並可以包括嘗試高特權操作的許可權。 例如，角色管理、對所有郵箱或所有網站的完全存取權限以及完全使用者類比。

在授予租戶範圍的管理同意之前，必須確保信任應用程式和應用程式發行者，以便獲得您授予的存取層級。 如果您不確定您瞭解誰控制了應用程式以及應用程式請求許可權的原因，*請不要授予同意*。

以下清單提供了一些建議，在評估授予管理員同意的請求時需要考慮。

* **瞭解 Microsoft 標識平臺中[的許可權和同意框架](../develop/consent-framework.md)。**

* **瞭解[委派許可權和應用程式許可權](../develop/v2-permissions-and-consent.md#permission-types)之間的區別。**

   應用程式許可權允許應用程式訪問整個組織的資料，而無需任何使用者交互。 委派的許可權允許應用程式代表在某個時候登錄到應用程式的使用者執行操作。

* **瞭解請求的許可權。**

   應用程式請求的許可權列在[同意提示](../develop/application-consent-experience.md)中。 展開許可權標題將顯示許可權的說明。 應用程式許可權的描述通常以"沒有登錄使用者"結尾。 委派許可權的說明通常以"代表登錄使用者"結尾。 在 [Microsoft 圖形許可權參考] 中描述了 Microsoft 圖形 API 的許可權 - 請參閱其他 API 的文檔以瞭解它們公開的許可權。

   如果您不理解請求的許可權，*請不要同意*。

* **瞭解請求許可權的應用程式以及誰發佈了該應用程式。**

   警惕試圖看起來像其他應用程式的惡意應用程式。

   如果您懷疑申請或其發行者的合法性，*請不要同意*。 而是尋求其他確認（例如，直接從應用程式發行者）。

* **確保請求的許可權與應用程式所需的功能保持一致。**

   例如，提供 SharePoint 網站管理的應用程式可能需要委派存取權限才能讀取所有網站集，但不一定需要對所有郵箱的完全存取權限或目錄中的完整類比許可權。

   如果您懷疑應用程式請求的許可權超過其需要的許可權，*請不要授予同意*。 請與應用程式發行者聯繫以獲取更多詳細資訊。

## <a name="granting-consent-as-an-administrator"></a>授予管理員同意

### <a name="granting-tenant-wide-admin-consent"></a>授予租戶範圍的管理同意

有關從 Azure 門戶、使用 Azure AD PowerShell 或同意提示器本身授予租戶範圍管理員同意的分步說明，請參閱[授予租戶範圍管理員同意](grant-admin-consent.md)。

### <a name="granting-consent-on-behalf-of-a-specific-user"></a>代表特定使用者授予同意

管理員還可以使用[Microsft 圖形 API](https://docs.microsoft.com/graph/use-the-api)代表單個使用者授予委派許可權的同意，而不是授予整個組織的同意。 有關詳細資訊，請參閱[代表使用者獲取存取權限](https://docs.microsoft.com/graph/auth-v2-user)。

## <a name="limiting-user-access-to-applications"></a>限制使用者訪問應用程式

即使已授予租戶範圍的管理同意，使用者對應用程式的存取權限仍可能受到限制。 有關如何要求使用者分配到應用程式的詳細資訊，請參閱[分配使用者和組的方法](methods-for-assigning-users-and-groups.md)。

有關更廣泛的概述，包括如何處理其他複雜方案，請參閱使用 Azure [AD 進行應用程式訪問管理](what-is-access-management.md)。

## <a name="next-steps"></a>後續步驟

[可保護身分識別基礎結構的五個步驟](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#before-you-begin-protect-privileged-accounts-with-mfa)

[建構管理員同意工作流](configure-admin-consent-workflow.md)

[配置最終使用者如何同意應用程式](configure-user-consent.md)

[Microsoft 身份平臺中的許可權和同意](../develop/active-directory-v2-scopes.md)

[堆疊溢位上的 Azure AD](https://stackoverflow.com/questions/tagged/azure-active-directory)