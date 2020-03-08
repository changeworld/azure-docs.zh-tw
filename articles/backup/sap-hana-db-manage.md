---
title: 管理 Azure Vm 上已備份的 SAP Hana 資料庫
description: 在本文中，您將瞭解管理和監視在 Azure 虛擬機器上執行 SAP Hana 資料庫的一般工作。
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: a9462f8608fc5ae35255ac321a0742b3f1834fde
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78382468"
---
# <a name="manage-and-monitor-backed-up-sap-hana-databases"></a>管理和監視已備份的 SAP Hana 資料庫

本文說明管理和監視 Azure 虛擬機器（VM）上執行的 SAP Hana 資料庫，以及由[Azure 備份](https://docs.microsoft.com/azure/backup/backup-overview)服務備份到 Azure 備份復原服務保存庫的一般工作。 您將瞭解如何監視作業和警示、觸發隨選備份、編輯原則、停止和繼續資料庫保護，以及從備份取消註冊 VM。

如果您尚未為 SAP Hana 資料庫設定備份，請參閱[在 Azure vm 上備份 SAP Hana 資料庫](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)。

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>在入口網站中監視手動備份作業

Azure 備份會在 Azure 入口網站的 [**備份作業**] 區段中顯示所有手動觸發的作業。

![備份作業區段](./media/sap-hana-db-manage/backup-jobs.png)

您在此入口網站中看到的工作包括資料庫探索和註冊，以及備份和還原作業。 這一節不會顯示已排程的工作，包括記錄備份。 從 SAP Hana 的原生用戶端（Studio/駕駛中心/DBA 環境）手動觸發備份，也不會顯示在這裡。

![備份作業清單](./media/sap-hana-db-manage/backup-jobs-list.png)

若要深入瞭解監視，請移至[Azure 入口網站中的 [監視](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor)]，並[使用 Azure 監視器進行監視](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)。

## <a name="view-backup-alerts"></a>檢視備份警示

警示是監視 SAP Hana 資料庫備份的簡單方法。 警示可協助您專注于最關心的事件，而不會因為備份所產生的許多事件而遺失。 Azure 備份可讓您設定警示，並可加以監視，如下所示：

* 登入 [Azure 入口網站](https://portal.azure.com/)。
* 在保存庫儀表板上，選取 [**備份警示**]。

  ![保存庫儀表板上的備份警示](./media/sap-hana-db-manage/backup-alerts-dashboard.png)

* 您將能夠看到警示：

  ![備份警示清單](./media/sap-hana-db-manage/backup-alerts-list.png)

* 按一下警示以查看更多詳細資料：

  ![警示詳細資料](./media/sap-hana-db-manage/alert-details.png)

今天，Azure 備份可讓您透過電子郵件傳送警示。 這些警示包括：

* 已針對所有備份失敗觸發。
* 依照錯誤碼，在資料庫層級進行匯總。
* 只有在資料庫第一次備份失敗時才傳送。

此深入瞭解監視、移至[Azure 入口網站中的監視](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor)，以及[使用 Azure 監視器進行監視](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)。

## <a name="management-operations"></a>管理作業

Azure 備份利用其支援的豐富管理作業，輕鬆管理已備份的 SAP Hana 資料庫。 下列各節將更詳細地討論這些作業。

### <a name="run-an-ad-hoc-backup"></a>執行臨機操作備份

備份會根據原則排程執行。 您可以視需要執行備份，如下所示：

1. 在保存庫功能表中，按一下 [備份項目]。
2. 在 [**備份專案**] 中，選取執行 SAP Hana 資料庫的 VM，然後按一下 [**立即備份**]。
3. 在 [**立即備份**] 中，使用行事曆控制項選取復原點應保留的最後一天。 然後按一下 **[確定]** 。
4. 監視入口網站通知。 您可以在保存庫儀表板中監視作業進度 > [備份作業] > [進行中]。 視資料庫的大小而定，建立初始備份可能需要一段時間。

### <a name="run-sap-hana-native-client-backup-on-a-database-with-azure-backup-enabled"></a>在已啟用 Azure 備份的資料庫上執行 SAP Hana native client 備份

如果您想要進行使用 Azure 備份備份的資料庫的本機備份（使用 HANA Studio/駕駛中心），請執行下列動作：

1. 等待資料庫的完整或記錄備份完成。 檢查 SAP Hana Studio/駕駛環境中的狀態。
2. 停用記錄備份，並將備份類別目錄設定為相關資料庫的檔案系統。
3. 若要這麼做，請按兩下 [systemdb ** > 設定**] > **選取 [資料庫** > **篩選（記錄）** ]。
4. 將**enable_auto_log_backup**設定為 [**否**]。
5. 將**log_backup_using_backint**設定為**False**。
6. 依照需求進行資料庫的完整備份。
7. 等待完整備份和目錄備份完成。
8. 將先前的設定還原回 Azure：
   * 將**enable_auto_log_backup**設定為 **[是]** 。
   * 將**log_backup_using_backint**設定為**True**。

### <a name="change-policy"></a>變更原則

您可以變更 SAP Hana 備份專案的基礎原則。

* 在保存庫儀表板中，移至 [**備份專案**]：

  ![選取備份項目](./media/sap-hana-db-manage/backup-items.png)

* 選擇**AZURE VM 中的 SAP Hana**

  ![選擇 Azure VM 中的 SAP Hana](./media/sap-hana-db-manage/sap-hana-in-azure-vm.png)

* 選擇您想要變更其基礎原則的備份專案
* 按一下現有的備份原則

  ![選取現有的備份原則](./media/sap-hana-db-manage/existing-backup-policy.png)

* 變更原則，並從清單中選擇。 視需要[建立新的備份原則](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database#create-a-backup-policy)。

  ![從下拉式清單中選擇原則](./media/sap-hana-db-manage/choose-backup-policy.png)

* 儲存變更

  ![儲存變更](./media/sap-hana-db-manage/save-changes.png)

* 修改原則會影響所有相關聯的備份專案，並觸發對應的 [**設定保護**工作]。

>[!NOTE]
> 除了新的復原點以外，保留期限內的任何變更也會反而要套用。
>
> 增量備份原則無法用於 SAP Hana 資料庫。 這些資料庫目前不支援增量備份。

### <a name="stop-protection-for-an-sap-hana-database"></a>停止 SAP Hana 資料庫的保護

您可以用幾種方式來停止保護 SAP Hana 資料庫：

* 停止所有未來的備份作業並刪除所有復原點。
* 停止所有未來的備份作業但保留復原點不變。

如果您選擇保留復原點，請記住下列詳細資料：

* 所有復原點會永遠保持不變，而停止保護但保留資料時，所有清除作業都應該停止。
* 系統會向您收取受保護實例和已耗用儲存體的費用。 如需詳細資訊，請參閱[Azure 備份定價](https://azure.microsoft.com/pricing/details/backup/)。
* 如果您刪除資料來源，但沒有停止備份，新的備份將會失敗。

若要停止保護資料庫：

* 在保存庫儀表板上，選取 [**備份專案**]。
* 在 [**備份管理類型**] 底下，選取 [ **Azure VM 中的 SAP Hana** ]

  ![選取 Azure VM 中的 SAP Hana](./media/sap-hana-db-manage/sap-hana-azure-vm.png)

* 選取您想要停止保護的資料庫：

  ![選取要停止保護的資料庫](./media/sap-hana-db-manage/select-database.png)

* 在資料庫功能表上，選取 [停止備份]。

  ![選取 [停止備份]](./media/sap-hana-db-manage/stop-backup.png)

* 在 [**停止備份**] 功能表上，選取是否要保留或刪除資料。 如果您想要的話，請提供原因和批註。

  ![選取保留或刪除資料](./media/sap-hana-db-manage/retain-backup-data.png)

* 選取 [**停止備份**]。

### <a name="resume-protection-for-an-sap-hana-database"></a>繼續保護 SAP Hana 資料庫

當您停止保護 SAP Hana 資料庫時，如果您選取 [**保留備份資料**] 選項，稍後可以繼續保護。 如果您未保留備份的資料，將無法繼續保護。

若要繼續保護 SAP Hana 資料庫：

* 開啟備份專案，然後選取 [**繼續備份**]。

   ![選取 [繼續備份]](./media/sap-hana-db-manage/resume-backup.png)

* 在 [備份原則] 功能表上選取原則，然後選取 [儲存]。

### <a name="upgrading-from-sap-hana-10-to-20"></a>從 SAP Hana 1.0 升級至2。0

瞭解[從 SAP Hana 1.0 升級至2.0 之後](backup-azure-sap-hana-database-troubleshoot.md#upgrading-from-sap-hana-10-to-20)，如何繼續備份 SAP Hana 資料庫。

### <a name="upgrading-without-a-sid-change"></a>不使用 SID 變更進行升級

瞭解如何繼續備份其[SID 在升級後未變更](backup-azure-sap-hana-database-troubleshoot.md#upgrading-without-an-sid-change)的 SAP Hana 資料庫。

### <a name="unregister-an-sap-hana-database"></a>取消註冊 SAP Hana 資料庫

在您停用保護之後但在刪除保存庫之前，取消註冊 SAP Hana 實例：

* 在保存庫儀表板的 [**管理**] 底下，選取 [**備份基礎結構**]。

   ![選取 [備份基礎結構]](./media/sap-hana-db-manage/backup-infrastructure.png)

* 選取**備份管理類型**做為**Azure VM 中的工作負載**

   ![選取備份管理類型做為 Azure VM 中的工作負載](./media/sap-hana-db-manage/backup-management-type.png)

* 在 [**受保護的伺服器**] 中，選取要取消註冊的實例。 若要刪除保存庫，您必須取消註冊所有伺服器/實例。

* 以滑鼠右鍵按一下受保護的實例，然後選取 [**取消註冊**]。

   ![選取 [取消註冊]](./media/sap-hana-db-manage/unregister.png)

## <a name="next-steps"></a>後續步驟

* 瞭解如何針對[備份 SAP Hana 資料庫時的常見問題進行疑難排解。](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot)
