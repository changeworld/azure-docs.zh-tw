---
title: Azure 活動目錄門戶中的預配日誌(預覽) |微軟文件
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
ms.openlocfilehash: 30cc8be6ad9ebffcad58c5b2412ae15ff3f26fa5
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113333"
---
# <a name="provisioning-reports-in-the-azure-active-directory-portal-preview"></a>在 Azure 活動目錄門戶中預配報表(預覽)

Azure Active Directory (Azure AD) 中的報告架構包含下列元件：

- **活動** 
    - **登入** – 受控應用程式和使用者登入活動的使用情況相關資訊。
    - **審核日誌** - [審核日誌](concept-audit-logs.md)提供有關使用者和組管理、託管應用程式和目錄活動的系統活動資訊。
    - **預配紀錄**- 提供有關 Azure AD 預配服務預配的使用者、組和角色的系統活動。 

- **安全性** 
    - **風險**登錄 -[風險登錄](concept-risky-sign-ins.md)是登錄嘗試的指示器,該嘗試可能由不是使用者帳戶的合法擁有者的人員執行。
    - **標記為風險的使用者**-[風險使用者](concept-user-at-risk.md)是可能已洩露的使用者帳戶的指示器。

本主題概述了預配報告。

## <a name="prerequisites"></a>Prerequisites

### <a name="who-can-access-the-data"></a>誰可以存取資料？
* 安全管理員、安全讀取器、報表閱讀器、應用程式管理員和雲端應用程式管理員角色中的使用者
* 全域系統管理員


### <a name="what-azure-ad-license-do-you-need-to-access-provisioning-activities"></a>存取預配活動需要哪些 Azure AD 許可證?

租戶必須具有與其關聯的 Azure AD 高級許可證才能查看所有向上預配活動報告。 請參閱[開始使用 Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) 來升級 Azure Active Directory 版本。 

## <a name="provisioning-logs"></a>佈建記錄

預配日誌提供以下問題的答案:

* 在 ServiceNow 中成功創建哪些組?
* 如何從亞馬遜 Web 服務導入角色?
* 在 DropBox 中未成功創建哪些使用者?

