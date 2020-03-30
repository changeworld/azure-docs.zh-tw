---
title: Azure 活動目錄治理操作參考指南
description: 此操作參考指南描述為確保治理管理而應採取的檢查和操作
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: 4826bcdc85e0c6189c51aa262014fe154bb479b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74535453"
---
# <a name="azure-active-directory-governance-operations-reference-guide"></a>Azure 活動目錄治理操作參考指南

Azure AD[操作參考指南](active-directory-ops-guide-intro.md)的這一部分介紹了為評估和證明授予的非特權和特權標識、審核和控制對環境的更改而應執行的檢查和操作。

> [!NOTE]
> 這些建議自發布之日起是最新的，但可能會隨時間而變化。 隨著 Microsoft 產品和服務隨時間推移而變化，組織應持續評估其治理實踐。

## <a name="key-operational-processes"></a>關鍵操作流程

### <a name="assign-owners-to-key-tasks"></a>將擁有者分配給要徑任務

管理 Azure 活動目錄需要持續執行關鍵操作任務和進程，這些任務和進程可能不是推出專案的一部分。 設置這些任務以優化環境仍然很重要。 要徑任務及其推薦的擁有者包括：

| Task | 擁有者 |
| :- | :- |
| 在 SIEM 系統中存檔 Azure AD 稽核記錄 | InfoSec 運營團隊 |
| 發現不符合合規性的應用程式 | IAM 運營團隊 |
| 定期審查對應用程式的訪問 | InfoSec 架構團隊 |
| 定期審查對外部身份的訪問 | InfoSec 架構團隊 |
| 定期審查誰擁有特權角色 | InfoSec 架構團隊 |
| 定義安全門以啟動特權角色 | InfoSec 架構團隊 |
| 定期審查同意授予 | InfoSec 架構團隊 |
| 為組織中的員工設計應用程式和資源的目錄和訪問包 | 應用擁有者 |
| 定義安全性原則以將使用者分配給訪問包 | InfoSec 團隊 + 應用程式擁有者 |
| 如果策略包括審批工作流，則定期審核工作流審批 | 應用擁有者 |
| 使用訪問審核查看安全性原則中的異常，如條件訪問策略 | InfoSec 運營團隊 |

在查看清單時，您可能會發現需要為缺少擁有者的任務分配擁有者，或者調整擁有者與上述建議不一致的任務的擁有權。

#### <a name="owner-recommended-reading"></a>業主推薦閱讀

