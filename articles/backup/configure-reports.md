---
title: 設定 Azure 備份報告
description: 使用日誌分析和 Azure 活頁簿配置和查看 Azure 備份的報告
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 651d1383f0f292895ed95c91bafd5206d4f04c2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78161196"
---
# <a name="configure-azure-backup-reports"></a>設定 Azure 備份報告

備份管理員的常見要求是根據跨越很長時間的資料獲取備份見解。 此類解決方案可能有多個用例 - 分配和預測消耗的雲存儲，審核備份和還原，並在不同細微性級別識別關鍵趨勢。

如今，Azure 備份提供了一個報告解決方案，該解決方案利用[Azure 監視器日誌](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)和[Azure 活頁簿](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)，説明您在整個備份區獲得有關備份的豐富見解。 本文介紹如何配置和查看備份報告。

## <a name="supported-scenarios"></a>支援的案例

* Azure VM、Azure VM 中的 SQL、Azure VM 中的 SAP HANA/ASE、Azure 備份代理 （MARS）、Azure 備份伺服器 （MABS） 和系統中心 DPM 都支援備份報告。
* 對於 DPM 工作負載，DPM 版本 5.1.363.0 及以上支援備份報告，以及代理版本 2.0.9127.0 及以上。
* 對於 MABS 工作負載，MABS 版本 13.0.415.0 及以上支援備份報告，以及代理版本 2.0.9170.0 及以上。
* 只要備份報告的資料發送到使用者有權訪問的日誌分析 （LA） 工作區，就可以跨所有備份專案、保存庫、訂閱和區域查看備份報告。 
* 如果您是 Azure[燈塔](https://docs.microsoft.com/azure/lighthouse/)使用者，具有對客戶訂閱的委派存取權限，則可以使用 Azure 燈塔的這些報表查看所有租戶的報表。
* 記錄備份作業的資料當前未顯示在報表中。

## <a name="getting-started"></a>開始使用

要開始使用報表，請按照以下三個步驟進行操作：

1. **創建日誌分析 （LA） 工作區（或使用現有工作區）：**

您需要設置一個或多個 LA 工作區來存儲備份報告資料。 可以創建此 LA 工作區的位置和訂閱與存在保存庫的位置和訂閱無關。 

請參閱以下文章：在[Azure 門戶中創建日誌分析工作區](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)以設置 LA 工作區。

預設情況下，LA 工作區中的資料將保留 30 天。 要查看時間跨度較長的資料，更改 LA 工作區的保留期。 要更改保留期，請參閱以下文章：[使用 Azure 監視器日誌管理使用方式和成本](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage)。

2. **為保存庫配置診斷設置：**

Azure 資源管理器資源（如恢復服務保存庫）將有關計畫工序和使用者觸發操作的資訊記錄為診斷資料。 

在恢復服務保存庫的監視部分中，選擇**診斷設置**並指定恢復服務保存庫的診斷資料的目標。 [瞭解有關使用診斷事件的更多資訊](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events)。

![診斷設置刀片](./media/backup-azure-configure-backup-reports/resource-specific-blade.png)

Azure 備份還提供內置 Azure 策略，它自動設定給定作用域中所有保存庫的診斷設置。 請參閱以下文章以瞭解如何使用此策略：[大規模配置保存庫診斷設置](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics)

> [!NOTE]
> 配置診斷後，可能需要長達 24 小時才能完成初始資料推送。 資料開始流入 LA 工作區後，可能無法立即在報表中看到資料，因為當前部分日的資料不會顯示在報表中（[此處](https://docs.microsoft.com/azure/backup/configure-reports#conventions-used-in-backup-reports)的更多詳細資訊）。 因此，建議在配置保存庫以將資料發送到日誌分析 2 天后開始查看報告。

3. **在 Azure 門戶上查看報表：**

將保存庫配置為將資料發送到洛杉磯後，通過導航到任何保存庫的邊欄選項卡並按一下 **"備份報告"** 功能表項目來查看備份報告。 

![保存庫儀表板](./media/backup-azure-configure-backup-reports/vault-dashboard.png)

按一下此連結將打開備份報表活頁簿。

> [!NOTE]
> * 目前，報告的初始負載可能需要長達 1 分鐘。
> * 恢復服務保存庫只是備份報告的一個進入點。 從保存庫的邊欄選項卡打開備份報表活頁簿後，您將能夠查看跨所有保存庫聚合的資料（通過選擇適當的 LA 工作區集）。

下面是報表包含的各種選項卡的說明：

* **摘要**- "摘要"選項卡提供備份空間的高級概述。 在"摘要"選項卡下，您可以快速查看備份項總數、消耗的雲存儲總數、受保護實例數以及每個工作負載類型的作業成功率。 有關特定備份專案類型的更多詳細資訊，導航到相應的選項卡。

![[摘要] 索引標籤](./media/backup-azure-configure-backup-reports/summary.png)

* **備份專案**- "備份專案"選項卡允許您查看在備份專案級別使用的雲存儲資訊和趨勢。 例如，如果在 Azure VM 備份中使用 SQL，則可以看到要備份的每個 SQL 資料庫都使用的雲存儲。 您還可以選擇查看特定保護狀態的備份專案的資料。 例如，按一下選項卡頂部的"**保護停止"** 磁貼，篩選以下所有小部件，以僅顯示處於"保護停止"狀態的備份專案的資料。

![備份專案選項卡](./media/backup-azure-configure-backup-reports/backup-items.png)

* **使用方式**- "使用方式"選項卡可説明您查看備份的關鍵計費參數。 此選項卡中顯示的資訊位於計費實體（受保護容器）級別。 例如，在 DPM 服務器備份到 Azure 的情況下，您可以查看 DPM 服務器使用的受保護實例和雲存儲的趨勢。 同樣，如果您在 Azure 備份中使用 SQL 或在 Azure 備份中使用 SAP HANA，此選項卡在這些資料庫中包含的虛擬機器級別提供與使用方式相關的資訊。

![使用方式選項卡](./media/backup-azure-configure-backup-reports/usage.png)

* **作業**- "作業"選項卡允許您查看作業的長期運行趨勢，例如每天失敗的作業數和作業失敗的首要原因。 您可以在聚合級別和備份專案級別查看此資訊。 按一下網格中的特定備份項，您可以查看所選時間範圍內對該備份項上觸發的每個作業的詳細資訊。

![工作索引標籤](./media/backup-azure-configure-backup-reports/jobs.png)

* **策略**- "策略"選項卡允許您查看所有活動策略的資訊，例如關聯專案的數量，以及根據給定策略備份的專案使用的總雲存儲。 按一下特定策略可以查看有關其每個關聯備份項的資訊。

![策略選項卡](./media/backup-azure-configure-backup-reports/policies.png)

## <a name="exporting-to-excel"></a>匯出到 Excel

按一下任何小部件（表/圖表）右上角的向下箭頭按鈕，將該小部件的內容匯出為 Excel 工作表，按現有篩選器應用。 要將表的更多行匯出到 Excel，可以使用每個網格頂部的 **"每頁行**"下拉清單來增加頁面上顯示的行數。

## <a name="pinning-to-dashboard"></a>固定到儀表板

按一下每個小部件頂部的"固定圖示"，將小部件固定到 Azure 門戶儀表板。 這有助於您創建定制的儀表板，以顯示所需的最重要的資訊。

## <a name="cross-tenant-reports"></a>交叉租戶報告

如果您是 Azure[燈塔](https://docs.microsoft.com/azure/lighthouse/)使用者，具有對跨多個租戶環境的訂閱的委派存取權限，則可以使用預設訂閱篩選器（通過按一下 Azure 門戶右上角的篩選器圖示）選擇要查看資料的所有訂閱。 這樣做將允許您跨租戶選擇 LA 工作區以查看多租戶報表。

## <a name="conventions-used-in-backup-reports"></a>備份報告中使用的約定

* 篩選器在每個選項卡上從左至右和從上到下工作，也就是說，任何篩選器僅適用于定位在該篩選器右側或篩選器下方的所有小部件。 
* 按一下彩色磁貼可篩選磁貼下方的微件，以查找與該磁貼的值相關的記錄。 例如，按一下"備份專案"選項卡中的 *"保護停止*"磁貼會篩選下面的網格和圖表，以顯示處於"保護已停止"狀態的備份專案的資料。
* 未著色的磁貼不可按一下。
* 報表中不會顯示目前部分日期的資料。 因此，當 **"時間範圍**"的選定值***為"最近 7 天***"時，報表將顯示最近 7 個已完成天數（不包括當前日期）的記錄。
* 該報告顯示在選定時間範圍內**觸發**的作業（日誌作業外）的詳細資訊。 
* 雲存儲和受保護實例顯示的值位於所選時間範圍的**末尾**。
* 報表中顯示的備份專案是在所選時間範圍**末尾**存在的項。 不會顯示在選定時間範圍中間刪除的備份專案。 相同的約定也適用于備份策略。

## <a name="query-load-times"></a>查詢載入時間

備份報告中的小部件由 Kusto 查詢提供支援，這些查詢在使用者的 LA 工作區上運行。 由於這些查詢通常涉及處理大量資料，以及多個聯接以啟用更豐富的見解，因此當使用者跨大型備份區查看報表時，小部件可能無法立即載入。 下表根據備份項數和查看報表的時間範圍，粗略估計了不同小部件載入的時間：

| **• 資料來源**                         | **時間地平線** | **載入時間（約）**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ±5 K                       | 1 個月          | 瓷磚： 5-10 秒 <br> 網格：5-10 秒 <br> 圖表：5-10 秒 <br> 報告級篩檢程式：5-10 秒|
| ±5 K                       | 3 個月          | 瓷磚： 5-10 秒 <br> 網格：5-10 秒 <br> 圖表：5-10 秒 <br> 報告級篩檢程式：5-10 秒|
| Φ10 K                       | 3 個月          | 瓷磚： 15-20 秒 <br> 網格：15-20 秒 <br> 圖表： 1-2 分鐘 <br> 報告級篩檢程式：25-30 秒|
| Φ15 K                       | 1 個月          | 瓷磚： 15-20 秒 <br> 網格：15-20 秒 <br> 圖表：50-60 秒 <br> 報告級篩檢程式：20-25 秒|
| Φ15 K                       | 3 個月          | 瓷磚： 20-30 秒 <br> 網格：20-30 秒 <br> 圖表： 2-3 分鐘 <br> 報告級篩檢程式：50-60 秒 |

## <a name="what-happened-to-the-power-bi-reports"></a>Power BI 報告發生了什麼情況？
* 我們較早的 Power BI 範本應用用於報告（從 Azure 存儲帳戶獲取資料）位於棄用路徑上。 建議開始發送保存庫診斷資料到日誌分析如上所述，以查看報告。

* 此外，將診斷資料發送到存儲帳戶或 LA 工作區的 V1 架構也位於棄用路徑上。 這意味著，如果您已根據 V1 架構編寫了任何自訂查詢或自動化，則建議您更新這些查詢以使用當前支援的 V2 架構。

## <a name="next-steps"></a>後續步驟
[瞭解有關使用 Azure 備份進行監視和報告的更多內容](https://docs.microsoft.com/azure/backup/backup-azure-monitor-alert-faq)