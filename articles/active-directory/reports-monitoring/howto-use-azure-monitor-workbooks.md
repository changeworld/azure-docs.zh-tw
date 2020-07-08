---
title: 用於建立報表的 Azure 監視器活頁簿 | Microsoft Docs
description: 了解如何使用 Azure 監視器活頁簿建立 Azure Active Directory 報表。
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: 4066725c-c430-42b8-a75b-fe2360699b82
ms.service: active-directory
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 10/30/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: ec492466e107eb6f4821f0e6d2caed9daa141a35
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85608945"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>如何使用 Azure 監視器活頁簿建立 Azure Active Directory 報表

> [!IMPORTANT]
> 為了將此活頁簿中的基礎查詢最佳化，請依序按一下 [編輯] 和設定圖示，然後選取想要執行這些查詢的工作區。 活頁簿預設會選取要路由傳送 Azure AD 記錄的所有工作區。 

您是否想要：

- 了解[條件式存取原則](../conditional-access/overview.md)對使用者登入體驗的影響？

- 針對登入失敗進行疑難排解，以進一步檢視組織的登入健康狀態並快速解決問題？

- 知道誰使用舊版驗證來登入環境？ (藉由[封鎖舊版驗證](../conditional-access/block-legacy-authentication.md)，即可改善租用戶的保護。)

- 您是否需要了解租用戶中條件式存取原則的影響？

- 您是否想要能夠檢閱：登入記錄查詢 (活頁簿可報告已授與或拒絕存取的使用者人數，以及存取資源時已略過條件式存取原則的使用者人數)？

- 有興趣更深入了解：每個條件的活頁簿詳細資料，以便能夠根據條件內容判斷原則的影響，包括裝置平台、裝置狀態、用戶端應用程式、登入風險、位置和應用程式？

- 取登入記錄查詢的深入見解 (活頁簿會報告已授與或拒絕存取的使用者人數，以及存取資源時已略過條件式存取原則的使用者人數)。

