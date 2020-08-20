---
title: 管理 Azure 復原服務保存庫與伺服器
description: 在本文中，您將瞭解如何使用復原服務保存庫總覽儀表板來監視和管理您的復原服務保存庫。
ms.topic: conceptual
ms.date: 07/08/2019
ms.openlocfilehash: ee96acf624f1c313c85b21840c142e1e2d6f40d8
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88654203"
---
# <a name="monitor-and-manage-recovery-services-vaults"></a>監視及管理復原服務保存庫

本文說明如何使用復原服務保存庫的 [概觀]**** 儀表板來監視及管理復原服務保存庫。 當您從清單開啟復原服務保存庫時，所選保存庫的 [概觀]**** 儀表板隨即開啟。 此儀表板會提供有關保存庫的各種詳細資料。 有一些 *圖* 格會顯示：重大和警告警示的狀態、進行中和失敗的備份作業，以及本機冗余儲存體 (LRS) 及異地冗余儲存體 (GRS) 使用的數量。 如果您將 Azure VM 備份到保存庫，[[備份前置檢查狀態]**** 圖格會顯示任何重大或警告項目](#backup-pre-check-status)。 下圖是 **Contoso-vault** 的 [概觀]**** 儀表板。 [備份項目]**** 圖格會顯示有九個註冊到保存庫的項目。

![復原服務保存庫儀表板](./media/backup-azure-manage-windows-server/rs-vault-blade.png)

本文的必要條件如下：Azure 訂用帳戶、復原服務保存庫，以及至少有一個針對保存庫設定的備份項目。

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="open-a-recovery-services-vault"></a>開啟復原服務保存庫

若要監視警示，或檢視關於復原服務保存庫的管理資料，請開啟保存庫。

1. 使用 Azure 訂用帳戶登入 [Azure 入口網站](https://portal.azure.com/)。

2. 在入口網站中，選取 [所有服務]。

   ![開啟復原服務保存庫清單的步驟 1](./media/backup-azure-manage-windows-server/open-rs-vault-list.png)

3. 在 [所有服務]**** 對話方塊中，輸入**復原服務**。 當您開始輸入時，清單會根據您輸入的文字進行篩選。 當 [復原 **服務保存庫** ] 選項出現時，請選取它以開啟您訂用帳戶中的復原服務保存庫清單。

    ![建立復原服務保存庫的步驟 1](./media/backup-azure-manage-windows-server/list-of-rs-vaults.png) <br/>

4. 從保存庫清單中，選取保存庫以開啟其 **總覽** 儀表板。

    ![復原服務保存庫儀表板](./media/backup-azure-manage-windows-server/rs-vault-blade.png) <br/>

    [概觀] 儀表板會使用圖格來提供警示和備份作業資料。

## <a name="monitor-backup-jobs-and-alerts"></a>監視備份作業和警示

復原服務保存庫的 [概觀]**** 儀表板會提供 [監視] 和 [使用量] 資訊的圖格。 [監視] 區段中的圖格會顯示 [重大] 和 [警告] 警示，以及 [進行中] 和 [失敗] 的作業。 選取特定的警示或作業以開啟 [備份警示] 或 [備份作業] 功能表，篩選出該作業或警示。

![備份儀表板工作](./media/backup-azure-manage-windows-server/monitor-dashboard-tiles-warning.png)

[監視] 區段會顯示預先定義的 [備份警示]**** 和 [備份作業]**** 查詢結果。 [監視] 圖格會提供有關下列各項的最新資訊：

* 備份作業的 [重大] 和 [警告] 警示 (在過去 24 小時內)
* 預先檢查 Azure Vm 的狀態。 如需有關前置檢查狀態的完整資訊，請參閱 [備份前置檢查狀態](#backup-pre-check-status)。
* 進行中的備份作業，以及失敗的作業 (在過去 24 小時內)。

[使用量] 圖格會提供：

* 針對保存庫設定的備份項目數目。
* 保存庫所使用的 Azure 儲存體 (以 LRS 和 GRS 分隔)。

選取 [備份儲存體] (以外的磚) 開啟相關聯的功能表。 上圖中的 [備份警示] 圖格會顯示三個 [重大] 警示。 在 [備份警示] 磚中選取 [重大警示] 資料列，會開啟針對重大警示篩選的備份警示。

![針對重大警示篩選的備份警示功能表](./media/backup-azure-manage-windows-server/critical-backup-alerts.png)

上圖中的 [備份警示] 功能表的篩選依據：狀態為作用中、嚴重性為重大，且時間為前 24 小時。

### <a name="backup-pre-check-status"></a>備份前置檢查狀態

備份前置檢查會檢查您 Vm 的設定是否有可能會對備份造成負面影響的問題。 它們會匯總此資訊，讓您可以直接從復原服務保存庫儀表板加以查看，並提供矯正措施的建議，以確保成功的檔案一致或應用程式一致的備份。 它們不需要任何基礎結構，也不需要額外費用。  

備份前置檢查會作為 Azure Vm 的排程備份作業的一部分執行。 它們會以下列其中一種狀態結束：

* 已**通過**：此狀態表示 VM 的設定應該會導致成功的備份，而且不需要採取任何矯正措施。
* **警告**：此狀態表示 VM 設定中 *可能* 導致備份失敗的一或多個問題。 它會提供 *建議* 的步驟，以確保備份成功。 例如，未安裝最新的 VM 代理程式，可能會間歇性地導致備份失敗。 這種情況會提供警告狀態。
* **重大**：此狀態表示 VM 設定中 *會* 導致備份失敗的一或多個重大問題，並提供 *必要* 的步驟以確保備份成功。 例如，因 VM 的 NSG 規則更新而導致的網路問題，將導致備份失敗，因為這會導致 VM 無法與 Azure 備份服務進行通訊。 這種情況會提供重大狀態。

遵循下列步驟來開始解決復原服務保存庫上 VM 備份的備份預先檢查所報告的任何問題。

* 在 [復原服務保存庫] 儀表板上，選取 [ **Azure vm]) 磚的備份前置檢查狀態 (** 。
* 選取任何備份前置檢查狀態為 [ **重大** ] 或 [ **警告**] 的 VM。 此動作會開啟 [ **VM 詳細資料** ] 窗格。
* 選取窗格頂端的窗格通知，以顯示設定問題描述和補救步驟。

## <a name="manage-backup-alerts"></a>管理備份警示

若要存取 [備份警示] 功能表，請在 [復原服務保存庫] 功能表中選取 [ **備份警示**]。

![備份警示](./media/backup-azure-manage-windows-server/backup-alerts-menu.png)

[備份警示] 報告會列出保存庫的警示。

![備份警示報表](./media/backup-azure-manage-windows-server/backup-alerts.png)

### <a name="alerts"></a>警示

[備份警示] 清單會顯示篩選後警示的所選資訊。 在 [備份警示] 功能表中，您可以針對 [重踏] 或 [警告] 警示篩選。

| 警示層級 | 產生警示的事件 |
| ----------- | ----------- |
| 重大 | 您會在下列情況中收到重大警示：備份作業失敗、復原作業失敗，以及當您在伺服器停止保護時，但保留資料。|
| 警告 | 您會在下列情況收到警告警示：備份作業完成但出現警告。 例如，當不是因為損毀問題而未備份超過100的檔案時，或當) 的1000000檔案成功備份時。 |
| 資訊 | 目前沒有使用中的資訊警示。 |

### <a name="viewing-alert-details"></a>檢閱警示詳細資料

「備份警示」報告會追蹤有關每個警示的八項詳細資料。 使用 [選擇資料行]**** 按鈕來編輯報告中的詳細資料。

![備份警示選擇資料行按鈕](./media/backup-azure-manage-windows-server/backup-alerts.png)

根據預設，所有詳細資料 ([最新發生時間]**** 除外) 都會出現在報告中。

* 警示
* 備份項目
* 受保護的伺服器
* 嚴重性
* Duration
* 建立時間
* 狀態
* 最新發生時間

### <a name="change-the-details-in-alerts-report"></a>變更警示報告中的詳細資料

1. 若要變更報表資訊，請在 [ **備份警示** ] 功能表中選取 **[選擇資料行**]。

   ![選取 [選擇資料行]](./media/backup-azure-manage-windows-server/alerts-menu-choose-columns.png)

   [選擇資料行] **** 功能表隨即開啟。

2. 在 [選擇資料行]**** 功能表中，選擇您想要在報告中顯示的詳細資料。

    ![選擇資料行功能表](./media/backup-azure-manage-windows-server/choose-columns-menu.png)

3. 選取 [ **完成** ] 以儲存變更並關閉 [選擇資料行] 功能表。

   如果您進行變更，但不想要保留變更，請選取 [ **重設** ]，將選取的設回上次儲存的設定。

### <a name="change-the-filter-in-alerts-report"></a>變更警示報告中的篩選條件

使用 [篩選]**** 功能表，變更警示的 [嚴重性]、[狀態]、[開始時間] 和 [結束時間]。

> [!NOTE]
> 編輯 [備份警示] 篩選條件，並不會變更保存庫 [概觀] 儀表板中的 [重大] 或 [警告] 警示。
>  

1. 若要變更 [備份警示] 篩選準則，請在 [備份警示] 功能表中選取 [ **篩選**]。

   ![選擇篩選功能表](./media/backup-azure-manage-windows-server/alerts-menu-choose-filter.png)

   [篩選] 功能表隨即出現。

   ![篩選警示功能表](./media/backup-azure-manage-windows-server/filter-alert-menu.png)

2. 編輯 [嚴重性]、[狀態]、[開始時間] 或 [結束時間]，然後選取 [ **完成** ] 以儲存您的變更。

## <a name="configuring-notifications-for-alerts"></a>設定警示通知

設定通知，以在發生 [警告] 或 [重大] 警示時產生電子郵件。 您可以每小時或在發生特定警示時傳送電子郵件警示。

   ![篩選警示](./media/backup-azure-manage-windows-server/configure-notification.png)

依預設會 [開啟]**** 電子郵件通知。 選取 [ **關閉** ] 以停止電子郵件通知。

如果不想要分組，或者沒有很多可能產生警示的項目，請在 [通知]**** 控制項中，選擇 [每個警示]****。 每個警示會產生一個通知 (預設設定)，而解析電子郵件會立刻送出。

如果選取 [每小時摘要]****，則會傳送一封電子郵件給使用者，說明過去一小時有未解決的警示產生。 解決方式電子郵件會在每小時結束時送出。

選擇用來產生電子郵件的警示嚴重性 ([重大] 或 [警告])。 目前沒有任何資訊警示。

## <a name="manage-backup-items"></a>管理備份項目

復原服務保存庫可保存許多類型的備份資料。 [深入瞭解](backup-overview.md#what-can-i-back-up) 您可以備份的內容。 若要管理各種伺服器、電腦、資料庫和工作負載，請選取 [ **備份專案** ] 圖格，以查看保存庫的內容。

![備份項目圖格](./media/backup-azure-manage-windows-server/backup-items.png)

備份項目清單 (依 [備份管理類型] 組織) 隨即開啟。

![備份專案清單](./media/backup-azure-manage-windows-server/list-backup-items.png)

若要探索特定類型的受保護實例，請在 [備份管理類型] 欄中選取專案。 例如，上圖中有兩部 Azure 虛擬機器在此保存庫中保護。 選取 [ **Azure 虛擬機器**]，會開啟此保存庫中受保護虛擬機器的清單。

![受保護虛擬機器的清單](./media/backup-azure-manage-windows-server/list-of-protected-virtual-machines.png)

虛擬機器清單中有很有用的資料：相關聯的資源群組、先前[備份前置檢查](#backup-pre-check-status)、上次備份狀態，以及最近還原點的日期。 最後一欄中的省略符號可開啟用來觸發一般工作的功能表。 各欄中針對每個備份類型提供的有用資料都不同。

![開啟一般工作的省略號功能表](./media/backup-azure-manage-windows-server/ellipsis-menu.png)

## <a name="manage-backup-jobs"></a>管理備份作業

保存庫儀表板中的 [備份作業]**** 圖格會顯示過去 24 小時內 [進行中] 或 [失敗] 的作業數目。 此圖格可讓您一窺 [備份作業] 功能表。

![備份作業磚](./media/backup-azure-manage-windows-server/backup-jobs-tile.png)

若要查看作業的其他詳細資料，請選取 [ **進行中** ] 或 [ **失敗** ]，以開啟針對該狀態篩選的 [備份作業] 功能表。

### <a name="backup-jobs-menu"></a>備份作業功能表

[備份作業]**** 功能表會顯示有關 [項目類型]、[作業]、[狀態]、[開始時間] 及 [持續時間] 的資訊。  

若要開啟 [備份作業] 功能表，請在保存庫的主功能表中選取 [ **備份作業**]。

![選取備份作業](./media/backup-azure-manage-windows-server/backup-jobs-menu-item.png)

備份作業清單隨即開啟。

![備份作業清單](./media/backup-azure-manage-windows-server/backup-jobs-list.png)

[備份作業] 功能表會顯示過去 24 小時內所有備份類型的所有作業狀態。 使用 [篩選]**** 來變更篩選條件。 下列各節將說明篩選條件。

若要變更篩選條件：

1. 在 [保存庫備份作業] 功能表中，選取 [ **篩選**]。

   ![選取備份作業的篩選](./media/backup-azure-manage-windows-server/vault-backup-job-menu-filter.png)

    [篩選] 功能表隨即開啟。

   ![針對備份作業開啟篩選功能表](./media/backup-azure-manage-windows-server/filter-menu-backup-jobs.png)

2. 選擇篩選設定，然後選取 [ **完成**]。 經過篩選的清單會根據新的設定重新整理。

#### <a name="item-type"></a>項目類型

項目類型是受保護執行個體的備份管理類型。 共有四種類型；請參閱下列清單。 您可以檢視所有項目類型，或只檢視一個項目類型。 您無法選取兩個或三個項目類型。 可用的項目類型如下︰

* 所有項目類型
* Azure 虛擬機器
* 檔案和資料夾
* Azure 儲存體
* Azure 工作負載

#### <a name="operation"></a>操作

您可以檢視一項作業或所有作業。 您無法選取兩項或三項作業。 可用的作業如下：

* 所有作業
* 註冊
* 設定備份
* Backup
* 還原
* 停用備份
* 刪除備份資料

#### <a name="status"></a>狀態

您可以檢視 [所有狀態] 或一個狀態。 您無法選取兩個或三個狀態。 可用的狀態如下：

* 所有狀態
* Completed
* 進行中
* 失敗
* 已取消
* 已完成，但出現警告

#### <a name="start-time"></a>開始時間

查詢開始的日期和時間。 預設值為 24 小時的期間。

#### <a name="end-time"></a>結束時間

查詢結束的日期和時間。

### <a name="export-jobs"></a>匯出作業

使用 [匯出工作]**** 來建立包含所有作業功能表資訊的試算表。 此試算表有一個可保存所有作業摘要的工作表，以及各項作業的個別作表。

若要將工作資訊匯出到試算表，請選取 [ **匯出工作**]。 此服務會使用保存庫名稱和日期建立試算表，但您可以變更名稱。

## <a name="monitor-backup-usage"></a>監視備份使用量

儀表板中的 [備份儲存體] 圖格會顯示 Azure 中耗用的儲存體。 提供下列各項的儲存體使用量︰

* 與保存庫相關聯的雲端 LRS 儲存體使用量
* 與保存庫相關聯的雲端 GRS 儲存體使用量

## <a name="troubleshooting-monitoring-issues"></a>針對監視問題進行疑難排解

**問題：** Azure 備份代理程式中的作業和/或警示不會出現在入口網站中。

**疑難排解步驟︰**```OBRecoveryServicesManagementAgent``` 程序會將作業和警示資料傳送至 Azure 備份服務。 此程序可能偶爾會卡住或關閉。

1. 若要確認此程序不在執行中，請開啟 [工作管理員]****，然後檢查 ```OBRecoveryServicesManagementAgent``` 是否正在執行中。

2. 如果此程序不在執行中，請開啟 [控制台]****，然後瀏覽服務清單。 啟動或重新啟動 **Microsoft Azure 復原服務管理代理程式**。

    如需進一步資訊，請瀏覽以下位置的記錄：<br/>
   例如 `<AzureBackup_agent_install_folder>\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider*`：<br/>
   `C:\Program Files\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider0.errlog`

## <a name="next-steps"></a>後續步驟

* [從 Azure 還原 Windows Server 或 Windows 用戶端](backup-azure-restore-windows-server.md)
* 若要深入了解 Azure 備份，請參閱 [Azure 備份概觀](./backup-overview.md)
