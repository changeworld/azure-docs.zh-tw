---
title: Azure Active Directory 入口網站中的稽核活動報告 | Microsoft Docs
description: 介紹 Azure Active Directory 入口網站中的稽核活動報告
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 09/17/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 994fe6c5005eeeab1399091dca5f72024fdd7262
ms.sourcegitcommit: 5abc3919a6b99547f8077ce86a168524b2aca350
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2020
ms.locfileid: "91812585"
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Azure Active Directory 入口網站中的稽核活動報告 

透過 Azure Active Directory (Azure AD) 報告，您可以取得判斷環境執行狀況所需的資訊。



報告架構由下列元件組成：

- **活動** 
    - **登入** – [登入報告](concept-sign-ins.md)會提供受控應用程式和使用者登入活動的使用情況相關資訊。
    - **稽核記錄** - 可針對各種功能在 Azure AD 內進行的所有變更，提供記錄追蹤功能。 稽核記錄的範例包括對 Azure AD 中任何資源所做的變更，像是新增或移除使用者、應用程式、群組、角色和原則。
    - 布建**記錄**  - 布建[記錄](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)可讓客戶透過布建服務監視活動，例如在 ServiceNow 中建立群組或從 Workday 匯入的使用者。 
- **Security** 
    - 有**風險**的登入：有[風險](../identity-protection/overview-identity-protection.md)的登入是指登入嘗試的指標，該嘗試可能是使用者帳戶的合法擁有者所執行。 
    - **標示為有風險的使用者** -有 [風險的使用者](../identity-protection/overview-identity-protection.md) 是指可能遭到盜用的使用者帳戶指標。

本文提供稽核報告的概觀。
 
## <a name="who-can-access-the-data"></a>誰可以存取資料？

* **安全性系統管理員**、**安全性讀取**者、**報告讀取**者、**全域讀取**者或**全域管理員**角色中的使用者

## <a name="audit-logs"></a>稽核記錄

Azure AD 稽核記錄會提供符合規範的系統活動記錄。 若要存取 audit 報告，請在**Azure Active Directory**的 [**監視**] 區段中選取 [ **audit 記錄**檔]。 



稽核記錄的預設清單檢視顯示︰

- 發生時間與日期
- 記錄發生次數的服務
- 活動的分類和名稱 (*什麼*)  
- 活動 (成功或失敗的狀態) 
- 目標
- 活動的啟動者/執行者 (對象)

![稽核記錄](./media/concept-audit-logs/listview.png "稽核記錄")

您可以按一下工具列中的 [資料 **行** ]，以自訂清單查看。

![Audit 資料行](./media/concept-audit-logs/columns.png "Audit 資料行")

這可讓您顯示其他欄位，或移除已顯示的欄位。

![移除欄位](./media/concept-audit-logs/columnselect.png "移除欄位")

選取清單檢視中的項目，即可取得更詳細的資訊。

![選取專案](./media/concept-audit-logs/details.png "選取專案")


## <a name="filtering-audit-logs"></a>篩選稽核記錄

您可以在下列欄位上篩選稽核資料：

- 服務
- 類別
- 活動
- 狀態
- 目標
- 啟動者 (執行者)
- 日期範圍

![篩選物件](./media/concept-audit-logs/filter.png "Filter 物件")

**服務**篩選器可讓您從下列服務的下拉式清單中選取：

- 全部
- AAD 管理 UX
- 存取權檢閱
- 帳戶佈建
- 應用程式 Proxy
- 驗證方法
- B2C
- 條件式存取
- 核心目錄
- 權利管理
- 混合式驗證
- 身分識別保護
- 受邀的使用者
- MIM 服務
- MyApps
- PIM
- 自助式群組管理
- 自助式密碼管理
- 使用規定

**類別目錄**篩選器可讓您選取下列其中一個篩選準則：

- 全部
- AdministrativeUnit
- ApplicationManagement
- 驗證
- 授權
- Contact
- 裝置
- DeviceConfiguration
- DirectoryManagement
- EntitlementManagement
- GroupManagement
- KerberosDomain
- KeyManagement
- 標籤
- 其他
- PermissionGrantPolicy
- 原則
- ResourceManagement
- RoleManagement
- Usermanagement program.cs

**活動**篩選是根據您所做的分類和活動資源類型選取專案。 您可以選取您想要查看的特定活動或選擇全部。 

