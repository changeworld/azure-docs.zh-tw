---
title: 在僅限報表模式中設定條件式存取原則-Azure Active Directory
description: 在條件式存取中使用僅限報表模式以協助採用
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46a00d55c58992be1009da1de5441ebe4e589a70
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "83994964"
---
# <a name="configure-a-conditional-access-policy-in-report-only-mode"></a>在僅限報表模式中設定條件式存取原則

若要在僅限報表模式中設定條件式存取原則：

> [!IMPORTANT]
> 如果您的組織尚未[設定 Azure 監視器與 Azure AD 整合](#set-up-azure-monitor-integration-with-azure-ad)。 您必須先進行此程式，資料才可供審查。

1. 以條件式存取系統管理員、安全性系統管理員或全域管理員身分登入**Azure 入口網站**。
1. 瀏覽至 [Azure Active Directory] > [安全性] > [條件式存取]。
1. 選取 [新增原則]。
1. 視需要設定原則條件和必要的授與控制項。
1. 在 [**啟用原則**] 底下，將切換為 [**僅限報表**] 模式。
1. 選取 [儲存]

> [!TIP]
> 您可以將現有原則的 [**啟用原則**狀態] 從 [**開啟**] 編輯為 [**僅報告**]，但這麼做將會停用原則強制執行。 

View report-只會產生 Azure AD 登入記錄檔。

若要針對特定登入來查看僅限報告原則的結果：

1. 以「報表讀者」、「安全性讀取者」、「安全性系統管理員」或「全域管理員」身分登入**Azure 入口網站**。
1. 流覽至**Azure Active Directory**登  >  **入**。
1. 選取登入或新增篩選來縮小結果範圍。
1. 在 [**詳細資料**] 下拉式清單中，選取 [**僅限報告**] 索引標籤，以查看在登入期間評估的原則。

> [!NOTE]
> 下載登入記錄檔時，請選擇 [JSON 格式] 以包含條件式存取報告的結果資料。

## <a name="set-up-azure-monitor-integration-with-azure-ad"></a>設定與 Azure AD 的 Azure 監視器整合

若要使用新的條件式存取深入解析活頁簿來查看條件式存取原則的匯總影響，您必須將 Azure 監視器與 Azure AD 整合，並匯出登入記錄。 設定此整合的步驟有兩個： 

1. [註冊 Azure 監視器訂用帳戶，並建立工作區](/azure/azure-monitor/learn/quick-create-workspace)。
1. [將登入記錄從 Azure AD 匯出至 Azure 監視器](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)。

Azure 監視器定價的詳細資訊可在[Azure 監視器定價頁面](https://azure.microsoft.com/pricing/details/monitor/)上找到。 若要估計成本、設定每日上限或自訂資料保留期限，請參閱[使用 Azure 監視器記錄來管理使用量和成本](../../azure-monitor/platform/manage-cost-storage.md#estimating-the-costs-to-manage-your-environment)一文。

## <a name="view-conditional-access-insights-workbook"></a>View 條件式存取深入解析活頁簿

一旦將 Azure AD 記錄與 Azure 監視器整合之後，您就可以使用新的條件式存取深入解析活頁簿來監視條件式存取原則的影響。

1. 以安全性系統管理員或全域管理員身分登入**Azure 入口網站**。
1. 流覽至**Azure Active Directory**活頁  >  **簿**。
1. 選取 [**條件式存取深入**解析]。
1. 從 [**條件式存取原則**] 下拉式清單中選取一或多個原則。 預設會選取所有已啟用的原則。
1. 選取時間範圍（如果時間範圍超過可用的資料集，報表就會顯示所有可用的資料）。 一旦您設定了**條件式存取原則**和**時間範圍**參數，報表就會載入。
   1. （選擇性）搜尋個別**使用者**或**應用程式**，以縮小報告的範圍。
1. 選取 [在時間範圍內，依據使用者數目或登入數目來查看資料]。
1. 視**資料檢視**而定，[**影響摘要**] 會顯示所選參數範圍內的使用者或登入數目，依據 [總數目]、[**成功**]、[**失敗**]、[**需要的使用者動作**] 和 [**未**套用] 群組。 選取磚以檢查特定結果類型的登入。 
   1. 如果您已變更活頁簿參數，您可以選擇儲存複本以供未來使用。 選取報表頂端的 [儲存] 圖示，並提供要儲存的名稱和位置。
1. 向下滾動，以查看每個條件的登入細目。
1. 查看報表底部的 [登**入詳細資料**]，以調查上述選擇所篩選的個別登入事件。

> [!TIP] 
> 需要向下切入特定查詢或匯出登入詳細資料嗎？ 選取任何查詢右邊的按鈕，以在 Log Analytics 中開啟查詢。 選取 [匯出] 以匯出至 CSV 或 Power BI。

## <a name="common-problems-and-solutions"></a>常見問題與解決方案

### <a name="why-are-the-queries-in-the-workbook-failing"></a>為什麼活頁簿中的查詢失敗？

客戶注意到，如果有錯誤或多個工作區與活頁簿相關聯，查詢有時會失敗。 若要修正此問題，請按一下活頁簿頂端的 [**編輯**]，然後按 [設定] 齒輪。 選取並移除與活頁簿無關的工作區。 每個活頁簿只能有一個相關聯的工作區。

### <a name="why-doesnt-the-conditional-access-policies-dropdown-parameter-contain-my-policies"></a>條件式存取原則下拉式清單參數為何不包含我的原則？

[條件式存取原則] 下拉式清單會藉由在4小時內查詢最新的登入來填入。 如果租使用者過去4小時內沒有任何登入，則下拉式清單可能會是空的。 如果這項延遲是持續性的問題（例如在具有不常登入的小型租使用者中），系統管理員可以編輯 [條件式存取原則] 下拉式清單的查詢，並將查詢的時間延長至超過4小時的時間。

## <a name="next-steps"></a>後續步驟

[條件式存取一般原則](concept-conditional-access-policy-common.md)

如需 Azure AD 活頁簿的詳細資訊，請參閱[如何將 Azure 監視器活頁簿用於 Azure Active Directory 報表](../reports-monitoring/howto-use-azure-monitor-workbooks.md)一文。
