---
title: Azure 活動目錄門戶中的預配日誌（預覽） |微軟文檔
description: Azure 活動目錄門戶中預配活動報表簡介
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/04/2019
ms.author: markvi
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6e0c697f9ab9796feade9b4d5c2a64794f3980b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73612805"
---
# <a name="provisioning-reports-in-the-azure-active-directory-portal-preview"></a>在 Azure 活動目錄門戶中預配報表（預覽）

Azure Active Directory (Azure AD) 中的報告架構包含下列元件：

- **活動** 
    - **登入** – 受控應用程式和使用者登入活動的使用情況相關資訊。
    - **稽核記錄** - [稽核記錄](concept-audit-logs.md)提供有關使用者和組管理、託管應用程式和目錄活動的系統活動資訊。
    - **預配日誌**- 提供有關 Azure AD 預配服務預配的使用者、組和角色的系統活動。 

- **安全性** 
    - **風險**登錄 -[風險登錄](concept-risky-sign-ins.md)是登錄嘗試的指示器，該嘗試可能由不是使用者帳戶的合法擁有者的人員執行。
    - **標記為風險的使用者**-[風險使用者](concept-user-at-risk.md)是可能已洩露的使用者帳戶的指示器。

本主題概述了預配報告。

## <a name="prerequisites"></a>Prerequisites

### <a name="who-can-access-the-data"></a>誰可以存取資料？
* 安全管理員、安全讀取器、報表閱讀器、應用程式管理員和雲應用程式管理員角色中的使用者
* 全域系統管理員


### <a name="what-azure-ad-license-do-you-need-to-access-provisioning-activities"></a>訪問預配活動需要哪些 Azure AD 許可證？

租戶必須具有與其關聯的 Azure AD 高級許可證才能查看所有向上預配活動報告。 請參閱[開始使用 Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) 來升級 Azure Active Directory 版本。 

## <a name="provisioning-logs"></a>佈建記錄

預配日誌提供以下問題的答案：

* 在 ServiceNow 中成功創建哪些組？
* 如何從亞馬遜 Web 服務導入角色？
* 在 DropBox 中未成功創建哪些使用者？

