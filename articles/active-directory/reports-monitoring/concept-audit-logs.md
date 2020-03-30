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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253230"
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Azure Active Directory 入口網站中的稽核活動報告 

透過 Azure Active Directory (Azure AD) 報告，您可以取得判斷環境執行狀況所需的資訊。

報告架構由下列元件組成：

- **活動** 
    - **登入** – [登入報告](concept-sign-ins.md)會提供受控應用程式和使用者登入活動的使用情況相關資訊。
    - **稽核記錄** - 可針對各種功能在 Azure AD 內進行的所有變更，提供記錄追蹤功能。 稽核記錄的範例包括對 Azure AD 中任何資源所做的變更，像是新增或移除使用者、應用程式、群組、角色和原則。
- **安全性** 
    - **風險**登錄 -[風險登錄](concept-risky-sign-ins.md)是登錄嘗試的指示器，該嘗試可能由不是使用者帳戶的合法擁有者的人員執行。 
    - **標記為風險的使用者**-[風險使用者](concept-user-at-risk.md)是可能已洩露的使用者帳戶的指示器。

本文提供稽核報告的概觀。
 
## <a name="who-can-access-the-data"></a>誰可以存取資料？

* **安全管理員**、**安全讀取器**、**報表閱讀器**、**全域讀取器**或**全域管理員**角色中的使用者

## <a name="audit-logs"></a>稽核記錄

Azure AD 稽核記錄會提供符合規範的系統活動記錄。 要訪問審核報告，請在**Azure 活動目錄**的**監視**部分中選擇**稽核記錄**。 請注意，稽核記錄的延遲可能長達一小時，因此在完成任務後，審核活動資料可能需要很長時間才能顯示在門戶中。



稽核記錄的預設清單檢視顯示︰

- 發生時間與日期
- 記錄事件的服務
- 活動的類別和名稱 （*什麼*） 
- 活動的狀態（成功或失敗）
- 目標
- 活動的啟動者/執行者 (對象)

![稽核記錄](./media/concept-audit-logs/listview.png "稽核記錄")

您可以通過按一下工具列中的**列**來自訂清單視圖。

![稽核記錄](./media/concept-audit-logs/columns.png "稽核記錄")

這可讓您顯示其他欄位，或移除已顯示的欄位。

![稽核記錄](./media/concept-audit-logs/columnselect.png "稽核記錄")

選取清單檢視中的項目，即可取得更詳細的資訊。

![稽核記錄](./media/concept-audit-logs/details.png "稽核記錄")


## <a name="filtering-audit-logs"></a>篩選稽核記錄

您可以在下列欄位上篩選稽核資料：

- 服務
- 類別
- 活動
- 狀態
- 目標
- 啟動者 (執行者)
- 日期範圍

![稽核記錄](./media/concept-audit-logs/filter.png "稽核記錄")

**"服務**"篩選器允許您從以下服務的下拉清單中選擇：

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
- 混合身份驗證
- 身分識別保護
- 受邀的使用者
- MIM 服務
- 我的應用程式
- PIM
- 自助式群組管理
- 自助式密碼管理
- 使用條款

"**類別"** 篩選器允許您選擇以下篩選器之一：

- 全部
- AdministrativeUnit
- ApplicationManagement
- 驗證
- 授權
- 連絡人
- 裝置
- DeviceConfiguration
- 目錄管理
- 權利管理
- 集團管理
- 克伯羅斯域
- 金鑰管理
- 標籤
- 其他
- 許可權授予政策
- 原則
- 資源管理
- 角色管理
- 使用者管理

"**活動**"篩選器基於您選擇的類別和活動資源類型。 您可以選取您想要查看的特定活動或選擇全部。 

您可以使用圖形 API 獲取所有審核活動的清單：`https://graph.windows.net/<tenantdomain>/activities/auditActivityTypesV2?api-version=beta`

狀態**篩選器**允許您根據審核操作的狀態進行篩選。 狀態可以是以下狀態之一：

- 全部
- Success
- 失敗

**目標**篩選器允許您通過名稱或使用者主體名稱 （UPN） 的開頭搜索特定目標。 目標名稱和 UPN 區分大小寫。 

通過篩選器**啟動**，可以定義參與者的名稱或通用主體名稱 （UPN） 的開頭。 名稱和 UPN 區分大小寫。

"**日期範圍**"篩選器使您能夠為返回的資料定義時間範圍。  
可能的值包括：

- 7 天
- 24 小時
- Custom

當您選取自訂時間範圍時，可以設定開始時間和結束時間。

您還可以選擇通過選擇 **"下載"** 按鈕下載篩選的資料（最多 250，000 條記錄）。 您可以下載 CSV 或 JSON 格式的日誌。 您可以下載的記錄數目會受限於 [Azure Active Directory 報告保留原則](reference-reports-data-retention.md)。

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

如果只想查看與使用者相關的審核資料，可以在 **"使用者**"選項卡的 **"監視**"部分的**稽核記錄**下找到篩選的視圖。此進入點將**使用者管理**列為預選類別。

![稽核記錄](./media/concept-audit-logs/users.png "稽核記錄")

如果只想查看與組相關的審核資料，可以在 **"組"** 選項卡的 **"監視**"部分的 **"稽核記錄**"下找到篩選的視圖。此進入點將**組管理**列為預選類別。

![稽核記錄](./media/concept-audit-logs/groups.png "稽核記錄")

### <a name="enterprise-applications-audit-logs"></a>企業應用程式稽核記錄

透過以應用程式為基礎的稽核報告，可以取得下列問題的解答︰

* 已新增或更新的應用程式為何？
* 已移除的應用程式為何？
* 應用程式的服務原則已變更嗎？
* 應用程式的名稱已變更嗎？
* 誰已同意應用程式？

如果您想檢閱應用程式相關的稽核資料，可以在 [企業應用程式]**** 刀鋒視窗的 [活動]**** 區段中的 [稽核記錄]**** 之下找到篩選過的檢視。 此進入點已**預先選擇企業應用程式**作為**應用程式類型**。

![稽核記錄](./media/concept-audit-logs/enterpriseapplications.png "稽核記錄")

## <a name="office-365-activity-logs"></a>Office 365 活動記錄

您可以查看來自[Microsoft 365 管理中心](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center)的 Office 365 活動日誌。 即使 Office 365 活動和 Azure AD 活動日誌共用大量目錄資源，但只有 Microsoft 365 管理中心提供 Office 365 活動日誌的完整視圖。 

您還可以使用[Office 365 管理 API](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview)以程式設計方式訪問 Office 365 活動日誌。

## <a name="next-steps"></a>後續步驟

- [Azure AD 稽核活動參考](reference-audit-activities.md)
- [Azure AD 報告保留參考](reference-reports-data-retention.md)
- [Azure AD 記錄延遲參考](reference-reports-latencies.md)
