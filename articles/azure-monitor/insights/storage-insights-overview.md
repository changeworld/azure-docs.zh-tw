---
title: 使用 Azure 監視器監視 Azure 存儲服務以進行存儲（預覽）*微軟文檔
description: 本文介紹了 Azure 監視器存儲功能，該功能使存儲管理員能夠快速瞭解其 Azure 存儲帳戶的性能和利用率問題。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/15/2019
ms.openlocfilehash: f23be7e764ad180a23c76abb7f9bb2218fd61e4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77662514"
---
# <a name="monitoring-your-storage-service-with-azure-monitor-for-storage-preview"></a>使用 Azure 監視器監視存儲服務（預覽）

Azure 存儲監視器（預覽）通過提供 Azure 存儲服務性能、容量和可用性的統一視圖，提供 Azure 存儲帳戶的全面監視。 您可以通過兩種方式觀察存儲容量和性能，直接從存儲帳戶查看，或者從 Azure 監視器查看，以查看跨存儲帳戶組。 

本文將説明您瞭解 Azure 存儲監視器（預覽）提供的經驗，以便獲得有關存儲帳戶的運行狀況和性能的可操作知識，並能夠專注于熱點並診斷延遲、限制、和可用性問題。

## <a name="introduction-to-azure-monitor-for-storage-preview"></a>Azure 監視器存儲簡介（預覽版）

在深入瞭解體驗之前，您應該瞭解它如何呈現和視覺化資訊。 無論是直接從存儲帳戶還是從 Azure 監視器中選擇存儲功能，Azure 存儲監視器都提供了一致的體驗。 

組合它提供：

* **在比例透視**顯示基於存儲服務或 API 操作的運行狀況的快照視圖，顯示存儲服務接收的請求總數的利用率，以及顯示存儲服務或 API 操作類型處理請求的平均時間的延遲。 您還可以按 Blob、檔、表和佇列查看容量。

* **向下切入**對特定存儲帳戶的分析，以説明診斷問題或按類別執行詳細分析 - 可用性、性能、故障和容量。 選擇其中任一選項可深入瞭解指標。  

* **可自訂**，您可以在其中更改要查看的指標，修改或設置與您的限制一致的閾值，並將其另存為您自己的活頁簿。 活頁簿中的圖表可以固定到 Azure 儀表板。  

此功能不要求您啟用或配置任何內容，預設情況下會收集存儲帳戶中的存儲指標。 如果您不熟悉 Azure 存儲上可用的指標，請查看 Azure[存儲](../../storage/common/storage-metrics-in-azure-monitor.md)指標中的說明和定義。

