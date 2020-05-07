---
title: 條件式存取見解和報表活頁簿-Azure Active Directory
description: 使用 Azure AD 的條件式存取深入解析和報表活頁簿來疑難排解原則
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 05/01/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4113328a5de02c36b7285c837bd5314d11e526b
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690425"
---
# <a name="conditional-access-insights-and-reporting"></a>條件式存取見解和報告

條件式存取見解和報告活頁簿可讓您瞭解組織中條件式存取原則在一段時間內的影響。 在登入期間，可能會套用一或多個條件式存取原則，如果已滿足特定的授與控制或拒絕存取，則授予存取權。 因為在每次登入時可能會評估多個條件式存取原則，所以 [深入解析] 和 [報表] 活頁簿可讓您檢查個別原則或所有原則子集的影響。  

## <a name="prerequisites"></a>Prerequisites

若要啟用 insights 和報表活頁簿，您的租使用者必須具有 Log Analytics 工作區，才能保留登入記錄資料。 使用者必須具有 Azure AD Premium P1 或 P2 授權，才能使用條件式存取。

下列角色可以存取深入解析和報告：  

- 條件式存取系統管理員 
- 安全性讀取者 
- 安全性系統管理員 
- 全域讀者 
- 全域管理員 

使用者也需要下列其中一個 Log Analytics 工作區角色：  

- 讀取者 
- 監視讀取器 
- Log Analytics 讀者 
- 參與者  
- 擁有者 

### <a name="stream-sign-in-logs-from-azure-ad-to-azure-monitor-logs"></a>從 Azure AD 將登入記錄串流至 Azure 監視器記錄 

如果您尚未將 Azure AD 記錄與 Azure 監視器記錄整合，則必須先執行下列步驟，才會載入活頁簿：  

1. [在 Azure 監視器中建立 Log Analytics 工作區](../../azure-monitor/learn/quick-create-workspace.md)。
1. 將[Azure AD 記錄與 Azure 監視器記錄整合](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)。

## <a name="how-it-works"></a>運作方式 

若要存取 insights 和報表活頁簿：  

1. 登入 **Azure 入口網站**。
1. 流覽至**Azure Active Directory** > **安全性** > **條件式存取** > **深入解析和報告**。

### <a name="get-started-select-parameters"></a>開始使用：選取參數 

[深入解析] 和 [報表] 儀表板可讓您查看一或多個條件式存取原則在指定期間內的影響。 首先，設定活頁簿頂端的每個參數。 

![Azure 入口網站中的條件式存取深入解析和報告儀表板](./media/howto-conditional-access-insights-reporting/conditional-access-insights-and-reporting-dashboard.png)

**條件式存取原則**：選取一或多個條件式存取原則，以查看其合併的影響。 原則會分成兩個群組： [已啟用] 和 [僅限報告] 原則。 預設會選取所有已啟用的原則。 這些已啟用的原則是目前在您的租使用者中強制執行的原則。  

**時間範圍**：選取從4小時到90天為止的時間範圍。 如果您選取的時間範圍大於將 Azure AD 記錄與 Azure 監視器整合時，則只會顯示整合時間之後的登入。  

**使用者**：根據預設，儀表板會顯示所有使用者所選原則的影響。 若要依個別使用者篩選，請在文字欄位中輸入使用者的名稱。 若要依所有使用者篩選，請在文字欄位中輸入「所有使用者」，或將參數保留空白。 

**應用程式**：根據預設，儀表板會顯示針對所有應用程式所選原則的影響。 若要依個別應用程式進行篩選，請在文字欄位中輸入應用程式的名稱。 若要依所有應用程式篩選，請在文字欄位中輸入「所有應用程式」，或將參數保留空白。 

**資料檢視**：選取您是否要讓儀表板根據使用者數目或登入數目來顯示結果。個別使用者在指定的時間範圍內，可能會有許多不同結果的應用程式登入。 如果您選取資料檢視做為使用者，則使用者可能會包含在成功和失敗計數中（例如，如果有10個使用者，其中8個可能在過去30天內成功，而9個則可能在過去30天內發生失敗的結果）。

