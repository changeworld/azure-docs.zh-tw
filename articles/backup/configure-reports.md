---
title: 設定 Azure 備份報告
description: 使用 Log Analytics 和 Azure 活頁簿來設定及查看 Azure 備份的報表
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: cefe81e53e89b8d7903469e836f3c5d2665febea
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77582699"
---
# <a name="configure-azure-backup-reports"></a>設定 Azure 備份報告

備份管理員的常見需求是根據長時間內的資料，取得備份的深入解析。 針對這類解決方案，可能會有多個使用案例：配置和預測已耗用的雲端儲存體、審核備份和還原，以及識別不同資料細微性層級的重要趨勢。

現今，Azure 備份提供的報告解決方案會利用[Azure 監視器記錄](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)和[Azure 活頁簿](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)，協助您在整個備份資產上取得有關備份的豐富見解。 本文說明如何設定及查看備份報表。

## <a name="supported-scenarios"></a>支援的案例

* 備份報表支援 azure Vm、azure Vm 中的 SQL、Azure Vm 中的 SAP Hana/ASE、Azure 備份代理程式（MARS）、Azure 備份伺服器（MABS）和 System Center DPM。
* 針對 DPM 工作負載，DPM 版本5.1.363.0 和更新版本支援備份報告，以及代理程式版本2.0.9127.0 和更新版本。
* 針對 MABS 工作負載，支援 MABS 版本的13.0.415.0 和更新版本，以及代理程式版本2.0.9170.0 和更新版本。
* 只要將資料傳送到使用者有權存取的 Log Analytics （LA）工作區，您就可以跨所有備份專案、保存庫、訂用帳戶和區域查看備份報表。 
* 如果您是具有客戶訂用帳戶之委派存取權的[Azure 燈塔](https://docs.microsoft.com/azure/lighthouse/)使用者，您可以使用這些報表搭配 Azure 燈塔來跨所有租使用者查看報表。
* 記錄備份作業的資料目前不會顯示在報表中。

## <a name="getting-started"></a>開始使用

若要開始使用報表，請遵循下列三個步驟，如下所述：

1. **建立 Log Analytics （LA）工作區（或使用現有的）：**

您必須設定一或多個 LA 工作區，以儲存您的備份報告資料。 可以建立此 LA 工作區的位置和訂用帳戶，與保存庫所在的位置和訂用帳戶無關。 

請參閱下列文章：[在 Azure 入口網站中建立 Log Analytics 工作區](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)，以設定 LA 工作區。

根據預設，LA 工作區中的資料會保留30天。 若要查看較長時間範圍的資料，請變更 LA 工作區的保留期。 若要變更保留期限，請參閱下列文章：[使用 Azure 監視器記錄來管理使用量和成本](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage)。

2. **設定保存庫的診斷設定：**

Azure Resource Manager 的資源，例如復原服務保存庫，會將排程作業和使用者觸發作業的相關資訊記錄為診斷資料。 

在復原服務保存庫的 [監視] 區段中，選取 [**診斷設定**]，並指定復原服務保存庫之診斷資料的目標。 [深入瞭解如何使用診斷事件](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events)。

![診斷設定分頁](./media/backup-azure-configure-backup-reports/resource-specific-blade.png)

Azure 備份也提供內建的 Azure 原則，可將指定範圍內所有保存庫的診斷設定自動化。 請參閱下列文章，以瞭解如何使用此原則：[設定大規模的保存庫診斷設定](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics)

3. **在 Azure 入口網站上查看報告：**

設定保存庫以將資料傳送至 LA 之後，請流覽至任何保存庫的分頁，然後按一下 [**備份報告**] 功能表項目，以查看您的備份報告。 

![保存庫儀表板](./media/backup-azure-configure-backup-reports/vault-dashboard.png)

按一下此連結會開啟 [備份報表] 活頁簿。

> [!NOTE]
> 目前，報表的初始載入可能需要最多1分鐘的時間。

以下是報表包含之各種索引標籤的描述：

* **摘要**-[摘要] 索引標籤提供備份資產的高階總覽。 在 [摘要] 索引標籤底下，您可以快速概覽備份專案總數、已耗用的雲端儲存體總數、受保護實例的數目，以及每個工作負載類型的作業成功率。 如需特定備份成品類型的詳細資訊，請流覽至個別的索引標籤。

![[摘要] 索引標籤](./media/backup-azure-configure-backup-reports/summary.png)

* **備份專案**-[備份專案] 索引標籤可讓您查看備份專案層級所使用之雲端儲存體的資訊和趨勢。 例如，如果您在 Azure VM 備份中使用 SQL，您可以看到每個備份的 SQL 資料庫所耗用的雲端儲存體。 您也可以選擇查看特定保護狀態的備份專案資料。 例如，按一下索引標籤頂端的 [**保護已停止**] 磚，會篩選下列所有 widget，只針對 [保護已停止] 狀態中的備份專案顯示資料。

![[備份專案] 索引標籤](./media/backup-azure-configure-backup-reports/backup-items.png)

* **使用**方式-[使用量] 索引標籤可協助您查看備份的金鑰計費參數。 此索引標籤中顯示的資訊位於計費實體（受保護的容器）層級。 例如，在將 DPM 服務器備份至 Azure 的情況下，您可以查看受保護實例的趨勢，以及針對 DPM 服務器所使用的雲端存放裝置。 同樣地，如果您在 Azure 備份的 Azure 備份或 SAP Hana 中使用 SQL，此索引標籤會提供這些資料庫所包含之虛擬機器層級的使用量相關資訊。

![[使用量] 索引標籤](./media/backup-azure-configure-backup-reports/usage.png)

* **作業**-[工作] 索引標籤可讓您查看作業的長時間執行趨勢，例如每天失敗的工作數，以及作業失敗的最大原因。 您可以在匯總層級和備份專案層級查看這項資訊。 按一下方格中的特定備份專案，可讓您查看在所選時間範圍內，針對該備份專案所觸發之每個工作的詳細資訊。

![工作索引標籤](./media/backup-azure-configure-backup-reports/jobs.png)

* **原則**-[原則] 索引標籤可讓您查看所有作用中原則的資訊，例如相關聯專案的數目，以及在給定原則下備份的專案所耗用的雲端儲存體總計。 按一下特定原則可讓您查看每個相關聯備份專案的資訊。

![[原則] 索引標籤](./media/backup-azure-configure-backup-reports/policies.png)

## <a name="exporting-to-excel"></a>匯出至 Excel

按一下任何 widget （資料表/圖表）右上方的向下箭號按鈕，就會將該 widget 的內容匯出為 Excel 工作表，如同已套用的現有篩選。 若要將資料表的更多資料列匯出至 Excel，您可以使用每個方格頂端的 [**每頁**的資料列] 下拉式清單，來增加頁面上顯示的資料列數目。

## <a name="pinning-to-dashboard"></a>釘選到儀表板

按一下每個 widget 頂端的釘選圖示，將 widget 釘選到您的 Azure 入口網站儀表板。 這可協助您建立量身打造的自訂儀表板，以顯示您所需的最重要資訊。

## <a name="cross-tenant-reports"></a>跨租使用者報告

如果您是具有跨多個租使用者環境之訂用帳戶的委派存取權的[Azure 燈塔](https://docs.microsoft.com/azure/lighthouse/)使用者，您可以使用預設的訂用帳戶篩選（按一下 Azure 入口網站右上方的篩選圖示），以選擇您想要查看其資料的所有訂閱。 這麼做可讓您選取所有租使用者的 LA 工作區，以查看多租使用者報告。

## <a name="conventions-used-in-backup-reports"></a>備份報表中使用的慣例

* 篩選在每個索引標籤上由左至右和上而下的工作，亦即，任何篩選準則僅適用于位於該篩選器右邊或篩選準則底下的所有小工具。 
* 按一下彩色磚會篩選磚底下的 widget，尋找與該磚的值相關的記錄。 例如，按一下 [備份專案] 索引標籤中的 [*保護已停止*] 磚會篩選下方的方格和圖表，以顯示「保護已停止」狀態中的備份專案資料。
* 未著色的磚無法按。
* 報表中不會顯示目前部分日期的資料。 因此，當所選的**時間範圍**值為，***最後7天***，此報表會顯示過去7個完成天數的記錄（不包含當天的日期）。
* 此報表會顯示在所選時間範圍內**觸發**之作業的詳細資料（除了記錄工作外）。 
* 針對雲端儲存體和受保護的實例所顯示的值會在所選時間範圍的**結尾**。
* 報表中顯示的備份專案就是存在於所選時間範圍**結尾**的專案。 在選取的時間範圍中間刪除的備份專案不會顯示。 相同的慣例也適用于備份原則。

## <a name="query-load-times"></a>查詢載入時間

備份報表中的 widget 是由在使用者的 LA 工作區上執行的 Kusto 查詢所提供技術支援。 因為這些查詢通常牽涉到大量資料的處理，而且有多個聯結可啟用更豐富的深入解析，所以當使用者跨大型備份資產查看報表時，小工具可能不會立即載入。 下表根據備份專案的數目以及要查看報表的時間範圍，提供不同 widget 載入時間的粗略估計值：。

| **資料來源數目**                         | **時間範圍** | **載入時間（大約）**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ~ 5 K                       | 1 個月          | 磚：5-10 秒 <br> 格線：5-10 秒 <br> 圖表：5-10 秒 <br> 報表層級篩選：5-10 秒|
| ~ 5 K                       | 3 個月          | 磚：5-10 秒 <br> 格線：5-10 秒 <br> 圖表：5-10 秒 <br> 報表層級篩選：5-10 秒|
| ~ 10 K                       | 3 個月          | 磚：15-20 秒 <br> 格線：15-20 秒 <br> 圖表：1-2 分鐘 <br> 報表層級篩選：25-30 秒|
| ~ 15 K                       | 1 個月          | 磚：15-20 秒 <br> 格線：15-20 秒 <br> 圖表：50-60 秒 <br> 報表層級篩選：20-25 秒|
| ~ 15 K                       | 3 個月          | 磚：20-30 秒 <br> 格線：20-30 秒 <br> 圖表：2-3 分鐘 <br> 報表層級篩選：50-60 秒 |

## <a name="what-happened-to-the-power-bi-reports"></a>Power BI 報表發生了什麼事？
* 我們 Power BI 先前用來報告的範本應用程式（來自 Azure 儲存體帳戶的資料）位於取代路徑。 建議您開始將保存庫診斷資料傳送至 Log Analytics （如上所述），以查看報表。

* 此外，將診斷資料傳送至儲存體帳戶或 LA 工作區的 V1 架構也會在取代路徑上。 這表示，如果您根據 V1 架構撰寫了任何自訂查詢或自動化，建議您更新這些查詢，以使用目前支援的 V2 架構。

## <a name="next-steps"></a>後續步驟
[深入瞭解如何使用 Azure 備份進行監視和報告](https://docs.microsoft.com/azure/backup/backup-azure-monitor-alert-faq)