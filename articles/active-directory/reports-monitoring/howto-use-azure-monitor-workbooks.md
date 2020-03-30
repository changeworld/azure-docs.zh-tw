---
title: Azure 監視器報表活頁簿 |微軟文檔
description: 瞭解如何將 Azure 活動目錄報表的 Azure 監視器活頁簿使用。
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: 4066725c-c430-42b8-a75b-fe2360699b82
ms.service: active-directory
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 10/30/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 2e94d9f56a865999f9169650f621a6af892c27ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74014369"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>如何使用 Azure 活動目錄報表的 Azure 監視器活頁簿

> [!IMPORTANT]
> 為了優化此活頁簿中的基礎查詢，請按一下"編輯"，按一下"設置"圖示並選擇要運行這些查詢的工作區。 預設情況下，活頁簿將選擇路由 Azure AD 日誌的所有工作區。 

是否要：

- 瞭解[條件訪問策略](../conditional-access/overview.md)對使用者登錄體驗的影響嗎？

- 排除登錄失敗，以便更好地瞭解組織的登錄運行狀況並快速解決問題？

- 知道誰使用舊版身份驗證登錄到您的環境？ （通過[阻止舊版身份驗證](../conditional-access/block-legacy-authentication.md)，可以改進租戶的保護。

- 您是否需要瞭解條件訪問策略對租戶的影響？

- 您是否希望能夠查看：登錄日誌查詢、活頁簿報告有多少使用者被授予或拒絕訪問，以及有多少使用者在訪問資源時繞過條件訪問策略？

- 有興趣深入瞭解：每個條件的活頁簿詳細資訊，以便策略的影響可以按條件進行上下文化，包括設備平臺、設備狀態、用戶端應用、登錄風險、位置和應用程式？

- 深入瞭解登錄日誌查詢，活頁簿報告有多少使用者被授予或拒絕訪問，以及訪問資源時繞過條件訪問策略的使用者數量。

- 為了説明您解決這些問題，Active Directory 提供了用於監視的活頁簿。 [Azure 監視器活頁簿](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)將文本、分析查詢、指標和參數合併為豐富的互動式報表。



本文：

- 假設您熟悉[如何使用 Monitor 活頁簿創建互動式報表](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)。

- 說明如何使用 Monitor 活頁簿瞭解條件訪問策略的效果、排除登錄失敗的問題以及標識舊版身份驗證。
 


## <a name="prerequisites"></a>Prerequisites

要使用 Monitor 活頁簿，您需要：

- 具有高級 （P1 或 P2） 許可證的活動目錄租戶。 瞭解如何[獲得高級許可證](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-get-started-premium)。

- [日誌分析工作區](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)。

- [訪問](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-workspace-permissions)日誌分析工作區
- Azure 活動目錄中的角色（如果通過 Azure 活動目錄門戶訪問日誌分析）
    - 安全性系統管理員
    - 安全性讀取者
    - 報告閱讀器
    - 全域管理員

## <a name="roles"></a>角色
您必須擔任以下角色之一，並[有權訪問基礎日誌分析](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-azure-permissions)工作區來管理活頁簿：
-   全域管理員
-   安全性系統管理員
-   安全性讀取者
-   報告閱讀器
-   應用程式管理員

## <a name="workbook-access"></a>活頁簿訪問 

要訪問活頁簿：