- 為協助解決這些問題，Azure Active Directory 提供活頁簿來進行監視。 [Azure 監視器活頁簿](https://docs.microsoft.com/azure/azure-monitor/platform/workbooks-overview)結合文字、分析查詢、計量和參數來建立豐富的互動式報表。



本文：

- 假設您已熟悉如何使用 [Azure 監視器活頁簿來建立互動式報表](https://docs.microsoft.com/azure/azure-monitor/platform/workbooks-overview)。

- 說明如何使用監視器活頁簿，以了解條件式存取原則的影響、針對登入失敗進行疑難排解，以及識別舊版驗證。
 


## <a name="prerequisites"></a>Prerequisites

若要使用監視器活頁簿，則需要：

- 具有進階 (P1 或 P2) 授權的 Azure Active Directory 租用戶。 了解如何[取得進階授權](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-get-started-premium)。

- [Log Analytics 工作區](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)。

- [存取](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-workspace-permissions) Log Analytics 工作區
- Azure Active Directory 中的下列角色 (如果是透過 Azure Active Directory 入口網站存取 Log Analytics)
    - 安全性系統管理員
    - 安全性讀取者
    - 報表讀取者
    - 全域管理員

## <a name="roles"></a>角色
您必須是下列其中一個角色，且可以[存取基礎 Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-azure-permissions) 工作區，才能管理活頁簿：
-   全域管理員
-   安全性系統管理員
-   安全性讀取者
-   報表讀取者
-   應用程式管理員

## <a name="workbook-access"></a>活頁簿存取 

若要存取活頁簿：

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 巡覽至 [Azure Active Directory] > [監視] > [活頁簿]。 

1. 選取一個報表或範本，或在工具列上選取 [開啟]。 

![在 Azure AD 中尋找 Azure 監視器活頁簿](./media/howto-use-azure-monitor-workbooks/azure-monitor-workbooks-in-azure-ad.png)

## <a name="sign-in-analysis"></a>登入分析

若要存取登入分析活頁簿，請在 [使用方式] 區段中，選取 [登入]。 

此活頁簿會顯示下列登入趨勢：

- 所有登入

- Success

- 等候使用者動作

- 失敗

您可依下列類別篩選每個趨勢：

- 時間範圍

- 應用程式

- 使用者

![登入分析](./media/howto-use-azure-monitor-workbooks/43.png)


針對每個趨勢，您可取得依下列類別顯示的明細：

- Location

    ![依位置顯示的登入](./media/howto-use-azure-monitor-workbooks/45.png)

- 裝置

    ![依裝置顯示的登入](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>使用舊版驗證的登入 


若要存取使用[舊版驗證](../conditional-access/block-legacy-authentication.md)的登入活頁簿，請在 [使用方式] 區段中，選取 [Sign-ins using Legacy Authentication] \(使用舊版驗證的登入\)。 

此活頁簿會顯示下列登入趨勢：

- 所有登入

- Success


您可依下列類別篩選每個趨勢：

- 時間範圍

- 應用程式

- 使用者

- 通訊協定

![透過舊版驗證的登入](./media/howto-use-azure-monitor-workbooks/47.png)


針對每個趨勢，您可取得依應用程式和通訊協定顯示的明細。

![依應用程式和通訊協定顯示的舊版驗證登入](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>透過條件式存取的登入 


若要存取透過[條件式存取原則](../conditional-access/overview.md)的登入活頁簿，請在 [條件式存取] 區段中，選取 [Sign-ins by Conditional Access] \(透過條件式存取的登入\)。 

此活頁簿會顯示已停用登入的趨勢。您可依下列類別篩選每個趨勢：

- 時間範圍

- 應用程式

- 使用者

![使用條件式存取登入](./media/howto-use-azure-monitor-workbooks/49.png)


針對已停用的登入，您可取得依條件式存取狀態顯示的明細。

![條件式存取狀態](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)


## <a name="conditional-access-insights"></a>條件式存取深入解析

### <a name="overview"></a>概觀

活頁簿包含登入記錄查詢，可協助 IT 系統管理員監視其租用戶中條件式存取原則的影響。 您可報告已授與或拒絕存取的使用者人數。 活頁簿可深入解析登入時根據使用者屬性已略過條件式存取原則的使用者人數。 其中包含每個條件的詳細資料，以便能夠根據條件內容判斷原則的影響，包括裝置平台、裝置狀態、用戶端應用程式、登入風險、位置和應用程式。

### <a name="instructions"></a>Instructions 
若要存取 [條件式存取見解] 的活頁簿，請在 [條件式存取] 區段中選取 [條件式存取見解] 活頁簿。 此活頁簿會顯示租用戶中每個條件式存取原則的預期影響。 從下拉式清單中選取一或多個條件式存取原則，然後套用下列篩選來縮小活頁簿的範圍： 

- **時間範圍**

- **使用者**

- **應用程式**

- **資料檢視**

![條件式存取狀態](./media/howto-use-azure-monitor-workbooks/access-insights.png)


[影響摘要] 會顯示所選原則對其具有特定結果的使用者或登入數目。 [總計] 是在所選時間範圍內對其評估所選原則的使用者或登入數目。 按一下磚即可依結果類型來篩選活頁簿中的資料。 

![條件式存取狀態](./media/howto-use-azure-monitor-workbooks/impact-summary.png)

此活頁簿也會顯示所選原則依六個條件任一個細分的影響： 
- **裝置狀態**
- **裝置平台**
- **用戶端應用程式**
- **登入風險**
- **位置**
- **應用程式**

![條件式存取狀態](./media/howto-use-azure-monitor-workbooks/device-platform.png)

您也可以調查依活頁簿中所選參數篩選的個別登入。 搜尋個別使用者 (依登入頻率排序)，然後檢視其對應的登入事件。 

![條件式存取狀態](./media/howto-use-azure-monitor-workbooks/filtered.png)





## <a name="sign-ins-by-grant-controls"></a>透過授與控制項的登入

若要存取透過[授與控制項](../conditional-access/controls.md)的登入活頁簿，請在 [條件式存取] 區段中，選取 [Sign-ins by Grant Controls] \(透過授與控制項的登入\)。 

此活頁簿會顯示下列已停用登入的趨勢：

- 需要 MFA
 
- 需要使用規定

- 需要隱私權聲明

- 其他


您可依下列類別篩選每個趨勢：

- 時間範圍

- 應用程式

- 使用者

![透過授與控制項的登入](./media/howto-use-azure-monitor-workbooks/50.png)


針對每個趨勢，您可取得依應用程式和通訊協定顯示的明細。

![最近的登入明細](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>登入失敗分析

使用 [登入失敗分析] 活頁簿來針對下列錯誤進行疑難排解：

- 登入
- 條件式存取原則
- 舊版驗證 


若要存取透過條件式存取的登入資料，請在 [疑難排解] 區段中，選取 [Sign-ins using Legacy Authentication] \(使用舊版驗證的登入\)。 

此活頁簿會顯示下列登入趨勢：

- 所有登入

- Success

- 等候動作

- 失敗


您可依下列類別篩選每個趨勢：

- 時間範圍

- 應用程式

- 使用者

![針對登入進行疑難排解](./media/howto-use-azure-monitor-workbooks/52.png)


為了協助針對登入進行疑難排解，Azure 監視器會提供依下列類別顯示的明細：

- 前幾項錯誤

    ![前幾大錯誤的摘要](./media/howto-use-azure-monitor-workbooks/53.png)

- 等候使用者採取動作的登入

    ![等候使用者採取動作之登入的摘要](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>後續步驟

[使用監視器活頁簿建立互動式報表](https://docs.microsoft.com/azure/azure-monitor/platform/workbooks-overview)。