通過在[Azure 門戶](https://portal.azure.com)中的**Azure 活動目錄**邊欄選項卡的**監視**部分中選擇**預配日誌**,可以存取預配日誌。 某些預配記錄最多可能需要兩個小時才能顯示在門戶中。

![佈建記錄](./media/concept-provisioning-logs/access-provisioning-logs.png "佈建記錄")


預配紀錄具有預設清單檢視,該檢視顯示:

- 識別
- 操作
- 源系統
- 目標系統
- 狀態
- 日期


![預設欄](./media/concept-provisioning-logs/default-columns.png "預設欄")

您可以通過按一下工具列中的**列**來自定義清單檢視。

![欄選擇器](./media/concept-provisioning-logs/column-chooser.png "欄選擇器")

這可讓您顯示其他欄位，或移除已顯示的欄位。

![可用欄位](./media/concept-provisioning-logs/available-columns.png "可用的資料行")

選取清單檢視中的項目，即可取得更詳細的資訊。

![詳細資訊](./media/concept-provisioning-logs/steps.png "Filter")


## <a name="filter-provisioning-activities"></a>篩選器預先使用活動

您可以篩選預配資料。 某些篩選器值會根據您的租戶動態填充。 例如,如果租戶中沒有任何創建事件,則創建時沒有篩選器選項。
在預設檢視中,您可以選擇以下篩選器:

- 身分識別
- Date
- 狀態
- 動作


![Filter](./media/concept-provisioning-logs/default-filter.png "Filter")

以 **「識別」** 篩選器,您可以指定您關心的名稱或識別。 此標識可以是使用者、組、角色或其他物件。 您可以按物件的名稱或 ID 進行搜尋。 ID 因方案而異。 例如,將物件從 Azure AD 預配到 SalesForce 時,源 ID 是 Azure AD 中使用者的物件 ID,而 TargetID 是 Salesforce 中使用者的 ID。 從工作日預配到活動目錄時,源 ID 是工作日員工 ID。 請注意,使用者的姓名可能並不總是存在於"標識"列中。 始終有一個 ID。 


[日期]**** 篩選條件可讓您定義傳回資料的時間範圍。  
可能的值包括：

- 1 個月
- 7 天
- 30 天
- 24 小時
- 自訂時間間隔

選擇自訂時間範圍時,可以配置開始日期和結束日期。


[狀態]**** 篩選條件可讓您選取︰

- 全部
- Success
- 失敗
- 已略過



**' 動作'** 篩選器允許您篩選:

- 建立 
- 更新
- 刪除
- 停用
- 其他

此外,除了默認檢視的篩選器,還可以設置以下篩選器:

- 作業識別碼
- 循環識別碼
- 變更識別碼
- 源識別碼
- 目標識別碼
- Application


![選取欄位](./media/concept-provisioning-logs/add-filter.png "選取欄位")


- **作業 ID** - 與已為其啟用預配的每個應用程式相關聯的唯一作業 ID。   

- **週期 ID** - 唯一標識預配週期。 您可以共用此 ID 以支援查找發生此事件的週期。

- **更改 ID** - 預配事件的唯一識別碼。 您可以共用此 ID 以支援查找預配事件。   


- **源系統**- 使您能夠指定從何處預配標識。 例如,將物件從 Azure AD 預配到 ServiceNow 時,源系統是 Azure AD。 

- **目標系統**- 使您能夠指定標識預配到的位置。 例如,將物件從 Azure AD 預配到 ServiceNow 時,目標系統是 ServiceNow。 

- **應用程式**- 使您能夠僅顯示具有包含特定字串的顯示名稱的應用程式的記錄。

 

## <a name="provisioning-details"></a>預先詳細資訊 

在預配清單檢視中選擇專案時,將獲取有關此項的更多詳細資訊。
詳細資訊根據以下類別進行群組:

- 步驟

- 容錯排除與建議

- 變更屬性

- 總結


![Filter](./media/concept-provisioning-logs/provisioning-tabs.png "定位點")



### <a name="steps"></a>步驟

「**步驟**」選項卡概述了預配物件的步驟。 預配物件可以包含四個步驟: 

- 匯入物件
- 確定物件是否在作用網域中
- 在來源與目標之間符合物件
- 預先對物件 (執行操作 - 這可能是建立、更新、刪除或關閉)



![Filter](./media/concept-provisioning-logs/steps.png "Filter")


### <a name="troubleshoot-and-recommendations"></a>容錯排除與建議


**疑難解答和建議**選項卡提供錯誤代碼和原因。 錯誤資訊僅在發生故障時可用。 


### <a name="modified-properties"></a>變更屬性

**修改後的屬性**顯示舊值和新值。 如果沒有舊值,則舊值列為空。 


### <a name="summary"></a>總結

**摘要**選項卡提供源和目標系統中物件的已發生的情況和識別碼的概述。 

## <a name="what-you-should-know"></a>您應該知道的事情

- Azure 門戶存儲 30 天的預配數據(如果有高級版)和 7 天(如果有免費版本)。

- 您可以將更改 ID 屬性用作唯一識別碼。 例如,在與產品支援交互時,這很有説明。

- 當前沒有下載預配數據的選項。

- 目前不支援日誌分析。

- 當您從應用的上下文中訪問預配日誌時,它不會自動將事件篩選到特定應用,就像審核日誌那樣。

## <a name="error-codes"></a>錯誤碼

使用下表更好地瞭解如何解決在預配日誌中可能發現的錯誤。 對於缺少的任何錯誤代碼,請使用此頁面底部的連結提供反饋。 

|錯誤碼|描述|
|---|---|
|衝突,進入衝突|更正 Azure AD 或應用程式中的衝突屬性值,或者如果衝突使用者帳戶應匹配並接管,則查看匹配的屬性配置。 有關設定符合屬性的詳細資訊,請檢視以下[文件](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)。|
|TooManyRequests|目標應用拒絕此更新用戶嘗試,因為它超載並接收了太多請求。 沒什麼可做的。 此嘗試將自動停用。 微軟也被告知此問題。|
|InternalServerError |目標應用返回了意外錯誤。 目標應用程式可能存在阻止此功能的工作的服務問題。 此嘗試將在 40 分鐘內自動停用。|
|權利不足、方法不允許、不允許、未經授權| Azure AD 能夠對目標應用程式進行身份驗證,但無權執行更新。 請查看目標應用程式以及相應的應用程式[教程](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)提供的任何說明。|
|無法處理實體|目標應用程式返回了意外回應。 目標應用程式的配置可能不正確,或者目標應用程式可能存在阻止此功能的工作的服務問題。|
|Web 例外協定錯誤 |連接到目標應用程式時發生 HTTP 協定錯誤。 沒什麼可做的。 此嘗試將在 40 分鐘內自動停用。|
|不合法錨定|以前由預配服務創建或匹配的使用者不再存在。 檢查以確保使用者存在。 要強制重新符合所有使用者,請使用 MS 圖形 API[重新啟動作業](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http)。 請注意,重新啟動預配將觸發初始週期,這可能需要時間才能完成。 它還刪除預配服務用於操作的緩存,這意味著租戶中的所有使用者和組都必須再次進行評估,並且可能會刪除某些預配事件。|
|NotImplemented | 目標應用返回了意外回應。 應用的配置可能不正確,或者目標應用可能存在服務問題,無法解決此問題。 請查看目標應用程式以及相應的應用程式[教程](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)提供的任何說明。 |
|必填欄位缺失,缺失值 |無法創建使用者,因為缺少所需的值。 更正源記錄中缺少的屬性值,或查看匹配的屬性配置以確保不省略所需的欄位。 [瞭解有關](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)配置匹配屬性的更多詳細資訊。|
|找不到架構屬性 |無法執行操作,因為指定了目標應用程式中不存在的屬性。 請參閱有關屬性自定義[的文檔](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes),並確保配置正確。|
|InternalError |Azure AD 預配服務中發生了內部服務錯誤。 沒什麼可做的。 此嘗試將在 40 分鐘內自動重試。|
|InvalidDomain |由於包含無效功能變數名稱的屬性值,無法執行該操作。 更新使用者的功能變數名稱或將其添加到目標應用程式中的允許清單中。 |
|逾時 |無法完成該操作,因為目標應用程式需要很長時間才能回應。 沒什麼可做的。 此嘗試將在 40 分鐘內自動重試。|
|超出授權限制|無法在目標應用程式中創建使用者,因為此用戶沒有可用的許可證。 為目標應用程序獲取其他許可證,或者查看使用者分配和屬性映射配置,以確保使用正確的屬性分配正確的使用者。|
|重複目標項目  |無法完成該操作,因為使用已配置的匹配屬性找到了目標應用程式中的多個使用者。 從目標應用程式中刪除重複的使用者,或者按照[此處](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)所述重新配置屬性映射。|
|重複來源項目 | 無法完成該操作,因為使用已配置的匹配屬性找到多個使用者。 刪除重複使用者,或按照[此處](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)所述重新配置屬性映射。|

## <a name="next-steps"></a>後續步驟

* [檢查使用者預先狀態](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)
* [設定 Azure AD 資源庫應用程式的使用者佈建時遇到的問題](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem)


