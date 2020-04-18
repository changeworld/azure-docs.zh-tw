---
title: 設定 Azure 備份報告
description: 使用紀錄分析與 Azure 工作簿設定及檢視 Azure 備份的報告
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: bcc87deb19190149329369ca58f54b45b62b41fe
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617807"
---
# <a name="configure-azure-backup-reports"></a>設定 Azure 備份報告

備份管理員的常見要求是根據跨越很長時間的數據獲取備份的見解。 此類解決方案的用例包括:

 - 分配和預測雲存儲的消耗。
 - 審核備份和還原。
 - 在不同粒度級別識別關鍵趨勢。

今天,Azure 備份提供了一個使用[Azure 監視器日誌](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)和[Azure 工作簿](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)的報告解決方案。 這些資源可説明您在整個備份區獲得有關備份的豐富見解。 本文介紹如何配置和查看 Azure 備份報告。

## <a name="supported-scenarios"></a>支援的案例

* Azure VM、Azure VM 中的 SQL、Azure VM 中的 SAP HANA/ASE、Microsoft Azure 恢複服務 (MARS) 代理、Microsoft Azure 備份伺服器 (MABS) 和系統中心數據保護管理器 (DPM) 都支援備份報告。
* 對於 DPM 工作負載,DPM 版本 5.1.363.0 及以上版本以及代理版本 2.0.9127.0 及以上支持備份報告。
* 對於 MABS 工作負載,MABS 版本 13.0.415.0 及以上版本以及代理版本 2.0.9170.0 及以上支援備份報告。
* 只要備份報告的數據發送到使用者有權訪問的日誌分析工作區,就可以跨所有備份專案、保管庫、訂閱和區域查看備份報告。 要查看一組保管庫的報告,只需讓讀者訪問保管庫向其發送數據的日誌分析工作區。 您無需存取各個保管庫。
* 如果您是[Azure 燈塔](https://docs.microsoft.com/azure/lighthouse/)使用者,具有對客戶訂閱的委派訪問許可權,則可以使用 Azure 燈塔的這些報表查看所有租戶的報表。
* 日誌備份作業的數據當前未顯示在報表中。

## <a name="get-started"></a>開始使用

按照以下步驟開始使用報表。

#### <a name="1-create-a-log-analytics-workspace-or-use-an-existing-one"></a>1. 建立紀錄分析工作區或使用現有工作區

設置一個或多個日誌分析工作區以存儲備份報告數據。 可以創建此日誌分析工作區的位置和訂閱與存在保管庫的位置和訂閱無關。 

要設定紀錄分析工作區,請參閱在[Azure 門戶中建立紀錄分析工作區](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)。

默認情況下,日誌分析工作區中的數據將保留 30 天。 要查看時間跨度較長的數據,請更改日誌分析工作區的保留期。 要改變保留期,請參閱[使用 Azure 監視器紀錄管理使用方式和成本](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage)。

#### <a name="2-configure-diagnostics-settings-for-your-vaults"></a>2. 為保管庫設定診斷設定

Azure 資源管理員資源(如恢復服務保管庫)將有關計劃工序和使用者觸發操作的資訊記錄為診斷數據。 

在恢復服務保管庫的監視部分中,選擇 **「診斷設置」** 並指定恢復服務保管庫的診斷數據的目標。 要瞭解有關使用診斷事件的更多資訊,請參閱[使用恢復服務保管庫的診斷設定](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events)。

![診斷設定窗格](./media/backup-azure-configure-backup-reports/resource-specific-blade.png)

Azure 備份還提供內置 Azure 策略,該策略可自動配置給定作用域中所有保管庫的診斷設置。 要瞭解如何使用此政策,請參閱[大規模設定保管庫診斷設定](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics)。

> [!NOTE]
> 配置診斷後,可能需要長達 24 小時才能完成初始數據推送。 數據開始流入日誌分析工作區后,可能無法立即在報表中看到數據,因為報表中未顯示當前部分日的數據。 有關詳細資訊,請參閱[備份報表中使用的約定](https://docs.microsoft.com/azure/backup/configure-reports#conventions-used-in-backup-reports)。 我們建議您在將保管庫配置為將數據發送到日誌分析兩天后開始查看報告。

#### <a name="3-view-reports-in-the-azure-portal"></a>3. 在 Azure 門戶中查看報表

將保管庫配置為將資料發送到日誌分析後,請通過訪問任何保管庫的窗格並選擇 **「備份報告**」來查看備份報告。

![保存庫儀表板](./media/backup-azure-configure-backup-reports/vault-dashboard.png)

選擇此連結可打開備份報表工作簿。

> [!NOTE]
> * 目前,報告的初始負載可能需要長達 1 分鐘。
> * 恢復服務保管庫只是備份報告的一個入口點。 從保管庫的窗格打開備份報表工作簿后,選擇相應的日誌分析工作區集以查看跨所有保管庫聚合的數據。

此報告包含各種選項卡:

* **摘要**:使用此選項卡可以獲取備份空間的高級概述。 您可以快速瞭解備份項總數、消耗的雲端儲存總數、受保護實例數以及每個工作負載類型的作業成功率。 有關特定備份工件類型的更多詳細資訊,請造訪相應的選項卡。

   ![[摘要] 索引標籤](./media/backup-azure-configure-backup-reports/summary.png)

* **備份專案**:使用此選項卡可查看在備份專案級別使用的雲端儲存的資訊和趨勢。 例如,如果在 Azure VM 備份中使用 SQL,則可以看到正在備份的每個 SQL 資料庫使用的雲端儲存。 您還可以選擇查看特定保護狀態的備份項目的數據。 例如,選擇選項卡頂部的 **「保護停止」** 磁貼會篩選下面的所有小部件,以便僅顯示處於「保護停止」狀態的備份項目的數據。

   ![備份項目選項卡](./media/backup-azure-configure-backup-reports/backup-items.png)

* **使用方式**:使用此選項卡可查看備份的關鍵計費參數。 此選項卡上顯示的資訊位於計費實體(受保護容器)級別。 例如,在 DPM 伺服器備份到 Azure 的情況下,您可以查看 DPM 伺服器使用的受保護實例和雲端儲存的趨勢。 同樣,如果在 Azure 備份中使用 SQL 或在 Azure 備份中使用 SAP HANA,此選項卡在包含這些資料庫的虛擬機器級別提供與使用方式相關的資訊。

   ![使用方式選項卡](./media/backup-azure-configure-backup-reports/usage.png)

* **作業**:使用此選項卡可以查看作業的長期運行趨勢,例如每天失敗的作業數和作業失敗的首要原因。 您可以在聚合等級和備份項級別查看此資訊。 在網格中選擇特定的備份項,以查看在所選時間範圍內觸發該備份項的每個作業的詳細資訊。

   ![工作索引標籤](./media/backup-azure-configure-backup-reports/jobs.png)

* **策略**:使用此選項卡可以查看有關所有活動策略的資訊,例如關聯項的數量以及根據給定策略備份的專案消耗的總雲存儲。 選擇特定策略以查看有關其每個關聯的備份項目的資訊。

   ![原則選項卡](./media/backup-azure-configure-backup-reports/policies.png)

## <a name="export-to-excel"></a>匯出至 Excel

選擇任何小部件右上角的向下箭頭按鈕(如表格或圖表),以匯出該小部件的內容為 Excel 工作表,並應用現有篩選器。 要將表的更多行匯出到 Excel,可以使用每個網格頂部的 **「每頁行**」下拉箭頭增加頁面上顯示的行數。

## <a name="pin-to-dashboard"></a>釘選到儀表板

選擇每個小部件頂部的針按鈕,將小部件固定到 Azure 門戶儀錶板。 此功能可説明您創建定製的儀錶板,以顯示所需的最重要的資訊。

## <a name="cross-tenant-reports"></a>交叉租戶報告

如果使用[Azure 燈塔](https://docs.microsoft.com/azure/lighthouse/)對跨多個租戶環境的訂閱進行委派訪問,則可以使用預設訂閱篩選器。 選擇 Azure 門戶右上角的篩選器按鈕,以選擇要查看數據的所有訂閱。 這樣,您就可以在租戶中選擇日誌分析工作區以查看多租戶報表。

## <a name="conventions-used-in-backup-reports"></a>備份報告中使用的約定

* 每個選項卡上從左到右和從上到下都有篩選器工作。也就是說,任何篩選器僅適用於定位到該篩選器右側或該篩選器下方的所有小部件。 
* 選擇彩色磁貼會篩選磁貼下方與該磁貼的值相關的記錄的小部件。 例如,選擇 **「備份專案**」選項卡上的 **「保護停止**」磁貼會篩選下面的網格和圖表,以顯示處於「保護停止」狀態的備份項目的數據。
* 不著色的磁貼不可單擊。
* 報表中未顯示當前部分日的數據。 因此,當 **「時間範圍**」的選定值**為「過去 7 天**」時,報表將顯示最近 7 個已完成天的記錄。 不包括當前的日子。
* 該報告顯示在選定時間範圍內*觸發*的作業的詳細資訊(日誌作業外)。 
* 實體**儲存**與**保護實體顯示**的值在選取時間範圍的*末尾*。
* 報表中顯示的備份專案是在所選時間範圍*末尾*存在的項。 不會顯示在選定時間範圍中間刪除的備份專案。 相同的約定也適用於備份策略。

## <a name="query-load-times"></a>查詢載入時間

備份報告中的小部件由 Kusto 查詢提供支援,這些查詢在使用者的日誌分析工作區上運行。 這些查詢通常涉及處理大量數據,並有多個聯接,以實現更豐富的見解。 因此,當使用者查看跨大型備份區的報告時,小部件可能無法立即載入。 此表根據備份項數和查看報表的時間範圍,粗略估計不同小部件載入的時間。

| **• 資料來源**                         | **時間範圍** | **大致載入時間**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ±5 K                       | 1 個月          | 瓷磚: 5-10 秒 <br> 網線:5-10 秒 <br> 圖表:5-10 秒 <br> 報告中過濾器:5-10 秒|
| ±5 K                       | 3 個月          | 瓷磚: 5-10 秒 <br> 網線:5-10 秒 <br> 圖表:5-10 秒 <br> 報告中過濾器:5-10 秒|
| Φ10 K                       | 3 個月          | 瓷磚: 15-20 秒 <br> 網線:15-20 秒 <br> 圖表: 1-2 分鐘 <br> 報告級過濾器:25-30 秒|
| Φ15 K                       | 1 個月          | 瓷磚: 15-20 秒 <br> 網線:15-20 秒 <br> 圖表:50-60 秒 <br> 報告級過濾器:20-25 秒|
| Φ15 K                       | 3 個月          | 瓷磚: 20-30 秒 <br> 網線:20-30 秒 <br> 圖表: 2-3 分鐘 <br> 報告級過濾器:50-60 秒 |

## <a name="what-happened-to-the-power-bi-reports"></a> Power BI 報表發生什麼情況？
* 早期用於報告的 Power BI 樣本應用(從 Azure 儲存帳戶獲取數據)位於棄用路徑上。 我們建議您開始向日誌分析發送保管庫診斷數據以查看報告。

* 此外,將診斷數據發送到存儲帳戶或日誌分析工作區的 V1 架構也位於棄用路徑上。 如果您已根據 V1 架構編寫了任何自定義查詢或自動化,建議更新這些查詢以使用當前支援的 V2 架構。

## <a name="next-steps"></a>後續步驟
[瞭解有關使用 Azure 備份進行監視和報告的更多內容](https://docs.microsoft.com/azure/backup/backup-azure-monitor-alert-faq)
