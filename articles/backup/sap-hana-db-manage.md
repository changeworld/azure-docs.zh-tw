---
title: 在 Azure VM 上管理備份的 SAP HANA 資料庫
description: 在本文中，瞭解用於管理和監視在 Azure 虛擬機器上運行的 SAP HANA 資料庫的常見任務。
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: 89fd7f23163d301817e767771257d9bc6f4ed526
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480057"
---
# <a name="manage-and-monitor-backed-up-sap-hana-databases"></a>管理和監視已備份的 SAP Hana 資料庫

本文介紹用於管理和監視在 Azure 虛擬機器 （VM） 上運行並由[Azure 備份](https://docs.microsoft.com/azure/backup/backup-overview)服務備份到 Azure 備份恢復服務保存庫的 SAP HANA 資料庫的常見任務。 您將學習如何監視作業和警報、觸發按需備份、編輯策略、停止和恢復資料庫保護以及取消註冊 VM 的備份。

如果尚未為 SAP HANA 資料庫配置備份，請參閱在[Azure VM 上備份 SAP HANA 資料庫](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)。

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>監視門戶中的手動備份作業

Azure 備份在 Azure 門戶的 **"備份作業**"部分中顯示所有手動觸發的作業。

![備份作業部分](./media/sap-hana-db-manage/backup-jobs.png)

在此門戶中看到的作業包括資料庫發現和註冊以及備份和還原操作。 本節中未顯示計畫作業（包括記錄備份）。 來自 SAP HANA 本機用戶端（工作室/駕駛艙/DBA 駕駛艙）手動觸發的備份也不會在此處顯示。

![備份作業清單](./media/sap-hana-db-manage/backup-jobs-list.png)

要瞭解有關監視的更多內容，請使用[Azure 門戶轉到監視](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor)和[使用 Azure 監視器進行監視](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)。

## <a name="view-backup-alerts"></a>檢視備份警示

警報是監視 SAP HANA 資料庫備份的一種簡單方法。 警報可説明您專注于您最關心的事件，而不會在備份生成的眾多事件中丟失。 Azure 備份允許您設置警報，並且可以按照如下方式監視警報：

* 登錄到 Azure[門戶](https://portal.azure.com/)。
* 在保存庫儀表板上，選擇 **"備份警報**"。

  ![保存庫儀表板上的備份警報](./media/sap-hana-db-manage/backup-alerts-dashboard.png)

* 您將能夠看到警報：

  ![備份警報清單](./media/sap-hana-db-manage/backup-alerts-list.png)

* 按一下警報以查看更多詳細資訊：

  ![警示詳細資料](./media/sap-hana-db-manage/alert-details.png)

如今，Azure 備份允許通過電子郵件發送警報。 這些警報包括：

* 為所有備份失敗觸發。
* 按錯誤代碼在資料庫級別合併。
* 僅為資料庫的第一個備份失敗發送。

要瞭解有關監視的更多內容，請使用[Azure 門戶轉到監視](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor)，並使用 Azure 監視器進行[監視](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)。

## <a name="management-operations"></a>管理作業

Azure 備份通過支援的大量管理操作，使備份的 SAP HANA 資料庫的管理變得簡單。 以下各節將更詳細地討論這些操作。

### <a name="run-an-on-demand-backup"></a>執行隨選備份

備份根據策略計畫運行。 您可以按需運行備份，如下所示：

1. 在保存庫功能表中，按一下 [備份項目]****。
2. 在**備份項**中，選擇運行 SAP HANA 資料庫的 VM，然後按一下 **"立即備份**"。
3. 在 **"立即備份"** 中，使用日曆控制項選擇應保留復原點的最後一天。 然後按一下 **[確定]**。
4. 監視入口網站通知。 您可以在保存庫儀表板>**正在進行的備份作業** > **中**監視作業進度。 根據資料庫的大小，創建初始備份可能需要一段時間。

### <a name="hana-native-client-integration"></a>HANA 本機用戶端集成

現在，從任何 HANA 本機用戶端觸發的按需完整備份將在 **"備份專案"** 頁上作為完整備份顯示。

![上次備份運行](./media/sap-hana-db-manage/last-backups.png)

這些臨時完整備份也會顯示在還原點的清單中。

![還原點清單](./media/sap-hana-db-manage/list-restore-points.png)

### <a name="run-sap-hana-native-client-backup-on-a-database-with-azure-backup-enabled"></a>在啟用 Azure 備份的資料庫上運行 SAP HANA 本機用戶端備份

如果要獲取正在使用 Azure 備份備份的資料庫的本地備份（使用 HANA Studio / 駕駛艙），請執行以下操作：

1. 等待資料庫完成的任何完整備份或記錄備份。 檢查 SAP HANA 工作室/駕駛艙中的狀態。
2. 禁用記錄備份，並將備份目錄設置為相關資料庫的檔案系統。
3. 為此，按兩下**系統db** > **配置** > **選擇資料庫** > **篩選器（日誌）。**
4. **enable_auto_log_backup**設置為 **"否**"。
5. **將log_backup_using_backint**設置為**false**。
6. 按需對資料庫進行完整備份。
7. 等待完整備份和目錄備份完成。
8. 將以前的設置還原到 Azure 的設置：
   * **將enable_auto_log_backup**設置為 **"是**"。
   * **將log_backup_using_backint**設置為 **"真**"。

### <a name="change-policy"></a>變更原則

您可以更改 SAP HANA 備份項的基礎策略。

* 在保存庫儀表板中，轉到 **"備份專案**" ：

  ![選取備份項目](./media/sap-hana-db-manage/backup-items.png)

* **在 Azure VM 中選擇 SAP HANA**

  ![在 Azure VM 中選擇 SAP HANA](./media/sap-hana-db-manage/sap-hana-in-azure-vm.png)

* 選擇要更改其基礎策略的備份項
* 按一下現有備份策略

  ![選擇現有備份策略](./media/sap-hana-db-manage/existing-backup-policy.png)

* 更改策略，從清單中選擇。 如果需要[，創建新的備份策略](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database#create-a-backup-policy)。

  ![從下拉清單中選擇策略](./media/sap-hana-db-manage/choose-backup-policy.png)

* 保存更改

  ![保存更改](./media/sap-hana-db-manage/save-changes.png)

* 策略修改將影響所有關聯的備份專案，並觸發相應的**配置保護**作業。

>[!NOTE]
> 保留期中的任何更改都將追溯應用於除新復原點之外的所有舊復原點。
>
> 增量備份策略不能用於 SAP HANA 資料庫。 這些資料庫當前不支援增量備份。

### <a name="modify-policy"></a>修改原則

修改策略以更改備份類型、頻率和保留範圍。

>[!NOTE]
>保留期的任何更改都將追溯應用於除新復原點之外的所有舊復原點。

1. 在保存庫儀表板中，轉到 **"管理>備份策略**並選擇要編輯的策略。

   ![選擇要編輯的策略](./media/sap-hana-db-manage/manage-backup-policies.png)

1. 選取 [修改]****。

   ![選擇"修改"](./media/sap-hana-db-manage/modify-policy.png)

1. 選擇備份類型的頻率。

   ![選擇備份頻率](./media/sap-hana-db-manage/choose-frequency.png)

策略修改將影響所有相關的備份項，並觸發相應的**配置保護**作業。

### <a name="inconsistent-policy"></a>不一致的策略

有時，修改策略操作可能會導致某些備份項的策略版本**不一致**。 當觸發修改策略操作後備份項的相應**配置保護**作業失敗時，將發生這種情況。 它在備份項視圖中如下所示：

![不一致的策略](./media/sap-hana-db-manage/inconsistent-policy.png)

只需按一下一下，即可修復所有受影響專案的策略版本：

![修復策略版本](./media/sap-hana-db-manage/fix-policy-version.png)

### <a name="stop-protection-for-an-sap-hana-database"></a>停止 SAP Hana 資料庫的保護

您可以用幾種方式來停止保護 SAP Hana 資料庫：

* 停止所有未來的備份作業並刪除所有復原點。
* 停止所有未來的備份作業但保留復原點不變。

如果您選擇保留復原點，請記住下列詳細資料：

* 所有復原點會永遠保持不變，而停止保護但保留資料時，所有清除作業都應該停止。
* 受保護實例和消耗的存儲將向您收費。 有關詳細資訊，請參閱[Azure 備份定價](https://azure.microsoft.com/pricing/details/backup/)。
* 如果您刪除資料來源，但沒有停止備份，新的備份將會失敗。

若要停止保護資料庫：

* 在保存庫儀表板上，選擇 **"備份專案**"。
* 在**備份管理類型**下，**在 Azure VM 中選擇 SAP HANA**

  ![在 Azure VM 中選擇 SAP HANA](./media/sap-hana-db-manage/sap-hana-azure-vm.png)

* 選擇要停止保護的資料庫：

  ![選擇資料庫以停止保護](./media/sap-hana-db-manage/select-database.png)

* 在資料庫功能表上，選取 [停止備份]****。

  ![選擇停止備份](./media/sap-hana-db-manage/stop-backup.png)

* 在 **"停止備份"** 功能表上，選擇是保留還是刪除資料。 如果需要，請提供理由和注釋。

  ![選擇保留或刪除資料](./media/sap-hana-db-manage/retain-backup-data.png)

* 選擇 **"停止備份**"。

### <a name="resume-protection-for-an-sap-hana-database"></a>恢復 SAP HANA 資料庫的保護

停止 SAP HANA 資料庫的保護時，如果選擇"**保留備份資料"** 選項，則可以稍後恢復保護。 如果不保留備份的資料，您將無法恢復保護。

要恢復 SAP HANA 資料庫的保護，

* 打開備份項並選擇 **"恢復備份**"。

   ![選擇恢復備份](./media/sap-hana-db-manage/resume-backup.png)

* 在 [備份原則]**** 功能表上選取原則，然後選取 [儲存]****。

### <a name="upgrading-from-sap-hana-10-to-20"></a>從 SAP HANA 1.0 升級到 2.0

瞭解如何[在從 SAP HANA 1.0 升級到 2.0 後繼續為 SAP HANA](backup-azure-sap-hana-database-troubleshoot.md#upgrading-from-sap-hana-10-to-20)資料庫進行備份。

### <a name="upgrading-without-a-sid-change"></a>升級時無需更改 SID

瞭解如何繼續備份其[SID 在升級後未更改](backup-azure-sap-hana-database-troubleshoot.md#upgrading-without-an-sid-change)的 SAP HANA 資料庫。

### <a name="unregister-an-sap-hana-instance"></a>取消註冊 SAP HANA 實例

在禁用保護後，但在刪除保存庫之前取消註冊 SAP HANA 實例：

* 在保存庫儀表板上，在 **"管理**"下，選擇 **"備份基礎結構**"。

   ![選取 [備份基礎結構]](./media/sap-hana-db-manage/backup-infrastructure.png)

* 選擇**備份管理類型**作為**Azure VM 中的工作負荷**

   ![選擇備份管理類型作為 Azure VM 中的工作負荷](./media/sap-hana-db-manage/backup-management-type.png)

* 在**受保護伺服器**中，選擇要取消註冊的實例。 要刪除保存庫，必須取消註冊所有伺服器/實例。

* 按右鍵受保護的實例並選擇 **"取消註冊**"。

   ![選擇取消註冊](./media/sap-hana-db-manage/unregister.png)

### <a name="re-register-extension-on-the-sap-hana-server-vm"></a>在 SAP HANA 伺服器 VM 上重新註冊擴展

有時，VM 上的工作負載擴展可能會由於以下或其他原因受到影響。 在這種情況下，在 VM 上觸發的所有操作將開始失敗。 然後，您可能需要在 VM 上重新註冊擴展。 重新註冊操作在 VM 上重新安裝工作負載備份擴展，以便繼續操作。

使用此選項時要小心：在具有已正常運行的擴展的 VM 上觸發時，此操作將導致擴展重新開機。 這可能會導致所有正在進行的作業失敗。 在觸發重新註冊操作之前，請檢查一個或多個[症狀](backup-azure-sap-hana-database-troubleshoot.md#re-registration-failures)。

## <a name="next-steps"></a>後續步驟

* 瞭解如何[在備份 SAP HANA 資料庫時解決常見問題。](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot)