1. 登錄到 Azure[門戶](https://portal.azure.com)。

1. 導航到**Azure 活動目錄** > **監視** > **活頁簿**。 

1. 選擇報表或範本，或在工具列上選擇 **"打開**"。 

![在 Azure AD 中查找 Azure 監視器活頁簿](./media/howto-use-azure-monitor-workbooks/azure-monitor-workbooks-in-azure-ad.png)

## <a name="sign-in-analysis"></a>登錄分析

要訪問登錄分析活頁簿，請在 **"使用方式**"部分中選擇 **"登錄**"。 

此活頁簿顯示以下登錄趨勢：

- 所有登錄

- Success

- 掛起的使用者操作

- 失敗

您可以按以下類別篩選每個趨勢：

- 時間範圍

- 應用程式

- 使用者

![登錄分析](./media/howto-use-azure-monitor-workbooks/43.png)


對於每種趨勢，您可以按以下類別進行細分：

- Location

    ![按位置登錄](./media/howto-use-azure-monitor-workbooks/45.png)

- 裝置

    ![按設備登錄](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>使用舊版身份驗證登錄 


要訪問使用[舊版身份驗證](../conditional-access/block-legacy-authentication.md)的登錄活頁簿，請在 **"使用"** 部分中選擇**使用舊身份驗證的登錄**。 

此活頁簿顯示以下登錄趨勢：

- 所有登錄

- Success


您可以按以下類別篩選每個趨勢：

- 時間範圍

- 應用程式

- 使用者

- 通訊協定

![按舊版身份驗證登錄](./media/howto-use-azure-monitor-workbooks/47.png)


對於每種趨勢，您都會按應用和協定進行細分。

![按應用和協定進行舊版身份驗證登錄](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>按條件訪問登錄 


要通過[條件訪問策略](../conditional-access/overview.md)訪問登錄活頁簿，請在 **"條件訪問**"部分中，**選擇"條件訪問"中的登錄**。 

此活頁簿顯示禁用登錄的趨勢。您可以按以下類別篩選每個趨勢：

- 時間範圍

- 應用程式

- 使用者

![使用條件式存取登入](./media/howto-use-azure-monitor-workbooks/49.png)


對於禁用的登錄，您可以按條件訪問狀態進行細分。

![條件訪問狀態](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)


## <a name="conditional-access-insights"></a>條件式存取深入解析

### <a name="overview"></a>總覽

活頁簿包含登錄日誌查詢，可説明 IT 管理員監視其租戶中條件訪問策略的影響。 您可以報告有多少使用者被授予或拒絕存取權限。 活頁簿包含有關在登錄時有多少使用者會根據這些使用者的屬性繞過條件訪問策略的見解。 它包含每個條件的詳細資訊，以便策略的影響可以按條件進行上下文化，包括設備平臺、設備狀態、用戶端應用、登錄風險、位置和應用程式。

### <a name="instructions"></a>Instructions 
要訪問條件訪問見解的活頁簿，請在"條件訪問"部分中選擇 **"條件訪問見解**"活頁簿。 此活頁簿顯示租戶中每個條件訪問策略的預期影響。 從下拉清單中選擇一個或多個條件訪問策略，並通過應用以下篩選器縮小活頁簿的範圍： 

- **時間範圍**

- **使用者**

- **應用程式**

- **資料檢視**

![條件訪問狀態](./media/howto-use-azure-monitor-workbooks/access-insights.png)


影響摘要顯示所選策略具有特定結果的使用者或登錄數。 總計是在所選時間範圍內為其計算所選策略的使用者或登錄數。 按一下磁貼可按該結果類型篩選活頁簿中的資料。 

![條件訪問狀態](./media/howto-use-azure-monitor-workbooks/impact-summary.png)

此活頁簿還顯示按六個條件細分的選定策略的影響： 
- **設備狀態**
- **設備平臺**
- **用戶端應用程式**
- **登錄風險**
- **位置**
- **應用程式**

![條件訪問狀態](./media/howto-use-azure-monitor-workbooks/device-platform.png)

您還可以調查各個登錄，由活頁簿中選擇的參數篩選。 搜索按登錄頻率排序的單個使用者，並查看相應的登錄事件。 

![條件訪問狀態](./media/howto-use-azure-monitor-workbooks/filtered.png)





## <a name="sign-ins-by-grant-controls"></a>通過授予控制項登錄

要通過[授予控制項](../conditional-access/controls.md)訪問登錄活頁簿，請在 **"條件訪問"** 部分中，**選擇"通過授予控制項登錄**"。 

此活頁簿顯示以下禁用登錄趨勢：

- 需要 MFA
 
- 需要使用規定

- 需要隱私權聲明

- 其他


您可以按以下類別篩選每個趨勢：

- 時間範圍

- 應用程式

- 使用者

![通過授予控制項登錄](./media/howto-use-azure-monitor-workbooks/50.png)


對於每種趨勢，您都會按應用和協定進行細分。

![最近登錄的細目](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>登錄失敗分析

使用**登錄失敗分析**活頁簿解決錯誤：

- 登入
- 條件式存取原則
- 舊版驗證 


要按條件訪問資料訪問登錄，請在 **"疑難排解**"部分中，**選擇使用舊身份驗證的登錄**。 

此活頁簿顯示以下登錄趨勢：

- 所有登錄

- Success

- 掛起的操作

- 失敗


您可以按以下類別篩選每個趨勢：

- 時間範圍

- 應用程式

- 使用者

![故障排除登錄](./media/howto-use-azure-monitor-workbooks/52.png)


為了説明解決登錄問題，Azure 監視器按以下類別提供細分：

- 熱門錯誤

    ![頂級錯誤的摘要](./media/howto-use-azure-monitor-workbooks/53.png)

- 等待使用者操作的登錄

    ![等待使用者操作的登錄摘要](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>後續步驟

[使用監視器活頁簿創建互動式報表](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)。