>[!NOTE]
>訪問此功能不收取任何費用，並且只需為配置或啟用的 Azure 監視器基本功能付費，如[Azure 監視器定價詳細資訊](https://azure.microsoft.com/pricing/details/monitor/)頁上所述。

>[!NOTE]
>Azure 存儲監視器不支援通用[v1 帳戶](../../storage/common/storage-account-overview.md#general-purpose-v1-accounts)。
>

## <a name="view-from-azure-monitor"></a>從 Azure 監視器查看

在 Azure 監視器中，您可以查看訂閱中多個存儲帳戶的事務、延遲和容量詳細資訊，並説明識別性能、容量問題和故障。

要查看所有訂閱中存儲帳戶的利用率和可用性，請執行以下步驟。

1. 登錄到 Azure[門戶](https://portal.azure.com)。

2. 從 Azure 門戶中的左側窗格中選擇 **"監視器"，** 在 **"見解"** 部分下選擇 **"存儲帳戶（預覽）"。**

    ![多個存儲帳戶視圖](./media/storage-insights-overview/multiple-storage-accounts-view-01.png)

### <a name="overview-workbook"></a>概述活頁簿

在所選訂閱的 **"概述**"活頁簿上，該表顯示訂閱中最多 10 個存儲帳戶的互動式存儲指標和服務可用性狀態。 您可以根據從以下下拉清單中選擇的選項篩選結果：

* **訂閱**- 僅列出具有存儲帳戶的訂閱。  

* **存儲帳戶**- 預設情況下，預先選擇了 10 個存儲帳戶。 如果在範圍選取器中選擇所有或多個存儲帳戶，則最多將返回 200 個存儲帳戶。 例如，如果您在所選的三個訂閱中共有 573 個存儲帳戶，則只顯示 200 個帳戶。 

* **時間範圍**- 預設情況下，根據所做的相應選擇顯示最後 4 小時的資訊。

下拉清單下的計數器磁貼將匯總訂閱中的存儲帳戶總數，並反映所選的存儲帳戶總數中有多少。 活頁簿中報告交易記錄指標或錯誤的列有條件顏色編碼或熱圖。 最深的顏色具有最高值，較淺的顏色基於最低值。 對於基於錯誤的列，該值為紅色，對於基於指標的列，該值為藍色。

在列中選擇"**可用性****"、"E2E 延遲**"、"**伺服器延遲**"和"**事務錯誤類型/錯誤**"中的值將引導您到根據為該存儲帳戶選擇的列定制的特定類型的存儲指標的報表。 有關每個類別的活頁簿的詳細資訊，請參閱下面的[詳細存儲活頁簿](#detailed-storage-workbooks)部分。 

>[!NOTE]
>有關哪些錯誤可以在報表中顯示的詳細資訊，請參閱[回應類型架構](../../storage/common/storage-metrics-in-azure-monitor.md#metrics-dimensions)並查找回應類型，如**伺服器其他錯誤**、**用戶端其他錯誤**、**用戶端限制錯誤**等。 根據所選的存儲帳戶，如果報告的錯誤類型超過三種，則所有其他錯誤都表示為 **"其他**" 類別。

預設**可用性**閾值為：

* 警告 - 99%
* 嚴重 - 90%

要根據觀察結果或要求設置可用性閾值，請查看[修改可用性閾值](#modify-the-availability-threshold)。 

### <a name="capacity-workbook"></a>容量活頁簿

選擇頁面頂部的 **"容量**"，然後打開 **"容量**"活頁簿。 它顯示帳戶中使用的總存儲量和帳戶中每個資料服務用於説明識別已使用存儲的容量。

![多個存儲帳戶 容量活頁簿](./media/storage-insights-overview/storage-account-capacity-02.png) 

活頁簿中用於報告具有藍色值的容量指標的列有條件顏色編碼或熱圖。 最深的顏色具有最高值，較淺的顏色基於最低值。

當您在活頁簿中的任何一列下選擇值時，將向下切入到存儲帳戶的 **"容量**"活頁簿。 有關向下切入報表的詳細資訊，請參閱下面的[詳細存儲活頁簿](#detailed-storage-workbooks)部分。 

## <a name="view-from-a-storage-account"></a>從存儲帳戶查看

要直接從存儲帳戶訪問 VM 的 Azure 監視器，：

1. 在 Azure 門戶中，選擇存儲帳戶。

2. 從清單中選擇存儲帳戶。 在"監視"部分中，選擇"見解"（預覽）。

    ![選定的存儲帳戶概述頁](./media/storage-insights-overview/storage-account-direct-overview-01.png)

在存儲帳戶**的概述**活頁簿上，它顯示了幾個存儲性能指標，可説明您快速評估：

* 存儲服務的運行狀況，可立即查看超出您的控制範圍的問題是否影響所部署到的區域的存儲服務，該列位於 **"摘要"** 列下。

* 互動式性能圖表，顯示與存儲容量、可用性、事務和延遲相關的最基本詳細資訊。  

* 突出顯示服務可用性、存儲服務事務總數、E2E 延遲和伺服器延遲的指標和狀態磁貼。

為**失敗**、**性能**、**可用性**和**容量**選擇任一按鈕將打開相應的活頁簿。 

![選定的存儲帳戶概述頁](./media/storage-insights-overview/storage-account-capacity-01.png)

## <a name="detailed-storage-workbooks"></a>詳細的存儲活頁簿

無論您是在列**可用性****、E2E 延遲**、**伺服器延遲**和**事務錯誤類型/錯誤**從多個存儲帳戶**概述**活頁簿中選擇值，還是從特定存儲帳戶的概述活頁簿中選擇"**故障**"、"**性能**"、"**可用性**"和 **"容量****"** 中的任何一個按鈕，每個按鈕都會提供一組針對該類別定制的互動式存儲相關資訊。  

* **可用性**打開**可用性**活頁簿。 它顯示 Azure 存儲服務的當前運行狀況狀態、顯示每個物件的可用運行狀況狀態，該表由存儲帳戶中定義的資料服務分類，趨勢線表示所選時間範圍，以及可用性趨勢圖帳戶中的每個資料服務。  

    ![可用性報告示例](./media/storage-insights-overview/storage-account-availability-01.png)

* **E2E 延遲**和**伺服器延遲**將打開**性能**活頁簿。 它包括顯示 E2E 延遲和伺服器延遲的匯總狀態磁貼、E2E 與伺服器延遲的性能圖表，以及按存儲帳戶中定義的資料服務分類的 API 成功調用的表。

    ![績效報告示例](./media/storage-insights-overview/storage-account-performance-01.png)

* 選擇網格中列出的任何錯誤類別將打開**失敗**活頁簿。 報表顯示除描述錯誤和成功請求之外的所有其他用戶端錯誤的指標磁貼、用戶端限制錯誤、特定于 ClientOtherError 屬性的事務**回應類型**維度指標的性能圖表以及兩個表 -**按 API 名稱的事務**和**按回應類型的事務**。

   ![失敗報告示例](./media/storage-insights-overview/storage-account-failures-01.png)

* **容量**打開**容量**活頁簿。 它顯示磁貼和圖表中帳戶中每個存儲資料物件的存儲總量，以及帳戶中存儲的資料物件數。  

    ![選定的存儲帳戶容量頁](./media/storage-insights-overview/storage-account-capacity-01.png) 

## <a name="pin-and-export"></a>針和匯出

您可以通過選擇分區右上角的圖釘圖示，將任一指標部分固定到 Azure 儀表板。

![指標節針到儀表板示例](./media/storage-insights-overview/workbook-pin-example.png)

多訂閱和存儲帳戶**概述**或**容量**活頁簿支援通過選擇圖釘圖示右側的向下箭頭圖示來匯出 Excel 格式的結果。

![匯出活頁簿網格結果示例](./media/storage-insights-overview/workbook-export-example.png)

## <a name="customize-azure-monitor-for-storage-preview"></a>自訂 Azure 監視器以進行存儲（預覽）

本節重點介紹用於編輯活頁簿以支援資料分析需求進行自訂的常見方案：

* 將活頁簿的範圍範圍範圍，以便始終選擇特定的訂閱或存儲帳戶
* 更改網格中的指標
* 更改可用性閾值
* 更改顏色渲染

自訂項將保存到自訂活頁簿中，以防止覆蓋我們已發佈的活頁簿中的預設配置。 活頁簿保存在資源組中，無論是在您專用的"**我的報告"** 部分中，還是"**共用報表**"部分中，有權訪問資源組的每個人都可以訪問。 保存自訂活頁簿後，需要轉到活頁簿庫來啟動它。

![從命令列啟動活頁簿庫](./media/storage-insights-overview/workbook-command-bar-gallery.png)

### <a name="specifying-a-subscription-or-storage-account"></a>指定訂閱或存儲帳戶

您可以將多訂閱和存儲帳戶**概述**或**容量**活頁簿配置為每次運行時限定為特定訂閱或存儲帳戶，執行以下步驟。

1. 從門戶中選擇 **"監視器**"，然後從左側窗格中選擇 **"存儲帳戶（預覽）。"**

2. 在 **"概述**"活頁簿上，從命令列中選擇 **"編輯**"。

3. 從 **"訂閱**"下拉清單中選擇一個或多個您希望預設訂閱的訂閱。 請記住，活頁簿支援選擇最多 10 個訂閱。  

4. 從 **"存儲帳戶**"下拉清單中選擇您希望預設的一個或多個帳戶。 請記住，活頁簿支援選擇最多 200 個存儲帳戶。 

5. 選擇"從命令列**中保存為"** 以保存活頁簿的副本與自訂項，然後按一下 **"完成編輯**"以返回到讀取模式。  

### <a name="modify-metrics-and-colors-in-the-workbook"></a>修改活頁簿中的指標和顏色

預構建的活頁簿包含指標資料，您可以修改或刪除任何一個視覺化效果，並根據您的團隊的特定需求進行自訂。

在我們的示例中，我們使用多訂閱和存儲帳戶容量活頁簿，以演示如何：

* 刪除指標
* 更改顏色渲染

您可以針對任何預構建**的失敗**、**性能**、**可用性**和**容量**活頁簿執行相同的更改。

1. 從門戶中選擇 **"監視器**"，然後從左側窗格中選擇 **"存儲帳戶（預覽）。"**

2. 選擇 **"容量**"以切換到容量活頁簿，然後從命令列中選擇"從命令列**進行編輯**"。

    ![選擇編輯以修改活頁簿](./media/storage-insights-overview/workbook-edit-workbook.png)

3. 在指標部分旁邊，選擇 **"編輯**"。

    ![選擇"編輯"以修改容量活頁簿指標](./media/storage-insights-overview/edit-metrics-capacity-workbook-01.png)

4. 我們將刪除"**帳戶使用的容量時間表"** 列，因此在指標網格中選擇**列設置**。

    ![編輯列設置](./media/storage-insights-overview/edit-capacity-workbook-resource-grid.png)

5. 在 **"編輯"列設置**窗格中，在 **"列**"部分**Microsoft.存儲/存儲帳戶 -容量-使用容量時間表$下選擇。帳戶使用的容量時間軸 $**， 下拉清單**下列呈現器**選擇**隱藏**.

6. 選擇 **"保存"並關閉**以提交更改。

現在，讓我們更改報表中的容量指標的顏色主題，以使用綠色而不是藍色。

1. 在指標網格中選擇**列設置**。

2. In the **Edit column settings** pane, select under the **Columns** section **microsoft.storage/storageaccounts-Capacity-UsedCapacity$|microsoft.storage/storageaccounts/blobservices-Capacity-BlobCapacity$|microsoft.storage/storageaccounts/fileservices-Capacity-FileCapacity$|microsoft.storage/storageaccounts/queueservices-Capacity-QueueCapacity$|microsoft.storage/storageaccounts/tableservices-Capacity-TableCapacity$**. 在下拉清單 **"調色板**"下，選擇 **"綠色**"。

3. 選擇 **"保存"並關閉**以提交更改。

4. 選擇"從命令列**中保存為"** 以保存活頁簿的副本與自訂項，然後按一下 **"完成編輯**"以返回到讀取模式。  

### <a name="modify-the-availability-threshold"></a>修改可用性閾值

在此示例中，我們使用存儲帳戶容量活頁簿，並演示如何修改可用性閾值。 預設情況下，磁貼和網格報告百分比可用性配置為最小閾值為 90，最大閾值為 99。 我們將將 **"可用****點"網格中可用性% 的**最小閾值更改為 85%，這意味著如果閾值小於 85%，運行狀況狀態將更改為嚴重。 

1. 從門戶中選擇**存儲帳戶**，然後從清單中選擇存儲帳戶。

2. 從左側窗格中選擇**見解（預覽）。**

3. 在活頁簿中，選擇 **"可用性**"以切換到可用性活頁簿，然後從命令列中選擇 **"編輯**"。 

4. 向下滾動到頁面底部和左側的 API**網格可用性**旁邊，選擇 **"編輯**"。

    ![按 API 名稱網格設置編輯可用性](./media/storage-insights-overview/availability-workbook-avail-by-apiname.png)

5. 選擇**列設置**，然後在 **"編輯列設置"** 窗格中，在 **"列"** 部分下選擇 **"可用性 （%） "（閾值 = 格式化）**.

6. 將 **"嚴重**運行狀況狀態"的值從**90**更改為**85，** 然後按一下"**保存並關閉**"。

    ![修改關鍵狀態的可用性閾值](./media/storage-insights-overview/edit-column-settings-capacity-workbook-01.png)

7. 選擇"從命令列**中保存為"** 以保存活頁簿的副本與自訂項，然後按一下 **"完成編輯**"以返回到讀取模式。

## <a name="troubleshooting"></a>疑難排解

本節將説明您診斷和排除使用 Azure 監視器進行存儲（預覽）時可能遇到的一些常見問題。 請使用下列清單，找到與您的特定問題相關的資訊。

### <a name="resolving-performance-capacity-or-availability-issues"></a>解決性能、容量或可用性問題

為了説明解決使用 Azure 監視器進行存儲（預覽）確定的任何與存儲相關的問題，請參閱 Azure 存儲[故障排除指南](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance)。  

### <a name="why-can-i-only-see-200-storage-accounts"></a>為什麼我只能看到 200 個存儲帳戶？

所選存儲帳戶數的限制為 200，而不考慮所選的訂閱數。

### <a name="what-happens-when-i-click-on-a-recently-pinned-tile-in-the-dashboard"></a>按一下儀表板中最近固定的磁貼時會發生什麼情況？

* 如果按一下磁貼上的任意位置，它將將帶您到固定磁貼的選項卡。 例如，如果在"存儲帳戶概覽"選項卡中固定圖形，則當您按一下儀表板中的磁貼時，它將打開該預設視圖，但是，如果您從自己的已保存副本中固定圖形，則該圖形將打開已保存的副本的視圖。
* 標題左上角的篩選器圖示將打開"配置磁貼設置"選項卡。
* 右上角的橢圓圖示將為您提供"自訂標題資料"、"自訂"、"刷新"和"從儀表板中刪除"的選項。

### <a name="what-happens-when-i-save-a-workbook"></a>保存活頁簿時會發生什麼情況？

* 保存活頁簿時，它允許您使用編輯創建活頁簿的新副本並更改標題。 保存不會覆蓋活頁簿，當前活頁簿將始終是預設視圖。
* **未保存**的活頁簿只是預設視圖。


### <a name="why-dont-i-see-all-my-subscriptions-in-the-portal"></a>為什麼我在門戶中看不到所有訂閱？

門戶將僅在門戶啟動時顯示所選訂閱的資料。 要更改所選訂閱的內容，請轉到右上角，然後按一下帶有篩選器圖示的筆記本。 這將顯示目錄 + 訂閱選項卡。

![目錄 + 訂閱](./media/storage-insights-overview/fqa3.png)

### <a name="how-to-change-the-coloring-and-threshold-for-availability"></a>如何更改可用性的著色和閾值？

有關如何更改顏色和閾值的可用性的詳細步驟，請參閱["修改可用性閾值"](storage-insights-overview.md#modify-the-availability-threshold)部分。

### <a name="how-to-analyze-and-troubleshoot-the-data-shown-in-azure-monitor-for-storage"></a>如何分析和排除 Azure 監視器中顯示的資料以進行存儲？

 有關如何分析和排除 Azure 監視器中存儲監視器中顯示的 Azure 存儲資料的詳細資訊，請參閱["監視、診斷和故障排除 Microsoft Azure 存儲](https://docs.microsoft.com/azure/storage/common/storage-monitoring-diagnosing-troubleshooting)"一文。

### <a name="why-dont-i-see-all-the-types-of-errors-in-metrics"></a>為什麼我看不到指標中的所有類型的錯誤？

目前，最多顯示三種不同類型的錯誤，其餘錯誤在單個存儲桶中分組。 它使用拆分限制進行控制，可以修改。 要更改此屬性：

1. 按一下編輯活頁簿。
2. 轉到指標，按一下編輯，然後選擇 **"交易記錄"、"總和**"或要編輯的任何指標。

    ![轉到指標，然後按一下編輯，然後點擊"交易，總和"](./media/storage-insights-overview/fqa7.png)

1. 然後更改拆分數。

    ![選擇指標參數"](./media/storage-insights-overview/fqa7-2.png)

如果要看到 n 種不同類型的錯誤，請將拆分比 Limit 指定為 n+1，則對於其餘錯誤，請額外看到 1 個錯誤。

###  <a name="i-saved-my-workbook-while-on-some-storage-account-why-cant-i-find-it-now"></a>我在某個存儲帳戶上保存了活頁簿。 為什麼我現在找不到它？

每個活頁簿都保存在您將其保存在的存儲帳戶中。 嘗試查找使用者在其中保存活頁簿的特定存儲帳戶。 否則，在不知道資源（存儲帳戶）的情況下，無法查找特定的活頁簿。

### <a name="what-is-time-range"></a>什麼是時間範圍？

時間範圍顯示來自特定時間範圍的資料。 例如，如果時間範圍為 24 小時，則顯示過去 24 小時的資料。

### <a name="what-is-time-granularity-time-grain"></a>什麼是時間細微性（時間細微性）？

時間細微性是兩個資料點之間的時差。 例如，如果時間細微性設置為 1 秒，這意味著每秒收集指標。

### <a name="what-is-the-time-granularity-once-we-pin-any-part-of-the-workbooks-to-a-dashboard"></a>將活頁簿的任何部分固定到儀表板後，時間細微性是多少？

預設時間細微性設置為自動，當前無法更改。

### <a name="how-do-i-change-the-timespan-time-range-of-the-workbook-step-on-my-dashboard"></a>如何更改儀表板上活頁簿步驟的時間範圍/時間範圍？

預設情況下，儀表板磁貼上的時間跨度/時間範圍設置為 24 小時，要更改右上角的橢圓的按一下，請選擇 **"自訂磁貼資料**"，選中"覆蓋標題級別的儀表板時間設置"框，然後使用下拉式功能表選擇時間跨度。  

![選擇磁貼右角的橢圓，然後選擇"自訂此資料"](./media/storage-insights-overview/fqa-data-settings.png)

![在"配置磁貼設置"中選擇時間跨度下拉清單以更改時間跨度/時間範圍](./media/storage-insights-overview/fqa-timespan.png)

### <a name="how-do-i-change-the-title-of-the-workbook-or-a-workbook-step-i-pinned-to-a-dashboard"></a>如何更改活頁簿或固定到儀表板的活頁簿步驟的標題？

固定到儀表板的活頁簿或活頁簿步驟的標題將保留與活頁簿中相同的名稱。 要更改標題，必須保存自己的活頁簿副本。 然後，您可以在按保存之前命名活頁簿。

![選擇"保存在頂部"以保存活頁簿的副本並更改其名稱](./media/storage-insights-overview/fqa-change-workbook-name.png)

要更改已保存活頁簿中步驟的名稱，請選擇在步驟下進行編輯，然後在設置的最底部選擇齒輪。

![選擇在活頁簿步驟底部進行編輯以打開設置](./media/storage-insights-overview/fqa-edit.png)
![設置 在設置中選擇底部的齒輪，以便能夠更改步驟名稱](./media/storage-insights-overview/fqa-change-name.png)

## <a name="next-steps"></a>後續步驟

* 配置[指標警報](../platform/alerts-metric.md)[和服務運行狀況通知](../../service-health/alerts-activity-log-service-notifications.md)以設置自動警報以説明檢測問題。

* 瞭解活頁簿旨在支援的方案、如何通過查看[使用 Azure 監視器活頁簿創建互動式報表](../app/usage-workbooks.md)來編寫新報表和自訂現有報表等。

* 如需使用儲存體分析和其他工具來識別、診斷及疑難排解 Azure 儲存體相關問題的深入指南，請參閱 [監視、診斷及疑難排解 Microsoft Azure 儲存體](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md)。
