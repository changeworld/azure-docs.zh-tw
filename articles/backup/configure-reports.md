---
title: 設定 Azure 備份報告
description: 使用紀錄分析與 Azure 工作簿設定及檢視 Azure 備份的報告
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 497d70c1bcc577faa467720b959eb828e785a26a
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80672654"
---
# <a name="configure-azure-backup-reports"></a>設定 Azure 備份報告

備份管理員的常見要求是根據跨越很長時間的數據獲取備份見解。 此類解決方案可能有多個用例 - 分配和預測消耗的雲端儲存,審核備份和還原,並在不同粒度級別辨識關鍵趨勢。

如今,Azure 備份提供了一個報告解決方案,該解決方案利用[Azure 監視器日誌](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)和[Azure 工作簿](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks),説明您在整個備份區獲得有關備份的豐富見解。 本文介紹如何配置和查看備份報告。

## <a name="supported-scenarios"></a>支援的案例

* Azure VM、Azure VM 中的 SQL、Azure VM 中的 SAP HANA/ASE、Azure 備份代理 (MARS)、Azure 備份伺服器 (MABS) 和系統中心 DPM 都支援備份報告。
* 對於 DPM 工作負載,DPM 版本 5.1.363.0 及以上支援備份報告,以及代理版本 2.0.9127.0 及以上。
* 對於 MABS 工作負載,MABS 版本 13.0.415.0 及以上支援備份報告,以及代理版本 2.0.9170.0 及以上。
* 只要備份報告的數據發送到使用者有權訪問的日誌分析 (LA) 工作區,就可以跨所有備份專案、保管庫、訂閱和區域查看備份報告。 請注意,要查看一組保管庫的報表,只需**讓讀者訪問**保管庫向其發送數據的 LA 工作區。 您**無需**存取各個保管庫。
* 如果您是 Azure[燈塔](https://docs.microsoft.com/azure/lighthouse/)使用者,具有對客戶訂閱的委派訪問許可權,則可以使用 Azure 燈塔的這些報表查看所有租戶的報表。
* 日誌備份作業的數據當前未顯示在報表中。

## <a name="getting-started"></a>開始使用

要開始使用報表,請按照以下三個步驟進行操作:

1. **建立紀錄分析 (LA) 工作區(或使用現有工作區):**

您需要設定一個或多個 LA 工作區來存儲備份報告數據。 可以創建此 LA 工作區的位置和訂閱與存在保管庫的位置和訂閱無關。 

請參閱以下文章:在[Azure 門戶中創建日誌分析工作區](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)以設置 LA 工作區。

默認情況下,LA 工作區中的數據將保留 30 天。 要查看時間跨度較長的數據,更改 LA 工作區的保留期。 要變更保留期,請參閱以下文章:[使用 Azure 監視器紀錄管理使用方式和成本](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage)。

2. **為保管庫設定診斷設定:**

Azure 資源管理員資源(如恢復服務保管庫)將有關計劃工序和使用者觸發操作的資訊記錄為診斷數據。 

在恢復服務保管庫的監視部分中,選擇**診斷設置**並指定恢復服務保管庫的診斷數據的目標。 [瞭解此問題的資訊 。](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events)

![診斷設定刀片](./media/backup-azure-configure-backup-reports/resource-specific-blade.png)

Azure 備份還提供內置 Azure 策略,它自動配置給定作用域中所有保管庫的診斷設置。 請參考以下文章以瞭解如何使用此政策:[大規模設定保管庫診斷設定](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics)

> [!NOTE]
> 配置診斷後,可能需要長達 24 小時才能完成初始數據推送。 數據開始流入 LA 工作區後,可能無法立即在報表中看到數據,因為當前部分日的數據不會顯示在報表中([此處](https://docs.microsoft.com/azure/backup/configure-reports#conventions-used-in-backup-reports)的更多詳細資訊)。 因此,建議在配置保管庫以將數據發送到日誌分析 2 天後開始查看報告。

3. **在 Azure 門戶上查看報表:**

將保管庫配置為將資料發送到洛杉磯後,通過導航到任何保管庫的邊欄選項卡並按一**下 「備份報告」** 功能表項來查看備份報告。 

![保存庫儀表板](./media/backup-azure-configure-backup-reports/vault-dashboard.png)

按一下此連結將打開備份報表工作簿。

> [!NOTE]
> * 目前,報告的初始負載可能需要長達 1 分鐘。
> * 恢復服務保管庫只是備份報告的一個入口點。 從保管庫的邊欄選項卡打開備份報表工作簿后,您將能夠查看跨所有保管庫聚合的數據(通過選擇適當的 LA 工作區集)。

下面是報表包含的各種選項卡的說明:

* **摘要**- 「摘要」選項卡提供備份空間的高級概述。 在「摘要」選項卡下,您可以快速查看備份項總數、消耗的雲端儲存總數、受保護實例數以及每個工作負載類型的作業成功率。 有關特定備份項目類型的更多詳細資訊,導航到相應的選項卡。

![[摘要] 索引標籤](./media/backup-azure-configure-backup-reports/summary.png)

* **備份專案**- 「備份專案」選項卡允許您查看在備份專案級別使用的雲端儲存資訊和趨勢。 例如,如果在 Azure VM 備份中使用 SQL,則可以看到要備份的每個 SQL 資料庫都使用的雲端儲存。 您還可以選擇查看特定保護狀態的備份項目的數據。 例如,按一下選項卡頂部的「**保護停止」** 磁貼,篩選以下所有小部件,以僅顯示處於「保護停止」狀態的備份項目的數據。

![備份項目選項卡](./media/backup-azure-configure-backup-reports/backup-items.png)

* **使用方式**- 「使用方式」選項卡可説明您查看備份的關鍵計費參數。 此選項卡中顯示的資訊位於計費實體(受保護容器)級別。 例如,在 DPM 伺服器備份到 Azure 的情況下,您可以查看 DPM 伺服器使用的受保護實例和雲端儲存的趨勢。 同樣,如果您在 Azure 備份中使用 SQL 或在 Azure 備份中使用 SAP HANA,此選項卡在這些資料庫中包含的虛擬機器級別提供與使用方式相關的資訊。

![使用方式選項卡](./media/backup-azure-configure-backup-reports/usage.png)

* **作業**- '工作"選項卡允許您查看作業的長期運行趨勢,例如每天失敗的作業數和作業失敗的首要原因。 您可以在聚合等級和備份專案級別查看此資訊。 按下格線中的特定備份項,您可以查看所選時間範圍內對該備份項上觸發的每個作業的詳細資訊。

![工作索引標籤](./media/backup-azure-configure-backup-reports/jobs.png)

* **策略**- 「策略」選項卡允許您查看所有活動策略的資訊,例如關聯專案的數量,以及根據給定策略備份的專案使用的總雲存儲。 按一下特定策略可以查看有關其每個關聯備份項的資訊。

![原則選項卡](./media/backup-azure-configure-backup-reports/policies.png)

## <a name="exporting-to-excel"></a>匯出到 Excel

按一下任何小元件(表/圖表)右上角的向下箭頭按鈕,將該小部件的內容匯出為 Excel 工作表,按現有篩選器應用。 要將表的更多行匯出到 Excel,可以使用每個網格頂部的 **「每頁行**」下拉清單來增加頁面上顯示的行數。

## <a name="pinning-to-dashboard"></a>固定到儀表板

按一下每個小部件頂部的「固定圖示」,將小部件固定到 Azure 門戶儀表板。 這有助於您創建定製的儀錶板,以顯示所需的最重要的資訊。

## <a name="cross-tenant-reports"></a>交叉租戶報告

如果您是 Azure[燈塔](https://docs.microsoft.com/azure/lighthouse/)使用者,具有對跨多個租戶環境的訂閱的委派訪問許可權,則可以使用預設訂閱篩選器(通過單擊 Azure 門戶右上角的篩選器圖示)選擇要查看數據的所有訂閱。 這樣做將允許您跨租戶選擇 LA 工作區以查看多租戶報表。

## <a name="conventions-used-in-backup-reports"></a>備份報告中使用的約定

* 篩選器在每個選項卡上從左到右和從上到下工作,也就是說,任何篩選器僅適用於定位在該篩選器右側或篩選器下方的所有小部件。 
* 單擊彩色磁貼可篩選磁貼下方的微件,以查找與該磁貼的值相關的記錄。 例如,按一下「備份專案」選項卡中的 *「保護停止*」磁貼會篩選下面的網格和圖表,以顯示處於「保護已停止」狀態的備份項目的數據。
* 未著色的磁貼不可單擊。
* 報表中不會顯示目前部分日期的資料。 因此,當 **「時間範圍**」的選定值***為「最近 7 天***」時,報表將顯示最近 7 個已完成天數(不包括當前日期)的記錄。
* 該報告顯示在選定時間範圍內**觸發**的作業(日誌作業外)的詳細資訊。 
* 實體儲存與受保護實體顯示的值位於選取時間範圍的**末尾**。
* 報表中顯示的備份專案是在所選時間範圍**末尾**存在的項。 不會顯示在選定時間範圍中間刪除的備份專案。 相同的約定也適用於備份策略。

## <a name="query-load-times"></a>查詢載入時間

備份報告中的小部件由 Kusto 查詢提供支援,這些查詢在使用者的 LA 工作區上運行。 由於這些查詢通常涉及處理大量數據,以及多個聯接以啟用更豐富的見解,因此當使用者跨大型備份區查看報表時,小部件可能無法立即載入。 下表根據備份項數和查看報表的時間範圍,粗略估計了不同小部件載入的時間:

| **• 資料來源**                         | **時間地平線** | **載入時間(約)**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ±5 K                       | 1 個月          | 瓷磚: 5-10 秒 <br> 網線:5-10 秒 <br> 圖表:5-10 秒 <br> 報告中過濾器:5-10 秒|
| ±5 K                       | 3 個月          | 瓷磚: 5-10 秒 <br> 網線:5-10 秒 <br> 圖表:5-10 秒 <br> 報告中過濾器:5-10 秒|
| Φ10 K                       | 3 個月          | 瓷磚: 15-20 秒 <br> 網線:15-20 秒 <br> 圖表: 1-2 分鐘 <br> 報告級過濾器:25-30 秒|
| Φ15 K                       | 1 個月          | 瓷磚: 15-20 秒 <br> 網線:15-20 秒 <br> 圖表:50-60 秒 <br> 報告級過濾器:20-25 秒|
| Φ15 K                       | 3 個月          | 瓷磚: 20-30 秒 <br> 網線:20-30 秒 <br> 圖表: 2-3 分鐘 <br> 報告級過濾器:50-60 秒 |

## <a name="what-happened-to-the-power-bi-reports"></a>Power BI 報告發生了什麼情況?
* 我們較早的 Power BI 樣本應用用於報告(從 Azure 儲存帳戶獲取數據)位於棄用路徑上。 建議開始發送保管庫診斷數據到日誌分析如上所述,以查看報告。

* 此外,將診斷數據發送到存儲帳戶或 LA 工作區的 V1 架構也位於棄用路徑上。 這意味著,如果您已根據 V1 架構編寫了任何自定義查詢或自動化,則建議您更新這些查詢以使用當前支援的 V2 架構。

## <a name="next-steps"></a>後續步驟
[瞭解有關使用 Azure 備份進行監視和報告的更多內容](https://docs.microsoft.com/azure/backup/backup-azure-monitor-alert-faq)