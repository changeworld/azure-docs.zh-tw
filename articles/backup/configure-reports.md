---
title: 設定 Azure 備份報告
description: 使用 Log Analytics 和 Azure 活頁簿來設定及檢視 Azure 備份的報告
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 5d1c7d628a61e550aa9dc4a5265ae16c5ed5336a
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86513620"
---
# <a name="configure-azure-backup-reports"></a>設定 Azure 備份報告

備份管理員的常見需求是根據長期資料來取得備份的見解。 這類解決方案的使用案例包括：

- 配置和預測已取用的雲端儲存體。
- 稽核備份和還原。
- 找出不同細微性層級的主要趨勢。

現今，Azure 備份提供的報告解決方案會使用 [Azure 監視器記錄](../azure-monitor/log-query/get-started-portal.md)和 [Azure 活頁簿](../azure-monitor/platform/workbooks-overview.md)。 這些資源可協助您取得有關整個備份資產中備份的豐富見解。 本文說明如何設定及檢視 Azure 備份報告。

## <a name="supported-scenarios"></a>支援的案例

- Azure VM、Azure VM 中的 SQL、Azure VM 中的 SAP HANA、Microsoft Azure 復原服務 (MARS) 代理程式、Microsoft Azure 備份伺服器 (MABS) 和 System Center Data Protection Manager (DPM) 都支援備份報告。 針對 Azure 檔案共用備份，在2020年6月1日之後建立的所有記錄都會顯示資料。
- 對於 DPM 工作負載，DPM 版本 5.1.363.0 和更新版本及代理程式版本 2.0.9127.0 和更新版本都支援備份報告。
- 對於 MABS 工作負載，MABS 版本 13.0.415.0 和更新版本及代理程式版本 2.0.9170.0 和更新版本都支援備份報告。
- 只要將資料傳送到使用者有權存取的 Log Analytics 工作區，即可跨所有備份項目、保存庫、訂用帳戶和區域檢視備份報告。 若要檢視一組保存庫的報告，您只需要對保存庫資料傳送至的 Log Analytics 工作區擁有讀者存取權即可。 您不需要擁有個別保存庫的存取權。
- 如果您是 [Azure Lighthouse](../lighthouse/index.yml) 使用者並擁有客戶訂用帳戶的委派存取權，即可使用這些報告搭配 Azure Lighthouse 來檢視所有租用戶的報告。
- 目前，可以在備份報告中檢視最多 100 個 Log Analytics 工作區 (跨租用戶) 的資料。
- 記錄備份作業的資料目前不會顯示在報告中。

## <a name="get-started"></a>開始使用

請遵循下列步驟來開始使用報告。

### <a name="1-create-a-log-analytics-workspace-or-use-an-existing-one"></a>1.建立 Log Analytics 工作區或使用現有 Log Analytics 工作區

設定一或多個 Log Analytics 工作區，以儲存您的備份報告資料。 可以建立此 Log Analytics 工作區的位置和訂用帳戶，與保存庫所在的位置和訂用帳戶無關。

若要設定 Log Analytics 工作區，請參閱[在 Azure 入口網站中建立 Log Analytics 工作區](../azure-monitor/learn/quick-create-workspace.md)。

根據預設，Log Analytics 工作區中的資料會保留 30 天。 若要查看較長時間範圍的資料，請變更 Log Analytics 工作區的保留期間。 若要變更保留期間，請參閱[使用 Azure 監視器記錄管理使用量和成本](../azure-monitor/platform/manage-cost-storage.md)。

### <a name="2-configure-diagnostics-settings-for-your-vaults"></a>2.設定保存庫的診斷設定

Azure Resource Manager 資源 (例如復原服務保存庫) 會將排程作業和使用者觸發作業的相關資訊記錄為診斷資料。

在復原服務保存庫的 [監視] 區段中，選取 [診斷設定] 並指定復原服務保存庫診斷資料的目標。 若要深入了解如何使用診斷事件，請參閱[使用復原服務保存庫的診斷設定](./backup-azure-diagnostic-events.md)。

![診斷設定窗格](./media/backup-azure-configure-backup-reports/resource-specific-blade.png)

Azure 備份也提供內建的 Azure 原則定義，可自動設定指定範圍內所有保存庫的診斷。 若要了解如何使用此原則，請參閱[設定大規模的保存庫診斷設定](./azure-policy-configure-diagnostics.md)。