- [在 Azure Active Directory 中指派系統管理員角色](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Azure 中的治理](https://docs.microsoft.com/azure/security/governance-in-azure)

### <a name="configuration-changes-testing"></a>配置更改測試

在測試時需要特殊考慮的更改，從簡單的技術（如推出目標使用者子集）到在並行測試租戶中部署更改。 如果尚未實施測試策略，則應根據下表中的指南定義測試方法：

| 狀況| 建議 |
|-|-|
|將身份驗證類型從聯合更改為小到小到條/PTA，反之亦然| 使用[暫存卷展欄](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-staged-rollout)測試更改身份驗證類型的影響。|
|推出新的條件訪問 （CA） 策略或身份保護原則|創建新的 CA 策略並分配給測試使用者。|
|加入應用程式的測試環境|將應用程式添加到生產環境，將其從 MyApps 面板中隱藏，並將其分配給品質保證 （QA） 階段中的測試使用者。|
|更改同步規則|在測試中執行 Azure AD 使用當前正在生產的配置（也稱為暫存模式）進行更改，並分析 CSExport 結果。 如果滿足，則在準備就緒時交換到生產。|
|改變品牌|在單獨的測試租戶中進行測試。|
|推出新功能|如果該功能支援向目標使用者集推出，請確定試點使用者並進行構建。例如，自助服務密碼重設和多重要素驗證可以針對特定使用者或組。|
|將應用程式從本地標識提供程式 （IdP，例如活動目錄）到 Azure AD 進行剪切|如果應用程式支援多個 IdP 配置（例如 Salesforce），則在更改視窗期間配置和測試 Azure AD（如果應用程式引入了 HRD 頁）。 如果應用程式不支援多個 IdP，請安排更改控制視窗期間的測試和程式停機時間。|
|更新動態組規則|使用新規則創建並行動態組。 與計算的結果進行比較，例如，運行具有相同條件的 PowerShell。<br>如果測試通過，則交換使用舊組的位置（如果可行）。|
|遷移產品許可證|請參閱[更改 Azure 活動目錄中許可組中的單個使用者的許可證](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-change-licenses)。|
|更改 AD FS 規則，如授權、頒發、MFA|使用組聲明來定位使用者子集。|
|更改 AD FS 身份驗證體驗或類似伺服器場範圍更改|創建具有相同主機名稱的並行伺服器場、實現配置更改、使用 HOSTS 檔、NLB 路由規則或類似路由從用戶端進行測試。<br>如果目標平臺不支援 HOSTS 檔（例如行動裝置），則控制更改。|

## <a name="access-reviews"></a>存取權檢閱

### <a name="access-reviews-to-applications"></a>訪問應用程式審核

隨著時間的推移，使用者可能會在不同團隊和職位中移動時累積對資源的存取權限。 重要的是，資源擁有者定期查看對應用程式的存取權限，並刪除使用者整個生命週期中不再需要的許可權。 Azure AD[訪問審核](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)使組織能夠高效地管理組成員身份、訪問企業應用程式和角色指派。 資源擁有者應定期查看使用者的存取權限，以確保只有合適的人員才能繼續訪問。 理想情況下，應考慮為此任務使用 Azure AD 訪問審核。

![訪問評論起始頁](./media/active-directory-ops-guide/active-directory-ops-img15.png)

> [!NOTE]
> 與訪問審核交互的每個使用者都必須具有付費的 Azure AD 高級 P2 許可證。

### <a name="access-reviews-to-external-identities"></a>訪問外部標識的審核

在需要的時間內，保持對外部標識的訪問僅限於所需的資源，這一點至關重要。 使用 Azure AD[訪問審核](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)為所有外部標識和應用程式訪問建立常規的自動訪問審核過程。 如果進程已在本地存在，請考慮使用 Azure AD 訪問審核。 一旦應用程式停用或不再使用，請刪除有權訪問該應用程式的所有外部標識。

> [!NOTE]
> 與訪問審核交互的每個使用者都必須具有付費的 Azure AD 高級 P2 許可證。

## <a name="privileged-account-management"></a>具有特殊權限的帳戶管理

### <a name="privileged-account-usage"></a>特權帳戶使用

駭客通常以管理員帳戶和特權訪問的其他元素為目標，以便快速訪問敏感性資料和系統。由於具有特權角色的使用者往往會隨著時間的推移而累積，因此定期查看和管理管理員存取權限並提供對 Azure AD 和 Azure 資源的即時特權訪問非常重要。

如果您的組織中不存在管理特權帳戶的進程，或者您當前有使用其常規使用者帳戶管理服務和資源的管理員，則應立即開始使用單獨的帳戶，例如，用於常規日常帳戶活動;另一個用於特權訪問，並配置 MFA。 更妙的是，如果您的組織具有 Azure AD 高級 P2 訂閱，則應立即部署[Azure AD 特權標識管理](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure#license-requirements)（PIM）。 出於同一權杖，您還應查看這些特權帳戶，並[分配特權較低的角色](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure)（如果適用）。

應實現的特權帳戶管理的另一個方面是手動或通過[PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-how-to-perform-security-review)定義這些帳戶[的訪問審核](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)。

#### <a name="privileged-account-management-recommended-reading"></a>特權帳戶管理推薦閱讀

- [Azure AD Privileged Identity Management 中的角色](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-roles)

### <a name="emergency-access-accounts"></a>緊急存取帳戶

組織必須創建[緊急帳戶](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access)，以便為身份驗證中斷等情況管理 Azure AD：

- 身份驗證基礎結構的中斷元件（AD FS、本地 AD、MFA 服務）
- 行政人員更替

為了防止由於無法以管理員身份登錄或啟動現有個人使用者帳戶而無意中被鎖定在租戶之外，應創建兩個或多個緊急帳戶，並確保這些帳戶已實現並與[Microsoft 的最佳做法](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure)保持一致，並[打破玻璃程式](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure#break-glass-what-to-do-in-an-emergency)。

### <a name="privileged-access-to-azure-ea-portal"></a>對 Azure EA 門戶的特權訪問

[Azure 企業協定 （Azure EA） 門戶](https://azure.microsoft.com/blog/create-enterprise-subscription-experience-in-azure-portal-public-preview/)使您能夠根據主企業協定創建 Azure 訂閱，這是企業內部的強大角色。 在將 Azure AD 放在原位之前，通常先引導創建此門戶，因此有必要使用 Azure AD 標識將其鎖定、從門戶中刪除個人帳戶、確保已正確委派到位並降低鎖定風險.

要明確，如果 EA 門戶授權級別當前設置為"混合模式"，則必須從 EA 門戶中的所有特權訪問中刪除任何[Microsoft 帳戶](https://support.skype.com/en/faq/FA12059/what-is-a-microsoft-account)，並將 EA 門戶配置為僅使用 Azure AD 帳戶。 如果未配置 EA 門戶委派的角色，則還應查找和實現部門和帳戶的委派角色。

#### <a name="privileged-access-recommended-reading"></a>推薦讀取特權訪問

- [Azure Active Directory 中的系統管理員角色權限](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

## <a name="entitlement-management"></a>權利管理

[授權管理 （EM）](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview)允許應用擁有者捆綁資源並將其分配給組織中的特定角色（內部和外部）。 EM 允許自助服務註冊和委派給業務擁有者，同時保持治理策略以授予存取權限、設置訪問持續時間和允許審批工作流。 

> [!NOTE]
> Azure AD 授權管理需要 Azure AD 高級 P2 許可證。

## <a name="summary"></a>總結

安全標識治理有八個方面。 此清單將説明您確定應執行的操作，以評估和證明授予非特權和特權標識、審核和控制對環境更改的存取權限。

- 將擁有者分配給要徑任務。
- 實施測試策略。
- 使用 Azure AD 訪問審核可高效地管理組成員身份、對企業應用程式的訪問和角色指派。
- 為所有類型的外部身份和應用程式訪問建立定期、自動化的訪問審核流程。
- 建立訪問審核流程，定期查看和管理管理員存取權限，並提供對 Azure AD 和 Azure 資源的及時特權訪問。
- 預配緊急帳戶，以便為意外中斷管理 Azure AD 做好準備。
- 鎖定對 Azure EA 門戶的訪問。
- 實現授權管理，以提供對資源集合的受治理訪問。

## <a name="next-steps"></a>後續步驟

開始使用[Azure AD 操作檢查和操作](active-directory-ops-guide-ops.md)。
