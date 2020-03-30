---
title: 在僅報告模式下配置條件訪問策略 - Azure 活動目錄
description: 在條件訪問中使用僅報告模式來説明採用
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d9bec829d7fb3e76eb243bda73423303670585e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295121"
---
# <a name="configure-a-conditional-access-policy-in-report-only-mode-preview"></a>在僅報告模式下配置條件訪問策略（預覽）

要在僅報告模式下配置條件訪問策略，請執行以下操作：

> [!IMPORTANT]
> 如果組織尚未，[請設置 Azure 監視器與 Azure AD 的集成](#set-up-azure-monitor-integration-with-azure-ad)。 在資料可供審查之前，必須進行此過程。

1. 以條件訪問管理員、安全管理員或全域管理員的身份登錄到**Azure 門戶**。
1. 流覽到**Azure 活動目錄** > **安全** > **條件訪問**。
1. 選取 [新增原則]****。
1. 根據需要配置策略條件和所需的授予控制。
1. 在 **"啟用"策略**下，將切換設置為**僅報告**模式。
1. 選擇 **"保存"**

> [!TIP]
> 您可以將現有策略的**啟用策略**狀態從 **"打開**"編輯為**僅報告**，但這樣做將禁用策略實施。 

查看僅查看 Azure AD 登錄日誌的結果。

要查看特定登錄的僅報告策略的結果，請：

1. 以報表讀取器、安全讀取器、安全管理員或全域管理員的身份登錄到**Azure 門戶**。
1. 流覽到**Azure 活動目錄** > **登錄**。
1. 選擇登錄或添加篩選器以縮小結果範圍。
1. 在 **"詳細資訊**"抽屜中，選擇 **"僅報告（預覽）"** 選項卡以查看登錄期間評估的策略。

> [!NOTE]
> 下載登錄日誌時，請選擇 JSON 格式以包括僅條件訪問報告結果資料。

## <a name="set-up-azure-monitor-integration-with-azure-ad"></a>設置 Azure 監視器與 Azure AD 的集成

為了使用新的條件訪問見解活頁簿查看條件訪問策略的聚合影響，必須將 Azure 監視器與 Azure AD 集成並匯出登錄日誌。 設置此集成有兩個步驟： 

1. [註冊 Azure 監視器訂閱並創建工作區](/azure/azure-monitor/learn/quick-create-workspace)。
1. [將登錄日誌從 Azure AD 匯出到 Azure 監視器](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)。

有關 Azure 監視器定價的詳細資訊，請參閱[Azure 監視器定價頁](https://azure.microsoft.com/pricing/details/monitor/)。 用於估計成本、設置每日上限或自訂資料保留期的資源，請參閱"[使用 Azure 監視器日誌管理使用方式和成本](../../azure-monitor/platform/manage-cost-storage.md#estimating-the-costs-to-manage-your-environment)"一文。

## <a name="view-conditional-access-insights-workbook"></a>查看條件訪問見解活頁簿

將 Azure AD 日誌與 Azure 監視器集成後，可以使用新的條件訪問見解活頁簿監視條件訪問策略的影響。

1. 以安全管理員或全域管理員身份登錄到**Azure 門戶**。
1. 流覽到**Azure 活動目錄** > **活頁簿**。
1. 選擇**條件訪問見解（預覽）。**
1. 從**條件訪問策略**下拉清單中選擇一個或多個策略。 預設情況下，將選擇所有啟用的策略。
1. 選擇時間範圍（如果時間範圍超過可用資料集，報表將顯示所有可用資料）。 設置**條件訪問策略**和**時間範圍**參數後，將載入報表。
   1. 可選，搜索單個**使用者**或**應用**以縮小報表的範圍。
1. 在按使用者數或登錄數查看時間範圍內的資料之間進行選擇。
1. 根據 **"資料"視圖**，"**影響摘要**"顯示所選參數範圍內的使用者數或登錄數，按總數、**成功**、**失敗**、**使用者操作和****未應用**顯示的參數進行分組。 選擇磁貼以檢查特定結果類型的登錄。 
   1. 如果更改了活頁簿參數，可以選擇保存副本以供將來使用。 選擇報表頂部的保存圖示，並提供要保存到的名稱和位置。
1. 向下滾動以查看每個條件的登錄細目。
1. 查看報表底部的**登錄詳細資訊**，以調查按上述選擇篩選的單個登錄事件。

> [!TIP] 
> 需要向下切入特定查詢或匯出登錄詳細資訊？ 選擇任何查詢右側的按鈕，以在日誌分析中打開查詢。 選擇"匯出以匯出"以匯出到 CSV 或 Power BI。

## <a name="common-problems-and-solutions"></a>常見問題與解決方案

### <a name="why-are-the-queries-in-the-workbook-failing"></a>為什麼活頁簿中的查詢失敗？

客戶注意到，如果與活頁簿關聯了錯誤或多個工作區，查詢有時會失敗。 要解決此問題，請按一下活頁簿頂部的 **"編輯"，** 然後按一下"設置"齒輪。 選擇並刪除未與活頁簿關聯的工作區。 應該只有一個工作區與每個活頁簿相關聯。

### <a name="why-doesnt-the-conditional-access-policies-dropdown-parameter-contain-my-policies"></a>為什麼條件訪問策略下拉參數不包含我的策略？

通過查詢 4 小時內的最新登錄，填充條件訪問策略下拉清單。 如果租戶在過去 4 小時內沒有任何登錄，則下拉清單可能為空。 如果此延遲是一個持續的問題，例如在登錄不頻繁的小租戶中，管理員可以編輯條件訪問策略下拉的查詢，並將查詢時間延長到 4 小時以上。

## <a name="next-steps"></a>後續步驟

[條件訪問通用策略](concept-conditional-access-policy-common.md)

有關 Azure AD 活頁簿的詳細資訊，請參閱文章"[如何使用 Azure 活動目錄報表的 Azure 監視器活頁簿](../reports-monitoring/howto-use-azure-monitor-workbooks.md)"。
