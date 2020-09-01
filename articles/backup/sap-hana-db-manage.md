---
title: 在 Azure VM 上管理備份的 SAP HANA 資料庫
description: 在本文中，可了解在 Azure 虛擬機器上執行的 SAP HANA 資料庫的一般管理和監視工作。
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: 40761415042cc619893ab3a712a763d4fb046e38
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267474"
---
# <a name="manage-and-monitor-backed-up-sap-hana-databases"></a>管理和監視已備份的 SAP Hana 資料庫

本文會介紹 SAP HANA 資料庫的常見管理和監視工作，包括在 Azure 虛擬機器 (VM) 上執行的資料庫，以及透過 [Azure 備份](./backup-overview.md)服務備份至 Azure 備份復原服務保存庫的資料庫。 您會瞭解到如何監視作業和警示、觸發隨選備份、編輯原則、停止和繼續資料庫保護，以及從備份取消註冊 VM。

如果您尚未為 SAP HANA 資料庫設定備份，請參閱[在 Azure Vm 上備份 SAP HANA 資料庫](./backup-azure-sap-hana-database.md)。

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>在入口網站中監視手動備份作業

Azure 備份會在 Azure 入口網站的 [備份作業] 區段中顯示所有手動觸發的作業。

![備份作業區段](./media/sap-hana-db-manage/backup-jobs.png)

您會在此入口網站中看到的工作，包括資料庫探索和註冊，以及備份和還原作業。 這一節不會顯示已排程的工作，包括記錄備份。 從 SAP HANA 的原生用戶端 (Studio/駕駛中心/DBA 環境) 手動觸發的備份，也不會在這裡顯示。

![備份作業清單](./media/sap-hana-db-manage/backup-jobs-list.png)

若要深入瞭解監視，請參閱 [Azure 入口網站中的監視](./backup-azure-monitoring-built-in-monitor.md)和[使用 Azure 監視器進行監視](./backup-azure-monitoring-use-azuremonitor.md)。

## <a name="view-backup-alerts"></a>檢視備份警示

警示是監視 SAP HANA 資料庫備份的一種簡單方法。 警示可協助您專注於最關注的事件，而不會因為備份產生的許多事件而混淆。 Azure 備份可讓您設定警示，並可加以監視，如下所示：