> [!NOTE]
> 設定診斷之後，最多可能需要 24 小時的時間，才能完成初始資料推送。 資料開始流入 Log Analytics 工作區之後，您可能不會立即在報告中看到此資料，因為目前未滿一天的資料不會顯示在報告中。 如需詳細資訊，請參閱[備份報告中使用的慣例](#conventions-used-in-backup-reports)。 我們建議您在將保存庫設定為將資料傳送至 Log Analytics 後兩天開始檢視報告。

#### <a name="3-view-reports-in-the-azure-portal"></a>3.在 Azure 入口網站中檢視報告

將保存庫設定為將資料傳送至 Log Analytics 之後，請前往任何保存庫的窗格，然後選取 [報份報告]，以檢視您的備份報告。

![保存庫儀表板](./media/backup-azure-configure-backup-reports/vault-dashboard.png)

選取此連結可開啟 [備份報告] 活頁簿。

> [!NOTE]
>
> - 目前，報告的初始載入最多可能需要 1 分鐘的時間。
> - 復原服務保存庫只是備份報告的進入點。 從保存庫的窗格開啟 [備份報告] 活頁簿之後，請選取適當的 Log Analytics 工作區集合，以查看所有保存庫的彙總資料。

此報告包含各種索引標籤：

- **摘要**：使用此索引標籤可取得備份資產的整體概觀。 您可以快速概覽備份項目總數、已取用的雲端儲存體總量、受保護的執行個體數目，以及每個工作負載類型的作業成功率。 如需特定備份成品類型的詳細資訊，請移至個別的索引標籤。

   ![摘要索引標籤](./media/backup-azure-configure-backup-reports/summary.png)

- **備份項目**：使用此索引標籤可查看在備份項目層級取用的雲端儲存體資訊和趨勢。 例如，如果您在 Azure VM 備份中使用 SQL，即可查看每個要備份的 SQL 資料庫所取用的雲端儲存體。 您也可選擇查看特定保護狀態的備份項目資料。 例如，選取索引標籤頂端的 [已停止保護] 圖格，可篩選下面所有的 Widget，只針對處於 [已停止保護] 狀態的備份項目顯示資料。

   ![備份項目索引標籤](./media/backup-azure-configure-backup-reports/backup-items.png)

- **使用量**：使用此索引標籤可檢視備份的主要計費參數。 此索引標籤上顯示的資訊屬於計費實體 (受保護的容器) 層級。 例如，在將 DPM 伺服器備份至 Azure 的情況下，您可檢視受保護的執行個體趨勢，以及 DPM 伺服器所取用的雲端儲存體。 同樣地，如果您在 Azure 備份中使用 SQL 或在 Azure 備份中使用 SAP HANA，此索引標籤會在內含這些資料庫的虛擬機器層級提供使用量相關資訊。

   ![使用量索引標籤](./media/backup-azure-configure-backup-reports/usage.png)

> [!NOTE]
> 對於 DPM 工作負載，相較於復原服務保存庫概觀索引標籤中所示的彙總使用量值，使用者可能會看到報告中顯示的使用量值之間有些許差異 (每部 DPM 伺服器 20 MB 的順序)。此差異的原因在於，每部要註冊備份的 DPM 伺服器都有相關聯的「中繼資料」資料來源，這不會呈現為可供報告的成品。

- **作業**：使用此索引標籤可檢視作業的長期趨勢，例如每天失敗的作業數，以及作業失敗的首要原因。 您可以同時在彙總層級和備份項目層級檢視此資訊。 在方格中選取特定備份專案，以檢視在所選時間範圍內，針對該備份項目所觸發的每項作業詳細資訊。

   ![作業索引標籤](./media/backup-azure-configure-backup-reports/jobs.png)

- **原則**：使用此索引標籤可檢視所有作用中原則的相關資訊，例如相關聯的項目數，以及根據指定原則備份的項目所取用的雲端儲存體總量。 選取特定原則，以檢視每個相關聯備份項目的資訊。

   ![原則索引標籤](./media/backup-azure-configure-backup-reports/policies.png)

## <a name="export-to-excel"></a>匯出至 Excel

選取任何 Widget (如資料表或圖表) 右上方的向下箭號按鈕，將該 Widget 的內容按現狀匯出為 Excel 工作表並套用現有的篩選條件。 若要將資料表的更多資料列匯出至 Excel，您可以使用每個方格頂端的 [每頁資料列數] 下拉箭號，增加頁面上顯示的資料列數。

## <a name="pin-to-dashboard"></a>釘選到儀表板

選取每個 Widget 頂端的釘選按鈕，將 Widget 釘選到您的 Azure 入口網站儀表板。 這項功能可協助您建立量身打造的自訂儀表板，以顯示您所需的最重要資訊。

## <a name="cross-tenant-reports"></a>跨租用戶報告

如果您使用 [Azure Lighthouse](../lighthouse/index.yml) 搭配多個租用戶環境中訂用帳戶的委派存取權，即可使用預設的訂用帳戶篩選器。 選取 Azure 入口網站右上角的篩選按鈕，以選擇您要查看其資料的所有訂用帳戶。 這麼做可讓您選取跨租用戶的 Log Analytics 工作區，以查看多租用戶報告。

## <a name="conventions-used-in-backup-reports"></a>備份報告中使用的慣例

- 每個索引標籤的篩選條件會從左至右、從上到下運作。也就是說，任何篩選條件都只會套用至位於該篩選器右邊或下方的所有 Widget。
- 選取彩色圖格可篩選圖格下方的 Widget，找出與該圖格值相關的記錄。 例如，選取 [備份項目] 索引標籤上的 [已停止保護] 圖格，可篩選下面的方格和圖表，以針對處於 [已停止保護] 狀態的備份項目顯示資料。
- 無法點選不是彩色的圖格。
- 報告中不會顯示目前未滿一天的資料。 因此，當 [時間範圍] 的選取值為 [過去 7 天] 時，報告會顯示過去七個整天的記錄。 不包含當天。
- 報告會顯示在所選時間範圍內，「已觸發」的作業詳細資料 (除了記錄作業以外)。
- 針對 [雲端儲存體] 和 [受保護的執行個體] 所顯示的值，位於所選時間範圍的「結尾」。
- 報告中顯示的備份項目就是所選時間範圍「結束」時存在的項目。 不會顯示在所選時間範圍中間刪除的備份項目。 相同的慣例也適用於備份原則。

## <a name="query-load-times"></a>查詢載入時間

備份報告中的 Widget 是由在使用者的 Log Analytics 工作區上執行的 Kusto 查詢提供技術支援。 這些查詢通常牽涉到大量資料的處理，並有多個聯結可提供更豐富的見解。 因此，當使用者檢視大型備份資產的報告時，可能不會立即載入 Widget。 下表根據備份項目數量和報告的檢視時間範圍，提供不同 Widget 載入所需的粗略估計時間。

| **資料來源量**                         | **時間範圍** | **大約載入次數**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ~5 K                       | 1 個月          | 圖格：5-10 秒 <br> 方格：5-10 秒 <br> 圖表：5-10 秒 <br> 報告層級篩選條件：5-10 秒|
| ~5 K                       | 3 個月          | 圖格：5-10 秒 <br> 方格：5-10 秒 <br> 圖表：5-10 秒 <br> 報告層級篩選條件：5-10 秒|
| ~10 K                       | 3 個月          | 圖格：15-20 秒 <br> 方格：15-20 秒 <br> 圖表：1-2 分鐘 <br> 報告層級篩選條件：25-30 秒|
| ~15 K                       | 1 個月          | 圖格：15-20 秒 <br> 方格：15-20 秒 <br> 圖表：50-60 秒 <br> 報告層級篩選條件：20-25 秒|
| ~15 K                       | 3 個月          | 圖格：20-30 秒 <br> 方格：20-30 秒 <br> 圖表：2-3 分鐘 <br> 報告層級篩選條件：50-60 秒 |

## <a name="what-happened-to-the-power-bi-reports"></a>Power BI 報告發生什麼情況？

- 先前用於報告的 Power BI 範本應用程式 (其資料來自 Azure 儲存體帳戶) 位於淘汰路徑上。 我們建議您開始將保存庫診斷資料傳送至 Log Analytics 以檢視報告。

- 此外，將診斷資料傳送至儲存體帳戶或 LA 工作區的 [V1 結構描述](./backup-azure-diagnostics-mode-data-model.md#v1-schema-vs-v2-schema)也位於淘汰路徑上。 這表示如果您根據 V1 結構描述撰寫了任何自訂查詢或自動化，建議您更新這些查詢，以使用目前支援的 V2 結構描述。

## <a name="next-steps"></a>後續步驟

[深入了解如何使用 Azure 備份進行監視和報告](./backup-azure-monitor-alert-faq.md)
