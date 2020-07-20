---
title: 條件式存取見解和報告活頁簿 - Azure Active Directory
description: 使用 Azure AD 條件式存取見解和報告活頁簿來進行原則疑難排解
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 678c32703501c4d0b66321cfc3518631ffa28c0c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85253268"
---
# <a name="conditional-access-insights-and-reporting"></a>條件式存取見解和報告

條件式存取見解和報告活頁簿可讓您了解條件式存取原則一段時間內對貴組織的影響。 登入期間，可能會套用一或多個條件式存取原則，若已滿足特定授與控制項，則授與存取權，否則拒絕存取。 每次登入期間都可能會評估多個條件式存取原則，因為見解和報告活頁簿可讓您檢查個別原則或所有原則子集的影響。  

## <a name="prerequisites"></a>Prerequisites

若要啟用見解和報告活頁簿，您的租用戶必須具有 Log Analytics 工作區，才能保留登入記錄資料。 使用者必須具有 Azure AD Premium P1 或 P2 授權，才能使用條件式存取。

下列角色可存取見解和報告：  

- 條件式存取系統管理員 
- 安全性讀取者 
- 安全性系統管理員 
- 全域讀者 
- 全域管理員 

使用者也需要下列其中一個 Log Analytics 工作區角色：  

- 參與者  
- 擁有者 

### <a name="stream-sign-in-logs-from-azure-ad-to-azure-monitor-logs"></a>從 Azure AD 將登入記錄串流至 Azure 監視器記錄 

如果您尚未將 Azure AD 記錄與 Azure 監視器記錄整合，則必須先執行下列步驟，才會載入活頁簿：  

1. [在 Azure 監視器中建立 Log Analytics 工作區](../../azure-monitor/learn/quick-create-workspace.md)。
1. [整合 Azure AD 記錄與 Azure 監視器記錄](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)。

## <a name="how-it-works"></a>運作方式 

若要存取見解和報告活頁簿：  

1. 登入 **Azure 入口網站**。
1. 瀏覽至 [Azure Active Directory] > [安全性] > [條件式存取] > [見解和報告]。

### <a name="get-started-select-parameters"></a>開始使用：選取參數 

見解和報告儀表板可讓您查看一或多個條件式存取原則在指定期間內的影響。 首先，在活頁簿頂端設定每個參數。 

![Azure 入口網站中的條件式存取見解和報告儀表板](./media/howto-conditional-access-insights-reporting/conditional-access-insights-and-reporting-dashboard.png)

**條件式存取原則**：選取一或多個條件式存取原則以檢視其合併影響。 原則分成兩個群組：[已啟用] 和 [報告專用] 原則。 預設會選取所有 [已啟用] 原則。 這些已啟用原則是目前在您租用戶中強制執行的原則。  

**時間範圍**：選取從 4 小時到回溯為 90天的時間範圍。 如果您選取的時間範圍回推超出將 Azure AD 記錄與 Azure 監視器整合時，則只會顯示整合時間之後的登入。  

**使用者**：根據預設，儀表板會顯示所選原則對所有使用者的影響。 若要依個別使用者篩選，請在文字欄位中輸入使用者的名稱。 若要依所有使用者篩選，請在文字欄位中輸入「所有使用者」，或將參數保留空白。 

**應用程式**：根據預設，儀表板會顯示所選原則對所有應用程式的影響。 若要依個別應用程式篩選，請在文字欄位中輸入應用程式的名稱。 若要依所有應用程式篩選，請在文字欄位中輸入「所有應用程式」，或將參數保留空白。 

**資料檢視**：選取您要讓儀表板根據使用者數目或登入數目來顯示結果。在指定的時間範圍內，個別使用者可能會登入許多應用程式數百次，而有許多不同的結果。 如果您選取使用者的資料檢視，則使用者可能會包含在成功和失敗計數中 (例如，如果有 10 個使用者，其中 8 個在過去 30 天內可能有成功的結果，而其中 9 個在過去 30 天內可能有失敗的結果)。

## <a name="impact-summary"></a>影響摘要 

一旦設定參數，就會載入影響摘要。 此摘要會顯示在評估所選的原則時，在此時間範圍內有多少使用者或登入導致「成功」、「失敗」、「需要使用者採取動作」或「未套用」。  

![條件式存取活頁簿中的影響摘要](./media/howto-conditional-access-insights-reporting/workbook-impact-summary.png)

**Total**：在至少評估其中一個所選原則的期間內，使用者或登入的數目。

**成功**：所選原則的合併結果為「成功」或「報告專用：成功」的期間內，使用者或登入的數目。

**失敗**：所選原則的合併結果為「失敗」或「報告專用：失敗」的期間內，使用者或登入的數目。