您可以使用圖形 API 取得所有審核活動的清單： `https://graph.windows.net/<tenantdomain>/activities/auditActivityTypesV2?api-version=beta`

**狀態**篩選器可讓您根據審核操作的狀態進行篩選。 狀態可以是下列其中一項：

- 全部
- Success
- 失敗

**目標**篩選器可讓您依名稱或使用者主體名稱 (UPN) 的開頭來搜尋特定的目標。 目標名稱和 UPN 會區分大小寫。 

**由**篩選起始的可讓您定義動作專案的名稱或通用主體名稱 (UPN) 開頭。 名稱和 UPN 會區分大小寫。

**日期範圍**篩選器可讓您定義傳回資料的時間範圍。  
可能的值包括：

- 7 天
- 24 小時
- 自訂

當您選取自訂時間範圍時，可以設定開始時間和結束時間。

您也可以選取 [ **下載** ] 按鈕，選擇下載篩選過的資料，最多可達250000筆記錄。 您可以下載 CSV 或 JSON 格式的記錄檔。 您可以下載的記錄數目會受限於 [Azure Active Directory 報告保留原則](reference-reports-data-retention.md)。

![下載資料](./media/concept-audit-logs/download.png "下載資料")

## <a name="audit-logs-shortcuts"></a>稽核記錄快速鍵

除了 **Azure Active Directory** 之外，Azure 入口網站可提供您稽核資料的兩個額外進入點︰

- 使用者和群組
- 企業應用程式

### <a name="users-and-groups-audit-logs"></a>使用者和群組稽核記錄

透過以使用者和群組為基礎的稽核報告，可以取得下列問題的解答︰

- 使用者已套用哪些類型的更新？

- 有多少使用者已變更？

- 有多少密碼已變更？

- 系統管理員已在目錄中執行哪些作業？

- 已新增的群組為何？

- 群組有成員資格變更嗎？

- 群組的擁有者已變更嗎？

- 指派給群組或使用者的授權為何？

如果您只想要檢查與使用者相關的審核資料，您可以在 [**使用者**] 索引標籤的 [**監視**] 區段中，找到 [ **Audit 記錄**檔] 底下的篩選視圖。這個進入點已**usermanagement program.cs**為預先選取的類別。

![使用者](./media/concept-audit-logs/users.png "User")

如果您只想要檢查與群組相關的審核資料，您可以在 [**群組**] 索引標籤的 [**監視**] 區段中，找到 [ **Audit 記錄**檔] 底下的篩選視圖。這個進入點已**GroupManagement**為預先選取的類別。

![篩選群組](./media/concept-audit-logs/groups.png "篩選群組")

### <a name="enterprise-applications-audit-logs"></a>企業應用程式稽核記錄

透過以應用程式為基礎的稽核報告，可以取得下列問題的解答︰

* 已新增或更新的應用程式為何？
* 已移除的應用程式為何？
* 應用程式的服務原則已變更嗎？
* 應用程式的名稱已變更嗎？
* 誰已同意應用程式？

如果您想檢閱應用程式相關的稽核資料，可以在 [企業應用程式]**** 刀鋒視窗的 [活動]**** 區段中的 [稽核記錄]**** 之下找到篩選過的檢視。 這個進入點會將 **企業應用程式** 預先選取為 **應用程式類型**。

![企業應用程式](./media/concept-audit-logs/enterpriseapplications.png "企業應用程式")

## <a name="microsoft-365-activity-logs"></a>Microsoft 365 活動記錄

您可以從 [Microsoft 365 系統管理中心](/office365/admin/admin-overview/about-the-admin-center)中查看 Microsoft 365 的活動記錄。 雖然 Microsoft 365 活動和 Azure AD 活動記錄共用許多目錄資源，但只有 Microsoft 365 系統管理中心提供 Microsoft 365 活動記錄的完整觀點。 

您也可以使用 [Office 365 管理 api](/office/office-365-management-api/office-365-management-apis-overview)，以程式設計方式存取 Microsoft 365 活動記錄。

## <a name="next-steps"></a>下一步

- [Azure AD 稽核活動參考](reference-audit-activities.md)
- [Azure AD 報告保留參考](reference-reports-data-retention.md)
- [Azure AD 記錄延遲參考](reference-reports-latencies.md)
