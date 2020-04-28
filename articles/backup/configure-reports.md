---
title: 設定 Azure 備份報告
description: 使用 Log Analytics 和 Azure 活頁簿來設定及查看 Azure 備份的報表
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: c1af9a532b390b428e74957c455988dfd4df3967
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82184940"
---
# <a name="configure-azure-backup-reports"></a>設定 Azure 備份報告

備份管理員的常見需求是根據長時間內的資料，取得備份的深入解析。 這類解決方案的使用案例包括：

- 配置和預測耗用的雲端儲存體。
- 備份和還原的審核。
- 識別不同資料細微性層級的主要趨勢。

目前，Azure 備份提供使用[Azure 監視器記錄](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)和[Azure 活頁簿](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)的報表解決方案。 這些資源可協助您在整個備份資產中，取得有關備份的豐富見解。 本文說明如何設定及查看 Azure 備份報表。

## <a name="supported-scenarios"></a>支援的案例

- Azure vm、azure vm 中的 SQL、Azure Vm 中的 SAP Hana/ASE、Microsoft Azure 復原服務（MARS）代理程式、Microsoft Azure 備份 Server （MABS）和 System Center Data Protection Manager （DPM）皆支援備份報表。 Azure 檔案共用備份的資料目前不會顯示在備份報表中。
- 針對 DPM 工作負載，支援 DPM 版本5.1.363.0 和更新版本的備份報告，以及代理程式版本2.0.9127.0 和更新版本。
- 針對 MABS 工作負載，支援 MABS 版13.0.415.0 和更新版本的備份報告，以及代理程式版本2.0.9170.0 和更新版本。
- 只要將資料傳送到使用者有權存取的 Log Analytics 工作區，您就可以跨所有備份專案、保存庫、訂用帳戶和區域查看備份報表。 若要查看一組保存庫的報表，您只需要擁有保存庫用來傳送其資料的 Log Analytics 工作區的讀取器存取權。 您不需要擁有個別保存庫的存取權。
- 如果您是具有客戶訂用帳戶之委派存取權的[Azure 燈塔](https://docs.microsoft.com/azure/lighthouse/)使用者，您可以使用這些報表搭配 Azure 燈塔來跨所有租使用者查看報表。
- 記錄備份作業的資料目前不會顯示在報表中。

## <a name="get-started"></a>開始使用

請遵循下列步驟來開始使用報表。

### <a name="1-create-a-log-analytics-workspace-or-use-an-existing-one"></a>1. 建立 Log Analytics 工作區，或使用現有的

設定一或多個 Log Analytics 工作區，以儲存您的備份報告資料。 可以建立此 Log Analytics 工作區的位置和訂用帳戶，與保存庫所在的位置和訂用帳戶無關。

若要設定 Log Analytics 工作區，請參閱[Azure 入口網站中的建立 Log analytics 工作區](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)。

根據預設，Log Analytics 工作區中的資料會保留30天。 若要查看較長時間範圍的資料，請變更 Log Analytics 工作區的保留期。 若要變更保留期限，請參閱[使用 Azure 監視器記錄來管理使用量和成本](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage)。

### <a name="2-configure-diagnostics-settings-for-your-vaults"></a>2. 設定保存庫的診斷設定

Azure Resource Manager 的資源，例如復原服務保存庫，會將排程作業和使用者觸發作業的相關資訊記錄為診斷資料。

在復原服務保存庫的 [監視] 區段中，選取 [**診斷設定**]，並指定復原服務保存庫診斷資料的目標。 若要深入瞭解如何使用診斷事件，請參閱[使用復原服務保存庫的診斷設定](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events)。

![診斷設定窗格](./media/backup-azure-configure-backup-reports/resource-specific-blade.png)

Azure 備份也提供內建的 Azure 原則定義，可將指定範圍內所有保存庫的診斷設定自動化。 若要瞭解如何使用此原則，請參閱[大規模設定保存庫診斷設定](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics)。

> [!NOTE]
> 設定診斷之後，最多可能需要24小時的時間，才能完成初始資料推送。 資料開始流入 Log Analytics 工作區之後，您可能不會立即看到報表中的資料，因為目前部分日期的資料未顯示在報表中。 如需詳細資訊，請參閱[備份報告中使用的慣例](https://docs.microsoft.com/azure/backup/configure-reports#conventions-used-in-backup-reports)。 我們建議您在設定保存庫後兩天開始查看報告，以將資料傳送至 Log Analytics。

#### <a name="3-view-reports-in-the-azure-portal"></a>3. 在 Azure 入口網站中查看報表

設定保存庫以將資料傳送至 Log Analytics 之後，請前往任何保存庫的窗格，然後選取 [**備份報告**]，以查看您的備份報告。

![保存庫儀表板](./media/backup-azure-configure-backup-reports/vault-dashboard.png)

選取此連結可開啟 [備份報表] 活頁簿。

> [!NOTE]
>
> - 目前，報表的初始載入可能需要最多1分鐘的時間。
> - 復原服務保存庫只是備份報告的進入點。 從保存庫的窗格開啟 [備份報表] 活頁簿之後，請選取適當的 Log Analytics 工作區集合，以查看所有保存庫的匯總資料。

此報表包含各種索引標籤：

- **摘要**：使用此索引標籤可取得備份資產的高階總覽。 您可以快速概覽備份專案總數、已耗用的雲端儲存體總數、受保護的實例數目，以及每個工作負載類型的作業成功率。 如需特定備份成品類型的詳細資訊，請移至個別的索引標籤。

   ![[摘要] 索引標籤](./media/backup-azure-configure-backup-reports/summary.png)

- **備份專案**：使用此索引標籤可查看在備份專案層級使用之雲端儲存體的資訊和趨勢。 例如，如果您在 Azure VM 備份中使用 SQL，您可以看到每個要備份的 SQL 資料庫所耗用的雲端儲存體。 您也可以選擇查看特定保護狀態的備份專案資料。 例如，選取索引標籤頂端的 [**保護已停止**] 磚會篩選底下的所有 widget，只針對處於 [保護已停止] 狀態的備份專案顯示資料。

   ![[備份專案] 索引標籤](./media/backup-azure-configure-backup-reports/backup-items.png)

- **使用**方式：使用此索引標籤可查看備份的金鑰計費參數。 此索引標籤上顯示的資訊位於計費實體（受保護的容器）層級。 例如，在將 DPM 服務器備份至 Azure 的情況下，您可以查看受保護實例的趨勢，以及針對 DPM 服務器所使用的雲端存放裝置。 同樣地，如果您在 Azure 備份的 Azure 備份或 SAP Hana 中使用 SQL，此索引標籤會在包含這些資料庫的虛擬機器層級提供使用相關資訊。

   ![[使用量] 索引標籤](./media/backup-azure-configure-backup-reports/usage.png)

- **作業**：使用此索引標籤可查看作業的長時間執行趨勢，例如每天失敗的工作數，以及作業失敗的最大原因。 您可以同時在匯總層級和備份專案層級查看這項資訊。 在方格中選取特定的備份專案，以查看在所選時間範圍內，針對該備份專案所觸發之每個工作的詳細資訊。

   ![工作索引標籤](./media/backup-azure-configure-backup-reports/jobs.png)

- **原則**：使用此索引標籤來查看所有作用中原則的資訊，例如相關專案的數目，以及在給定原則下備份的專案所耗用的雲端儲存體總計。 選取特定的原則，以查看每個相關聯備份專案的資訊。

   ![[原則] 索引標籤](./media/backup-azure-configure-backup-reports/policies.png)

## <a name="export-to-excel"></a>匯出至 Excel

選取任何 widget （如資料表或圖表）右上方的向下箭號按鈕，將該 widget 的內容匯出為 Excel 工作表，並套用現有的篩選。 若要將資料表的更多資料列匯出至 Excel，您可以使用每個方格頂端的 [**每頁**的資料列] 下拉式箭號來增加頁面上顯示的資料列數目。

## <a name="pin-to-dashboard"></a>釘選到儀表板

選取每個 widget 頂端的 [釘選] 按鈕，將 widget 釘選到您的 Azure 入口網站儀表板。 這項功能可協助您建立量身打造的自訂儀表板，以顯示您所需的最重要資訊。

## <a name="cross-tenant-reports"></a>跨租使用者報告

如果您使用[Azure 燈塔](https://docs.microsoft.com/azure/lighthouse/)搭配委派存取多個租使用者環境中的訂用帳戶，您可以使用預設的訂用帳戶篩選器。 選取 Azure 入口網站右上角的 [篩選] 按鈕，選擇您要查看其資料的所有訂用帳戶。 這麼做可讓您在整個租使用者中選取 Log Analytics 工作區，以查看 multitenanted 報告。

## <a name="conventions-used-in-backup-reports"></a>備份報表中使用的慣例

- 在每個索引標籤上，篩選從左至右和上到下的工作。也就是說，任何篩選準則僅適用于位於該篩選器右邊或篩選準則底下的所有小工具。
- 選取彩色磚會篩選磚底下的 widget，尋找與該磚的值相關的記錄。 例如，選取 [**備份專案**] 索引標籤上的 [**保護已停止**] 磚會篩選下方的方格和圖表，以顯示處於 [保護已停止] 狀態的備份專案資料。
- 未著色的磚無法按。
- 報表中不會顯示目前部分日期的資料。 因此，當所選的**時間範圍**值為 [**過去7天**] 時，報表會顯示過去七天的記錄。 不包含目前的日期。
- 此報表會顯示在所選時間範圍內*觸發*之作業的詳細資料（除了記錄工作外）。
- 針對**雲端儲存體**和**受保護的實例**所顯示的值會在所選時間範圍的*結尾*。
- 報表中顯示的備份專案就是存在於所選時間範圍*結尾*的專案。 不會顯示在所選時間範圍中間刪除的備份專案。 相同的慣例也適用于備份原則。

## <a name="query-load-times"></a>查詢載入時間

備份報告中的 widget 是由在使用者的 Log Analytics 工作區上執行的 Kusto 查詢所提供技術支援。 這些查詢通常牽涉到大量資料的處理，並具有多個聯結，以提供更豐富的深入解析。 因此，當使用者跨大型備份資產流覽報表時，小工具可能不會立即載入。 下表根據備份專案的數目和報表所查看的時間範圍，提供不同 widget 載入時間的粗略估計值。

| **資料來源數目**                         | **時間範圍** | **大約載入時間**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ~ 5 K                       | 1 個月          | 磚：5-10 秒 <br> 格線：5-10 秒 <br> 圖表：5-10 秒 <br> 報表層級篩選：5-10 秒|
| ~ 5 K                       | 3 個月          | 磚：5-10 秒 <br> 格線：5-10 秒 <br> 圖表：5-10 秒 <br> 報表層級篩選：5-10 秒|
| ~ 10 K                       | 3 個月          | 磚：15-20 秒 <br> 格線：15-20 秒 <br> 圖表：1-2 分鐘 <br> 報表層級篩選：25-30 秒|
| ~ 15 K                       | 1 個月          | 磚：15-20 秒 <br> 格線：15-20 秒 <br> 圖表：50-60 秒 <br> 報表層級篩選：20-25 秒|
| ~ 15 K                       | 3 個月          | 磚：20-30 秒 <br> 格線：20-30 秒 <br> 圖表：2-3 分鐘 <br> 報表層級篩選：50-60 秒 |

## <a name="what-happened-to-the-power-bi-reports"></a> Power BI 報表發生什麼情況？

- 先前用來報告的 Power BI 範本應用程式（來自 Azure 儲存體帳戶的資料）位於取代路徑。 我們建議您開始將保存庫診斷資料傳送至 Log Analytics 以查看報告。

- * 此外，將診斷資料傳送至儲存體帳戶或 LA 工作區的[V1 架構](https://docs.microsoft.com/azure/backup/backup-azure-diagnostics-mode-data-model#v1-schema-vs-v2-schema)也會在取代路徑上。 這表示，如果您根據 V1 架構撰寫了任何自訂查詢或自動化，建議您更新這些查詢，以使用目前支援的 V2 架構。

## <a name="next-steps"></a>後續步驟

[深入瞭解如何使用 Azure 備份進行監視和報告](https://docs.microsoft.com/azure/backup/backup-azure-monitor-alert-faq)
