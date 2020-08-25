---
title: 使用 MARS 代理程式備份 Windows 機器
description: 使用 Microsoft Azure 復原服務 (MARS) 代理程式來備份 Windows 機器。
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 381df40dfe4149b2f43dc22bb2186ec1f17b0515
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88825881"
---
# <a name="back-up-windows-server-files-and-folders-to-azure"></a>將 Windows Server 檔案和資料夾備份至 Azure

本文說明如何使用 [Azure 備份](backup-overview.md) 服務和 MICROSOFT AZURE 復原服務 (MARS) 代理程式來備份 Windows 機器。 MARS 也稱為 Azure 備份代理程式。

在本文中，您將了解如何：

> [!div class="checklist"]
>
> * 確認先決條件
> * 建立備份原則和排程。
> * 執行隨選備份。

## <a name="before-you-start"></a>開始之前

* 瞭解 [Azure 備份如何使用 MARS 代理程式來備份 Windows 機器](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)。
* 瞭解在次要 MABS 或 Data Protection Manager 伺服器上執行 MARS 代理程式的 [備份架構](backup-architecture.md#architecture-back-up-to-dpmmabs) 。
* 複習 [支援的內容，以及](backup-support-matrix-mars-agent.md) MARS 代理程式可以備份的內容。
* 確認您要備份的機器上的[網際網路存取](install-mars-agent.md#verify-internet-access)。
* 如果 MARS 代理程式未安裝，請 [在此](install-mars-agent.md)瞭解如何進行安裝。

## <a name="create-a-backup-policy"></a>建立備份原則

備份原則會指定何時建立資料的快照集來建立復原點。 它也會指定保留復原點的時間長度。 您可以使用 MARS 代理程式來設定備份原則。

Azure 備份不會自動將日光節約時間 (DST) 列入考慮。 此預設值可能會在實際時間和排程的備份時間之間造成某些差異。

若要建立備份原則：

1. 下載並註冊 MARS 代理程式之後，請開啟代理程式主控台。 您可以透過在您的電腦中搜尋 **Microsoft Azure 備份**來找出備份。  

1. 在 [ **動作**] 底下，選取 [ **排程備份**]。

    ![Windows Server 備份排程](./media/backup-configure-vault/schedule-first-backup.png)
1. 在 [排程備份] 嚮導中，選取 [**開始**使用  >  **]**。
1. 在 [ **選取要備份的專案**] 底下，選取 [ **加入專案**]。

    ![新增要備份的專案](./media/backup-azure-manage-mars/select-item-to-backup.png)

1. 在 [ **選取專案** ] 方塊中，選取要備份的專案，然後選取 **[確定]**。

    ![選取要備份的項目](./media/backup-azure-manage-mars/selected-items-to-backup.png)

1. 在 [ **選取要備份的專案** ] 頁面上，選取 **[下一步]**。
1. 在 [ **指定備份排程** ] 頁面上，指定每日或每週備份的時間。 接著，選取 [下一步]  。

    * 執行備份時，會建立復原點。
    * 在您的環境中建立的復原點數目取決於您的備份排程。
    * 您每天最多可以排程三個每天備份。 在下列範例中，會進行兩個每日備份，一個在午夜，另一個在下午6:00。

        ![設定每日備份排程](./media/backup-configure-vault/day-schedule.png)

    * 您也可以執行每週備份。 在下列範例中，會在每隔星期日和星期三的上午9:30 和上午1:00 執行備份。

        ![設定每週備份排程](./media/backup-configure-vault/week-schedule.png)

1. 在 [ **選取保留原則** ] 頁面上，指定如何儲存資料的歷程記錄複本。 接著，選取 [下一步]  。

    * 保留設定會指定要儲存的復原點，以及儲存它們的時間長度。
    * 針對每日保留設定，您表示在指定的每日保留時間內，最新的復原點會保留指定的天數。 或者，您可以指定每月的保留原則，以指出在每個月的30日建立的復原點應儲存12個月。
    * 每日和每週復原點的保留期通常會與備份排程一致。 因此，當排程觸發備份時，備份所建立的復原點會儲存在每日或每週保留原則所指定的持續時間內。
    * 在下例中︰

        * 午夜和下午6:00 的每日備份都會保留七天。
        * 在星期六午夜和下午6:00 執行的備份會保留四周。
        * 在當月的最後一個星期六午夜和下午6:00 的備份會保留12個月。
        * 在3月最後一個星期六所進行的備份會保留10年。

        ![保留原則的範例](./media/backup-configure-vault/retention-example.png)

1. 在 [ **選擇初始備份類型** ] 頁面上，決定您要透過網路進行初始備份，或使用離線備份。 若要透過網路進行初始備份，請選取 [**自動透過網路**  >  **]**。

    如需離線備份的詳細資訊，請參閱 [使用 Azure 資料箱進行離線備份](offline-backup-azure-data-box.md)。

    ![選擇初始備份類型](./media/backup-azure-manage-mars/choose-initial-backup-type.png)

1. 在 [ **確認** ] 頁面上，檢查資訊，然後選取 **[完成]**。

    ![確認備份類型](./media/backup-azure-manage-mars/confirm-backup-type.png)

1. 當精靈建立好備份排程時，請選取 [關閉] 。

    ![查看備份排程進度](./media/backup-azure-manage-mars/confirm-modify-backup-process.png)

在安裝代理程式的每部電腦上建立原則。

### <a name="do-the-initial-backup-offline"></a>離線備份初始備份

您可以透過網路自動執行初始備份，也可以離線備份。 如果您有大量資料需要傳輸大量的網路頻寬，則初始備份的離線植入將會很有用。

若要進行離線傳輸：

1. 將備份資料寫入至暫存位置。
1. 使用 AzureOfflineBackupDiskPrep 工具，將資料從暫存位置複製到一或多個 SATA 磁片。

    此工具會建立 Azure 匯入作業。 如需詳細資訊，請參閱 [什麼是 Azure 匯入/匯出服務](../storage/common/storage-import-export-service.md)。
1. 將 SATA 磁片傳送至 Azure 資料中心。

    在資料中心，磁片資料會複製到 Azure 儲存體帳戶。 Azure 備份會將資料從儲存體帳戶複製到保存庫，並排程增量備份。

如需離線植入的詳細資訊，請參閱 [使用 Azure 資料箱進行離線備份](offline-backup-azure-data-box.md)。

### <a name="enable-network-throttling"></a>啟用網路節流

您可以藉由啟用網路節流，來控制 MARS 代理程式如何使用網路頻寬。 如果您需要在工作時間內備份資料，但想要控制備份和還原活動所使用的頻寬量，則節流會很有説明。

Azure 備份中的網路節流會使用本機作業系統上的 [服務品質 (QoS) ](/windows-server/networking/technologies/qos/qos-policy-top) 。

您可以在 Windows Server 2012 和更新版本，以及 Windows 8 和更新版本上，取得備份的網路節流。 作業系統應執行最新的 service pack。

若要啟用網路節流：

1. 在 MARS 代理程式中，選取 [ **變更屬性**]。
1. 在 [節流]**** 索引標籤上，選取 [啟用備份作業的網際網路頻寬使用節流功能]****。

    ![設定備份作業的網路節流](./media/backup-configure-vault/throttling-dialog.png)
1. 指定工作時間與非時允許的頻寬。 頻寬值的開始時間為 512 Kbps，最高可達 1023 MBps。 然後選取 [確定]。

## <a name="run-an-on-demand-backup"></a>執行隨選備份

1. 在 MARS 代理程式中，選取 [ **立即備份**]。

    ![立即在 Windows Server 中備份](./media/backup-configure-vault/backup-now.png)

1. 如果 MARS 代理程式版本是2.0.9169.0 或更新版本，您可以設定自訂保留日期。 在 [ **保留備份** 到] 區段中，從行事曆選擇日期。

   ![使用行事曆自訂保留日期](./media/backup-configure-vault/mars-ondemand.png)

1. 在 [ **確認** ] 頁面上，檢查設定，然後選取 [ **備份**]。
1. 選取 [關閉]  即可關閉精靈。 如果您在備份完成之前關閉嚮導，則嚮導會繼續在背景執行。

初始備份完成後，[ **工作已完成** ] 狀態就會出現在備份主控台中。

## <a name="set-up-on-demand-backup-policy-retention-behavior"></a>設定隨選備份原則保留行為

> [!NOTE]
> 此資訊只適用于早于2.0.9169.0 的 MARS 代理程式版本。
>

| 備份排程選項 | 資料保留期間
| -- | --
| 天 | **預設保留**：相當於「每日備份的保留天數」。 <br/><br/> **例外**狀況：如果針對長期保留設定的每日排程備份 (周、月或年) 失敗，則在失敗之後所觸發的隨選備份會被視為長期保留。 否則，會將下一個排定的備份視為長期保留。<br/><br/> **範例案例**：星期四上午8:00 的排程備份失敗。 這份備份被視為每週、每月或每年保留。 因此，第一個隨選備份會在下星期五上午8:00 執行之前的排程備份時，自動標記為每週、每月或每年保留。 這種備份替代于星期四上午8:00 的備份。
| 週 | **預設保留期**：一天。 針對具有每週備份原則的資料來源所執行的隨選備份，會在下一天刪除。 即使它們是資料來源的最新備份，也會被刪除。 <br/><br/> **例外**狀況：如果針對長期保留設定的每週排程備份 (周、月或年) 失敗，則在失敗後立即觸發的隨選備份會被視為長期保留。 否則，會將下一個排定的備份視為長期保留。 <br/><br/> **範例案例**：星期四上午8:00 的排程備份失敗。 這是要考慮每月或每年保留的備份。 因此，在下一次排程備份于上午8:00 點之前觸發的第一個隨選備份會自動標記為每月或每年保留。 這種備份替代于星期四上午8:00 的備份。

如需詳細資訊，請參閱 [建立備份原則](#create-a-backup-policy)。

## <a name="next-steps"></a>後續步驟

* 瞭解如何 [在 Azure 中還原](backup-azure-restore-windows-server.md)檔案。
* 尋找 [有關備份檔案和資料夾的常見問題](backup-azure-file-folder-backup-faq.md)