**需要使用者採取動作**：所選原則的合併結果為「報告專用：需要使用者採取動作」的期間內，使用者或登入的數目。 當報告專用的條件式存取原則需要互動式授與控制項 (例如多重要素驗證) 時，則需要使用者採取動作。 由於報告專用的原則不會強制執行互動式授與控制項，因此無法判斷成功或失敗。  

**不適用**：在未套用任何所選原則的期間內，使用者或登入的數目。

### <a name="understanding-the-impact"></a>了解影響 

![依條件和狀態的活頁簿明細](./media/howto-conditional-access-insights-reporting/workbook-breakdown-condition-and-status.png)

針對每個條件檢視使用者或登入的明細。 您可選取活頁簿頂端的其中一個摘要圖格，以篩選特定結果的登入 (例如，成功或失敗)。 您可以查看每個條件式存取條件的登入明細：裝置狀態、裝置平台、用戶端應用程式、位置、應用程式和登入風險。  

## <a name="sign-in-details"></a>登入詳細資料 

![活頁簿登入詳細資料](./media/howto-conditional-access-insights-reporting/workbook-sign-in-details.png)

您也可在儀表板底部搜尋登入，以調查特定使用者的登入。 左側的查詢會顯示最常出現的使用者。 選取使用者會篩選右側的查詢。  

## <a name="troubleshooting"></a>疑難排解

### <a name="why-are-queries-failing-due-to-a-permissions-error"></a>為什麼查詢因為許可權錯誤而失敗？

為了存取活頁簿，您需要適當的 Azure AD 許可權，以及 Log Analytics 工作區許可權。 若要測試您是否具有適當的工作區許可權，請執行範例 log analytics 查詢：

1. 登入 **Azure 入口網站**。
1. 流覽至**Azure Active Directory**  >  **記錄**檔。
1. `SigninLogs`在 [查詢] 方塊中輸入，然後選取 [**執行**]。
1. 如果查詢未傳回任何結果，則您的工作區可能未正確設定。 

![疑難排解失敗的查詢](./media/howto-conditional-access-insights-reporting/query-troubleshoot-sign-in-logs.png)

如需有關如何將 Azure AD 登入記錄串流至 Log Analytics 工作區的詳細資訊，請參閱將[Azure AD 記錄與 Azure 監視器記錄整合](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)一文。

### <a name="why-is-the-conditional-access-policies-parameter-is-empty"></a>條件式存取原則參數為何是空的？

原則清單會藉由查看針對最新登入事件評估的原則來產生。 如果您的租使用者中沒有最近的登入，您可能需要等候幾分鐘的時間，讓活頁簿載入條件式存取原則的清單。 這可能會在設定 Log Analytics 之後立即發生，如果租使用者沒有最近的登入活動，則可能需要較長的時間。

### <a name="why-is-the-workbook-taking-a-long-time-to-load"></a>活頁簿為何需要很長的時間才能載入？  

根據所選的時間範圍和您的租用戶大小而定，活頁簿可能會評估極為大量的登入事件。 對於大型租用戶，登入量可能會超過 Log Analytics 的查詢容量。 請嘗試將時間範圍縮短為 4 小時，以查看活頁簿是否載入。  

### <a name="after-loading-for-a-few-minutes-why-is-the-workbook-returning-zero-results"></a>載入幾分鐘後，活頁簿為何會傳回零筆結果？ 

當登入量超過 Log Analytics 的查詢容量時，活頁簿就會傳回零筆結果。 請嘗試將時間範圍縮短為 4 小時，以查看活頁簿是否載入。  

### <a name="can-i-save-my-parameter-selections"></a>我可以儲存參數選取項目嗎？  

您可移至 [Azure Active Directory] > [活頁簿] > [條件式存取見解和報告]，以儲存活頁簿頂端的參數選取項目。 您會在此找到活頁簿範本，在範本中您可編輯活頁簿並將複本儲存到 [我的報告] 或 [共用報告]中的工作區，包括參數選取項目。 

### <a name="can-i-edit-and-customize-the-workbook-with-additional-queries"></a>我可使用其他查詢來編輯和自訂活頁簿嗎？ 

您可移至 [Azure Active Directory] > [活頁簿] > [條件式存取見解和報告]，以編輯和自訂活頁簿。 您會在此找到活頁簿範本，在範本中您可編輯活頁簿並將複本儲存到 [我的報告] 或 [共用報告]中的工作區，包括參數選取項目。 若要開始編輯查詢，請按一下活頁簿頂端的 [編輯]。  
 
## <a name="next-steps"></a>後續步驟

[條件式存取報告專用模式](concept-conditional-access-report-only.md)
