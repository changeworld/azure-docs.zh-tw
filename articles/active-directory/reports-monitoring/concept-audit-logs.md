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
ms.date: 07/17/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad399fc24b2cdfbdc51e7feccba2c05786216b19
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79253230"
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Azure Active Directory 入口網站中的稽核活動報告 

透過 Azure Active Directory (Azure AD) 報告，您可以取得判斷環境執行狀況所需的資訊。

報告架構由下列元件組成：

- **活動** 
    - **登入** – [登入報告](concept-sign-ins.md)會提供受控應用程式和使用者登入活動的使用情況相關資訊。
    - **稽核記錄** - 可針對各種功能在 Azure AD 內進行的所有變更，提供記錄追蹤功能。 稽核記錄的範例包括對 Azure AD 中任何資源所做的變更，像是新增或移除使用者、應用程式、群組、角色和原則。
- **安全性** 
    - 有**風險**的登入-有風險的登[入](concept-risky-sign-ins.md)表示登入嘗試，這可能是因為不是使用者帳戶合法擁有者的人所執行。 
    - **標示有風險的使用者**-有[風險的使用者](concept-user-at-risk.md)是可能遭盜用之使用者帳戶的指標。

本文提供稽核報告的概觀。
 
## <a name="who-can-access-the-data"></a>誰可以存取資料？

* **安全性系統管理員**、**安全性讀取**者、**報告讀取**者、**全域讀取**者或**全域管理員**角色中的使用者

## <a name="audit-logs"></a>稽核記錄

Azure AD 稽核記錄會提供符合規範的系統活動記錄。 若要存取 audit 報告，請在**Azure Active Directory**的 [**監視**] 區段中選取 [ **audit logs** ]。 請注意，「審核記錄」的延遲最多可能會有一小時，因此在完成工作之後，可能需要該時間才會在入口網站中顯示「審核活動資料」。



稽核記錄的預設清單檢視顯示︰

- 發生時間與日期
- 記錄發生次數的服務
- 活動的類別和名稱（*what*） 
- 活動的狀態（成功或失敗）
- 目標
- 活動的啟動者/執行者 (對象)

![稽核記錄](./media/concept-audit-logs/listview.png "稽核記錄")

您可以按一下工具列中的 [資料**行**] 來自訂清單視圖。

![稽核記錄](./media/concept-audit-logs/columns.png "稽核記錄")

這可讓您顯示其他欄位，或移除已顯示的欄位。

![稽核記錄](./media/concept-audit-logs/columnselect.png "稽核記錄")

選取清單檢視中的項目，即可取得更詳細的資訊。

![稽核記錄](./media/concept-audit-logs/details.png "稽核記錄")


## <a name="filtering-audit-logs"></a>篩選稽核記錄

您可以在下列欄位上篩選稽核資料：

- Service
- 類別
- 活動
- 狀態
- 目標
- 啟動者 (執行者)
- 日期範圍

![稽核記錄](./media/concept-audit-logs/filter.png "稽核記錄")

**服務**篩選準則可讓您從下列服務的下拉式清單中選取：

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
- 使用條款

[**類別目錄**] 篩選器可讓您選取下列其中一個篩選準則：

- 全部
- AdministrativeUnit
- ApplicationManagement
- 驗證
- 授權
- 連絡人
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

**活動**篩選準則是根據您所選取的 [類別] 和 [活動] 資源類型而定。 您可以選取您想要查看的特定活動或選擇全部。 

您可以使用圖形 API 來取得所有 Audit 活動的清單：`https://graph.windows.net/<tenantdomain>/activities/auditActivityTypesV2?api-version=beta`

**狀態**篩選準則可讓您根據 audit 作業的狀態進行篩選。 狀態可以是下列其中一項：

- 全部
- 成功
- 失敗

**目標**篩選準則可讓您依名稱或使用者主體名稱（UPN）的開頭，搜尋特定的目標。 目標名稱和 UPN 會區分大小寫。 

[**起始者**] 篩選器可讓您定義動作專案名稱或通用主要名稱（UPN）的開頭。 名稱和 UPN 會區分大小寫。

[**日期範圍**] 篩選準則可讓您定義所傳回資料的時間範圍。  
可能的值包括：

- 7 天
- 24 小時
- 自訂

當您選取自訂時間範圍時，可以設定開始時間和結束時間。

您也可以選擇下載篩選的資料，最多可達250000筆記錄，方法是選取 [**下載**] 按鈕。 您可以下載 CSV 或 JSON 格式的記錄。 您可以下載的記錄數目會受限於 [Azure Active Directory 報告保留原則](reference-reports-data-retention.md)。

![稽核記錄](./media/concept-audit-logs/download.png "稽核記錄")

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

如果您只想要檢查與使用者相關的資料，您可以在 [**使用者**] 索引標籤的 [**監視**] 區段中，找到 [ **Audit logs** ] 底下的篩選視圖。此進入點已**usermanagement program.cs**為預先選取的類別目錄。

![稽核記錄](./media/concept-audit-logs/users.png "稽核記錄")

如果您只想要檢查與群組相關的資料，您可以在 [**群組**] 索引標籤的 [**監視**] 區段中，找到 [ **Audit logs** ] 底下的篩選視圖。此進入點已**GroupManagement**為預先選取的類別目錄。

![稽核記錄](./media/concept-audit-logs/groups.png "稽核記錄")

### <a name="enterprise-applications-audit-logs"></a>企業應用程式稽核記錄

透過以應用程式為基礎的稽核報告，可以取得下列問題的解答︰

* 已新增或更新的應用程式為何？
* 已移除的應用程式為何？
* 應用程式的服務原則已變更嗎？
* 應用程式的名稱已變更嗎？
* 誰已同意應用程式？

如果您想檢閱應用程式相關的稽核資料，可以在 [企業應用程式]**** 刀鋒視窗的 [活動]**** 區段中的 [稽核記錄]**** 之下找到篩選過的檢視。 此進入點會將**企業應用程式**預先選取為**應用程式類型**。

![稽核記錄](./media/concept-audit-logs/enterpriseapplications.png "稽核記錄")

## <a name="office-365-activity-logs"></a>Office 365 活動記錄

您可以從[Microsoft 365 系統管理中心](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center)觀看 Office 365 活動記錄。 雖然 Office 365 活動和 Azure AD 活動記錄共用許多目錄資源，但只有 Microsoft 365 系統管理中心才會提供 Office 365 活動記錄的完整觀點。 

您也可以使用[office 365 管理 api](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview)，以程式設計方式存取 office 365 活動記錄。

## <a name="next-steps"></a>後續步驟

- [Azure AD 稽核活動參考](reference-audit-activities.md)
- [Azure AD 報告保留參考](reference-reports-data-retention.md)
- [Azure AD 記錄延遲參考](reference-reports-latencies.md)