* 登入 [Azure 入口網站](https://portal.azure.com/)。
* 在保存庫儀表板上，選取 [備份警示]。

  ![保存庫儀表板上的備份警示](./media/sap-hana-db-manage/backup-alerts-dashboard.png)

* 您將能夠看到警示：

  ![備份警示清單](./media/sap-hana-db-manage/backup-alerts-list.png)

* 按一下警示以檢視更多詳細資料：

  ![警示詳細資料](./media/sap-hana-db-manage/alert-details.png)

現在 Azure 備份可讓您透過電子郵件傳送警示。 這些警示包括：

* 所有備份失敗都會觸發的警示。
* 在資料庫層級依錯誤碼合併的警示。
* 在資料庫第一次備份失敗時才會傳送的警示。

若要深入瞭解監視，請參閱 [Azure 入口網站中的監視](./backup-azure-monitoring-built-in-monitor.md)和[使用 Azure 監視器進行監視](./backup-azure-monitoring-use-azuremonitor.md)。

## <a name="management-operations"></a>管理作業

Azure 備份利用其支援的豐富管理作業，輕鬆管理已備份的 SAP HANA 資料庫。 下一節詳細討論這些作業。

### <a name="run-an-on-demand-backup"></a>執行隨選備份

備份會根據原則排程執行。 您可以視需要執行備份，如下所示：

1. 在保存庫功能表中，按一下 [備份項目]。
2. 在 [備份項目] 中，選取執行 SAP Hana 資料庫的 VM，然後按一下 [立即備份]。
3. 在 [ **立即備份**] 中，選擇您想要執行的備份類型。 然後按一下 [確定] 。 系統會根據與此備份專案相關聯的原則來保留此備份。
4. 監視入口網站通知。 您可以在保存庫儀表板中監視作業進度 > [備份作業] > [進行中]。 根據您的資料庫大小，建立初始備份可能需要花一點時間。

依預設，隨選備份的保留期為45天。

### <a name="hana-native-client-integration"></a>HANA 原生用戶端整合

#### <a name="backup"></a>Backup

從任何 HANA 原生用戶端觸發的隨選備份 (到 **Backint**) 會顯示在 [備份項目] 頁面上的備份清單中。

![上次備份執行](./media/sap-hana-db-manage/last-backups.png)

您也可以從 [[備份作業]](#monitor-manual-backup-jobs-in-the-portal) 頁面**監視這些備份**。

這些隨選備份也會顯示在還原點的清單中，以供還原。

![還原點數清單](./media/sap-hana-db-manage/list-restore-points.png)

#### <a name="restore"></a>還原

從 HANA 原生用戶端 (使用 **Backint**) 觸發的還原，可以從 [[備份作業]](#monitor-manual-backup-jobs-in-the-portal) 頁面**監視**。

### <a name="run-sap-hana-native-client-backup-on-a-database-with-azure-backup-enabled"></a>在啟用 Azure 備份的資料庫上執行 SAP Hana native client 備份

如果您想要進行使用 Azure 備份備份的資料庫的本機備份 (使用 HANA Studio / Cockpit)，請執行下列動作：

1. 等待資料庫的完整或記錄備份完成。 檢查 SAP HANA Studio/駕駛環境中的狀態。
2. 停用記錄備份，並將備份類別目錄設定為相關資料庫的檔案系統。
3. 若要這麼做，請按兩下 **systemdb** >  > **選取資料庫** > **篩選 (記錄)** 。
4. 將 **enable_auto_log_backup** 設定為 [否]。
5. 將 **log_backup_using_backint** 設定為 [False]。
6. 對於資料庫進行隨選完整備份。
7. 等待完整備份和目錄備份完成。
8. 將先前的設定還原回 Azure：
   * 將 **enable_auto_log_backup** 設定為 [是]。
   * 將 **log_backup_using_backint** 設定為 [True]。

### <a name="change-policy"></a>變更原則

您可以變更 SAP HANA 備份項目的基礎原則。

* 在保存庫儀表板中，移至 [備份項目]：

  ![選取備份項目](./media/sap-hana-db-manage/backup-items.png)

* 選擇 [Azure VM 中的 SAP HANA]

  ![選擇 [Azure VM 中的 SAP HANA]](./media/sap-hana-db-manage/sap-hana-in-azure-vm.png)

* 選擇您想要變更基礎原則的備份項目
* 按一下現有的備份原則

  ![選取現有的備份原則](./media/sap-hana-db-manage/existing-backup-policy.png)

* 變更原則，並從清單中選擇。 視需要[建立新的備份原則](./backup-azure-sap-hana-database.md#create-a-backup-policy)。

  ![從下拉式清單中選擇原則](./media/sap-hana-db-manage/choose-backup-policy.png)

* 儲存變更

  ![儲存變更](./media/sap-hana-db-manage/save-changes.png)

* 修改原則會影響所有相關聯的備份項目，並觸發對應的**設定保護**作業。

>[!NOTE]
> 除了新的復原點以外，保留期限內的任何變更也會回溯套用。
>
> 增量備份原則無法用於 SAP Hana 資料庫。 這些資料庫目前不支援增量備份。

### <a name="modify-policy"></a>修改原則

修改原則以變更備份類型、頻率和保留範圍。

>[!NOTE]
>除了新的復原點以外，保留期限內的任何變更也會回溯套用。

1. 在保存庫儀表板中，移至 [管理 > 備份原則]，然後選擇您想要編輯的原則。

   ![選擇要編輯的原則](./media/sap-hana-db-manage/manage-backup-policies.png)

1. 選取 [修改]。

   ![選取 [修改]](./media/sap-hana-db-manage/modify-policy.png)

1. 選擇備份類型的頻率。

   ![選擇備份頻率](./media/sap-hana-db-manage/choose-frequency.png)

修改原則會影響所有相關聯的備份項目，並觸發對應的**設定保護**作業。

### <a name="inconsistent-policy"></a>不一致的原則

有時候，修改原則操作可能會導致某些備份項目的**不一致**原則版本。 在觸發修改原則作業之後，針對備份項目的對應**設定保護**作業失敗時，就會發生這種情況。 這會在備份項目檢視中顯示如下：

![不一致的原則](./media/sap-hana-db-manage/inconsistent-policy.png)

您只要按一下，就可以修正所有受影響項目的原則版本：

![修正原則版本](./media/sap-hana-db-manage/fix-policy-version.png)

### <a name="stop-protection-for-an-sap-hana-database"></a>停止 SAP Hana 資料庫的保護

您可以用幾種方式來停止保護 SAP Hana 資料庫：

* 停止所有未來的備份作業並刪除所有復原點。
* 停止所有未來的備份作業但保留復原點不變。

如果您選擇保留復原點，請記住下列詳細資料：

* 所有復原點會永遠保持不變，而且所有的剪除都會停止保護，但保留資料。
* 您需支付受保護執行個體和已使用儲存體的費用。 如需詳細資訊，請參閱 [Azure 備份服務定價](https://azure.microsoft.com/pricing/details/backup/)。
* 如果您刪除資料來源，但沒有停止備份，新的備份將會失敗。

若要停止保護資料庫：

* 在保存庫儀表板上，選取 [備份項目]。
* 在 [備份管理類型] 底下，選取 [Azure VM 中的 SAP HANA]

  ![選取 [Azure VM 中的 SAP HANA]](./media/sap-hana-db-manage/sap-hana-azure-vm.png)

* 選取您要停止保護的資料庫：

  ![選取要停止保護的資料庫](./media/sap-hana-db-manage/select-database.png)

* 在資料庫功能表上，選取 [停止備份]。

  ![選取 [停止備份]](./media/sap-hana-db-manage/stop-backup.png)

* 在 [停止備份] 功能表上，選取是否要保留或刪除資料。 若有需要，可提供原因或註解。

  ![選取保留或刪除資料](./media/sap-hana-db-manage/retain-backup-data.png)

* 選取 [停止備份]。

### <a name="resume-protection-for-an-sap-hana-database"></a>繼續 SAP HANA 資料庫的保護

您停止保護 SAP HANA 資料庫時，如果您選取 [保留備份資料] 選項，您可以在稍後恢復保護。 如果您未保留備份資料，則無法繼續保護。

若要繼續 SAP HANA 資料庫的保護：

* 開啟備份項目，然後選取 [繼續備份]。

   ![選取 [繼續備份]](./media/sap-hana-db-manage/resume-backup.png)

* 在 [備份原則] 功能表上選取原則，然後選取 [儲存]。

### <a name="upgrading-from-sdc-to-mdc"></a>從 SDC 升級至 MDC

瞭解如何在[從 SDC 升級至 MDC](backup-azure-sap-hana-database-troubleshoot.md#sdc-to-mdc-upgrade-with-a-change-in-sid) 之後，繼續備份 SAP HANA 資料庫。

### <a name="upgrading-from-sdc-to-mdc-without-a-sid-change"></a>在沒有 SID 變更的情況下從 SDC 升級至 MDC

瞭解如何 [在從 SDC 升級至 MDC 之後](backup-azure-sap-hana-database-troubleshoot.md#sdc-to-mdc-upgrade-with-no-change-in-sid)，繼續備份 SID 未變更的 SAP Hana 資料庫。

### <a name="unregister-an-sap-hana-instance"></a>取消註冊 SAP HANA 執行個體

在停用保護之後但在刪除保存庫之前，將 SAP HANA 執行個體取消註冊：

* 在保存庫儀表板上，選取 [管理] 下的 [備份基礎結構]。

   ![選取 [備份基礎結構]](./media/sap-hana-db-manage/backup-infrastructure.png)

* 選取 [備份管理類型] 做為 **Azure VM 中的工作負載**

   ![選取備份管理類型做為 Azure VM 中的工作負載](./media/sap-hana-db-manage/backup-management-type.png)

* 在 [受保護的執行個體] 中，選取要取消註冊的伺服器。 若要刪除保存庫，您必須將所有伺服器/執行個體取消註冊。

* 以滑鼠右鍵按一下受保護的執行個體，然後選取 [取消註冊]。

   ![選取 [取消註冊]](./media/sap-hana-db-manage/unregister.png)

### <a name="re-register-extension-on-the-sap-hana-server-vm"></a>在 SAP HANA 伺服器 VM 上重新註冊擴充功能

有時候，VM 上的工作負載延伸可能會因為一個原因或另一個原因而受到影響。 在這種情況下，VM 上觸發的所有作業將會開始失敗。 接著，您可能需要在 VM 上重新註冊此延伸模組。 重新註冊作業會重新安裝 VM 上的工作負載備份延伸模組，以便繼續進行操作。

請小心使用此選項：當在具有狀況良好擴充功能的 VM 上觸發時，此作業會導致擴充功能重新啟動。 這可能會導致所有進行中的工作失敗。 在觸發重新註冊作業之前，請先檢查一個或多個[徵兆](backup-azure-sap-hana-database-troubleshoot.md#re-registration-failures)：

## <a name="next-steps"></a>後續步驟

* 瞭解如何[對 SAP HANA 資料庫備份時的常見問題進行疑難排解](./backup-azure-sap-hana-database-troubleshoot.md)。