通過在[Azure 門戶](https://portal.azure.com)中的**Azure 活動目錄**邊欄選項卡的**監視**部分中選擇**預配日誌**，可以訪問預配日誌。 某些預配記錄最多可能需要兩個小時才能顯示在門戶中。

![佈建記錄](./media/concept-provisioning-logs/access-provisioning-logs.png "佈建記錄")


預配日誌具有預設清單視圖，該視圖顯示：

- 標識
- 操作
- 源系統
- 目標系統
- 狀態
- 日期


![預設列](./media/concept-provisioning-logs/default-columns.png "預設列")

您可以通過按一下工具列中的**列**來自訂清單視圖。

![欄選取器](./media/concept-provisioning-logs/column-chooser.png "欄選取器")

這可讓您顯示其他欄位，或移除已顯示的欄位。

![可用列](./media/concept-provisioning-logs/available-columns.png "可用的資料行")

選取清單檢視中的項目，即可取得更詳細的資訊。

![詳細資訊](./media/concept-provisioning-logs/steps.png "Filter")


## <a name="filter-provisioning-activities"></a>篩選器預配活動

要將報告的資料縮小到適合您的水準，可以使用以下預設欄位篩選預配資料。 請注意，篩選器中的值會根據您的租戶動態填充。 例如，如果租戶中沒有任何創建事件，則創建時沒有篩選器選項。

- 身分識別
- 動作
- 來源系統
- 目標系統
- 狀態
- Date


![篩選器](./media/concept-provisioning-logs/filter.png "Filter")

通過 **"標識"** 篩選器，您可以指定您關心的名稱或標識。 此標識可以是使用者、組、角色或其他物件。 您可以按物件的名稱或 ID 進行搜索。 ID 因方案而異。 例如，將物件從 Azure AD 預配到 SalesForce 時，源 ID 是 Azure AD 中使用者的物件識別碼，而 TargetID 是 Salesforce 中使用者的 ID。 從工作日預配到活動目錄時，源 ID 是工作日員工 ID。 請注意，使用者的姓名可能並不總是存在於"標識"列中。 始終有一個 ID。 

"**源系統**"篩選器使您能夠指定從何處預配標識。 例如，將物件從 Azure AD 預配到 ServiceNow 時，源系統是 Azure AD。 

目標**系統**篩選器使您能夠指定標識預配到的位置。 例如，將物件從 Azure AD 預配到 ServiceNow 時，目標系統是 ServiceNow。 

[狀態]**** 篩選條件可讓您選取︰

- 全部
- Success
- 失敗
- 已略過

"**操作"** 篩選器允許您篩選：

- 建立 
- 更新
- 刪除
- 停用
- 其他

[日期]**** 篩選條件可讓您定義傳回資料的時間範圍。  
可能的值包括：

- 1 個月
- 7 天
- 30 天
- 24 小時
- 自訂時間間隔

選擇自訂時間範圍時，可以配置開始日期和結束日期。


除了預設欄位之外，在選中時，還可以在篩選器中包括以下欄位：

- **作業 ID** - 與已為其啟用預配的每個應用程式相關聯的唯一作業 ID。   

- **週期 ID** - 唯一標識預配週期。 您可以共用此 ID 以支援查找發生此事件的週期。

- **更改 ID** - 預配事件的唯一識別碼。 您可以共用此 ID 以支援查找預配事件。   



  

## <a name="provisioning-details"></a>預配詳細資訊 

在預配清單視圖中選擇專案時，將獲取有關此項的更多詳細資訊。
詳細資訊根據以下類別進行分組：

- 步驟

- 故障排除和建議

- 修改的屬性

- 總結


![篩選器](./media/concept-provisioning-logs/provisioning-tabs.png "定位點")



### <a name="steps"></a>步驟

"**步驟**"選項卡概述了預配物件的步驟。 預配物件可以包含四個步驟： 

- 導入物件
- 確定物件是否在作用域中
- 在源和目標之間匹配物件
- 預配物件（執行操作 - 這可能是創建、更新、刪除或禁用）



![篩選器](./media/concept-provisioning-logs/steps.png "Filter")


### <a name="troubleshoot-and-recommendations"></a>故障排除和建議


**疑難排解和建議**選項卡提供錯誤代碼和原因。 錯誤資訊僅在發生故障時可用。 


### <a name="modified-properties"></a>修改的屬性

**修改後的屬性**顯示舊值和新值。 如果沒有舊值，則舊值列為空。 


### <a name="summary"></a>總結

**摘要**選項卡提供源和目標系統中物件的已發生的情況和識別碼的概述。 

## <a name="what-you-should-know"></a>您應該知道的事情

- Azure 門戶存儲 30 天的預配資料（如果有高級版）和 7 天（如果有免費版本）。

- 您可以將更改 ID 屬性用作唯一識別碼。 例如，在與產品支援交互時，這很有説明。

- 當前沒有下載預配資料的選項。

- 目前不支援日誌分析。

- 當您從應用的上下文中訪問預配日誌時，它不會自動將事件篩選到特定應用，就像稽核記錄那樣。

## <a name="error-codes"></a>錯誤碼

使用下表更好地瞭解如何解決在預配日誌中可能發現的錯誤。 對於缺少的任何錯誤代碼，請使用此頁面底部的連結提供回饋。 

|錯誤碼|描述|
|---|---|
|衝突，進入衝突|更正 Azure AD 或應用程式中的衝突屬性值，或者如果衝突使用者帳戶應匹配並接管，則查看匹配的屬性配置。 有關配置匹配屬性的詳細資訊，請查看以下[文檔](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)。|
|TooManyRequests|目標應用拒絕此更新使用者嘗試，因為它超載並接收了太多請求。 沒什麼可做的。 此嘗試將自動停用。 微軟也被告知此問題。|
|InternalServerError |目標應用返回了意外錯誤。 目標應用程式可能存在阻止此功能的工作的服務問題。 此嘗試將在 40 分鐘內自動停用。|
|權利不足、方法不允許、不允許、未經授權| Azure AD 能夠對目標應用程式進行身份驗證，但無權執行更新。 請查看目標應用程式以及相應的應用程式[教程](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)提供的任何說明。|
|不可處理實體|目標應用程式返回了意外回應。 目標應用程式的配置可能不正確，或者目標應用程式可能存在阻止此功能的工作的服務問題。|
|Web 例外協定錯誤 |連接到目標應用程式時發生 HTTP 協定錯誤。 沒什麼可做的。 此嘗試將在 40 分鐘內自動停用。|
|無效錨定|以前由預配服務創建或匹配的使用者不再存在。 檢查以確保使用者存在。 要強制重新匹配所有使用者，請使用 MS 圖形 API[重新開機作業](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http)。 請注意，重新開機預配將觸發初始週期，這可能需要時間才能完成。 它還刪除預配服務用於操作的緩存，這意味著租戶中的所有使用者和組都必須再次進行評估，並且可能會刪除某些預配事件。|
|NotImplemented | 目標應用返回了意外回應。 應用的配置可能不正確，或者目標應用可能存在服務問題，無法解決此問題。 請查看目標應用程式以及相應的應用程式[教程](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)提供的任何說明。 |
|必要欄位缺失，缺失值 |無法創建使用者，因為缺少所需的值。 更正源記錄中缺少的屬性值，或查看匹配的屬性配置以確保不省略所需的欄位。 [瞭解有關](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)配置匹配屬性的更多詳細資訊。|
|未找到架構屬性 |無法執行操作，因為指定了目標應用程式中不存在的屬性。 請參閱有關屬性自訂[的文檔](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)，並確保配置正確。|
|InternalError |Azure AD 預配服務中發生了內部服務錯誤。 沒什麼可做的。 此嘗試將在 40 分鐘內自動重試。|
|InvalidDomain |由於包含無效功能變數名稱的屬性值，無法執行該操作。 更新使用者的功能變數名稱或將其添加到目標應用程式中的允許清單中。 |
|逾時 |無法完成該操作，因為目標應用程式需要很長時間才能回應。 沒什麼可做的。 此嘗試將在 40 分鐘內自動重試。|
|超出許可證限制|無法在目標應用程式中創建使用者，因為此使用者沒有可用的許可證。 為目標應用程式獲取其他許可證，或者查看使用者分配和屬性對應配置，以確保使用正確的屬性分配正確的使用者。|
|重複目標條目  |無法完成該操作，因為使用已配置的匹配屬性找到了目標應用程式中的多個使用者。 從目標應用程式中刪除重複的使用者，或者按照[此處](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)所述重新配置屬性對應。|
|重複源條目 | 無法完成該操作，因為使用已配置的匹配屬性找到多個使用者。 刪除重複使用者，或按照[此處](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)所述重新配置屬性對應。|

## <a name="next-steps"></a>後續步驟

* [檢查使用者預配的狀態](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)
* [設定 Azure AD 資源庫應用程式的使用者佈建時遇到的問題](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem)