## <a name="impact-summary"></a>影響摘要 

一旦設定參數，就會載入影響摘要。 此摘要會顯示在評估選取的原則時，在此時間範圍內有多少使用者或登入導致「成功」、「失敗」、「需要使用者動作」或「未套用」。  

![條件式存取活頁簿中的影響摘要](./media/howto-conditional-access-insights-reporting/workbook-impact-summary.png)

**總計**：在評估至少其中一個所選原則的期間，使用者或登入的數目。

**成功**：在所選原則的合併結果為「成功」或「僅限報告：成功」的時間週期內，使用者或登入的數目。

**失敗**：在一段時間內，至少有一個所選原則的結果為「失敗」或「僅限報告：失敗」的使用者或登入數目。

**需要使用者動作**：在所選原則的合併結果為「僅限報告：需要使用者動作」的期間內，使用者或登入的數目。 當互動式授與控制項（例如，僅限報告的條件式存取原則需要多重要素驗證）時，需要使用者動作。 由於「僅限報表」原則不會強制執行互動式授與控制，因此無法判斷成功或失敗。  

**未**套用：在未套用任何選取的原則的期間內，使用者或登入的數目。

### <a name="understanding-the-impact"></a>瞭解影響 

![每個條件和狀態的活頁簿細目](./media/howto-conditional-access-insights-reporting/workbook-breakdown-condition-and-status.png)

針對每個條件，查看使用者或登入的明細。 您可以在活頁簿頂端的摘要磚上選取，以篩選特定結果的登入（例如，成功或失敗）。 您可以查看每個條件式存取條件的登入明細：裝置狀態、裝置平臺、用戶端應用程式、位置、應用程式和登入風險。  

## <a name="sign-in-details"></a>登入詳細資料 

![活頁簿登入詳細資料](./media/howto-conditional-access-insights-reporting/workbook-sign-in-details.png)

您也可以在儀表板底部搜尋登入，以調查特定使用者的登入。 左側的查詢會顯示最常出現的使用者。 選取使用者會篩選右側的查詢。  

## <a name="troubleshooting"></a>疑難排解

### <a name="why-is-the-workbook-taking-a-long-time-to-load"></a>活頁簿為什麼需要很長的時間才能載入？  

根據所選取的時間範圍和您的租使用者大小而定，活頁簿可能會評估大量的登入事件。 對於大型租使用者，登入量可能會超過 Log Analytics 的查詢容量。 請嘗試縮短時間範圍到4小時，以查看活頁簿是否載入。  

### <a name="after-loading-for-a-few-minutes-why-is-the-workbook-returning-zero-results"></a>載入幾分鐘後，活頁簿為什麼會傳回零個結果？ 

當登入量超過 Log Analytics 的查詢容量時，活頁簿會傳回零個結果。 請嘗試縮短時間範圍到4小時，以查看活頁簿是否載入。  

### <a name="can-i-save-my-parameter-selections"></a>我可以儲存參數選取專案嗎？  

您可以前往**Azure Active Directory** > 活頁**簿** > 的**條件式存取深入解析和報告**，將您的參數選擇儲存在活頁簿頂端。 您會在這裡找到 [活頁簿] 範本，您可以在其中編輯活頁簿，並將複本儲存到您的工作區，包括 [**我的報表**] 或 [**共用報表**] 中的參數選取專案。 

### <a name="can-i-edit-and-customize-the-workbook-with-additional-queries"></a>我可以使用其他查詢來編輯和自訂活頁簿嗎？ 

若要編輯和自訂活頁簿，請前往**Azure Active Directory** > 活頁**簿** > ] [**條件式存取深入**解析] 和 [報告]。 您會在這裡找到 [活頁簿] 範本，您可以在其中編輯活頁簿，並將複本儲存到您的工作區，包括 [**我的報表**] 或 [**共用報表**] 中的參數選取專案。 若要開始編輯查詢，請按一下活頁簿頂端的 [**編輯**]。  
 
## <a name="next-steps"></a>後續步驟

[條件式存取僅限報告模式](concept-conditional-access-report-only.md)
