---
title: 使用 MARS 代理備份 Windows 電腦
description: 使用 Microsoft Azure 恢復服務 （MARS） 代理備份 Windows 電腦。
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 12463f33a6fa97b33e70b77fb2fcf6b0a27b5790
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408907"
---
# <a name="back-up-windows-machines-by-using-the-azure-backup-mars-agent"></a>使用 Azure 備份 MARS 代理備份 Windows 電腦

本文介紹如何使用[Azure 備份](backup-overview.md)服務和 Microsoft Azure 恢復服務 （MARS） 代理備份 Windows 電腦。 MARS 也稱為 Azure 備份代理。

在本文中，您將了解如何：

> [!div class="checklist"]
>
> * 確認先決條件
> * 創建備份策略和計畫。
> * 執行按需備份。

## <a name="before-you-start"></a>開始之前

* 瞭解[Azure 備份如何使用 MARS 代理備份 Windows 電腦](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)。
* 瞭解在輔助 MABS 或資料保護管理器伺服器上運行 MARS 代理的[備份體系結構](backup-architecture.md#architecture-back-up-to-dpmmabs)。
* 查看支援[的內容以及 MARS 代理可以備份的內容](backup-support-matrix-mars-agent.md)。
* [驗證](install-mars-agent.md#verify-internet-access)要備份的電腦上的 Internet 訪問。
* 如果未安裝 MARS 代理，請瞭解如何[在此處](install-mars-agent.md)安裝它。

## <a name="create-a-backup-policy"></a>建立備份原則 

備份策略指定何時拍攝資料的快照以創建復原點。 它還指定保留復原點的時間。 您可以使用 MARS 代理配置備份策略。

Azure 備份不會自動考慮夏令時 （DST）。 此預設值可能會導致實際時間和計畫備份時間之間存在一些差異。

若要建立備份原則：

1. 下載並註冊 MARS 代理後，打開代理主控台。 您可以透過在您的電腦中搜尋 **Microsoft Azure 備份**來找出備份。  

1. 在 **"操作"** 下，選擇 **"計畫備份**"。

    ![Windows Server 備份排程](./media/backup-configure-vault/schedule-first-backup.png)
1. 在"計畫備份嚮導"中，選擇 **"下** > **一步**入門"。
1. 在 **"選擇要備份的專案**"下，選擇 **"添加專案**"。

    ![添加要備份的專案](./media/backup-azure-manage-mars/select-item-to-backup.png)

1. 在 **"選擇專案"** 框中，選擇要備份的專案，然後選擇 **"確定**"。

    ![選取要備份的項目](./media/backup-azure-manage-mars/selected-items-to-backup.png)

1. 在 **"選擇要備份的專案"** 頁上，選擇 **"下一步**"。
1. 在 **"指定備份計畫"** 頁上，指定何時進行每日或每週備份。 然後選擇 **"下一步**"。

    * 進行備份時將創建復原點。
    * 在環境中創建的復原點數取決於您的備份計畫。
    * 您每天最多可以安排三次備份。 在下面的示例中，每天發生兩次備份，一個在午夜，一個在下午 6：00。

        ![設置每日備份計畫](./media/backup-configure-vault/day-schedule.png)

    * 您也可以運行每週備份。 在下面的示例中，備份在每個備用星期日和週三上午 9：30 和淩晨 1：00 進行。

        ![設置每週備份計畫](./media/backup-configure-vault/week-schedule.png)

1. 在 **"選擇保留原則"** 頁上，指定如何存儲資料的歷史副本。 然後選擇 **"下一步**"。

    * 保留設置指定要存儲的復原點以及存儲它們的時間。
    * 對於每日保留設置，您指示在為每日保留指定的時間，最新的復原點將保留指定天數。 或者，您可以指定每月保留原則，以指示每月 30 日創建的復原點應存儲 12 個月。
    * 每日和每週復原點的保留通常與備份計畫一致。 因此，當計畫觸發備份時，備份創建的復原點將存儲在每日或每週保留原則指定的持續時間內。
    * 在下例中︰

        * 午夜和下午 6：00 的每日備份將保留 7 天。
        * 在星期六午夜和下午 6：00 進行的備份將保留四周。
        * 在每月的最後一個星期六午夜和下午 6：00 進行的備份將保留 12 個月。
        * 在 3 月的最後一個星期六進行的備份將保留 10 年。

        ![保留原則的示例](./media/backup-configure-vault/retention-example.png)

1. 在 **"選擇初始備份類型"** 頁上，確定是通過網路進行初始備份還是使用離線備份。 要通過網路進行初始備份，請在 > **網路上選擇"下****一步**"。

    有關離線備份的詳細資訊，請參閱使用[Azure 資料框進行離線備份](offline-backup-azure-data-box.md)。

    ![選擇初始備份類型](./media/backup-azure-manage-mars/choose-initial-backup-type.png)

1. 在**確認**頁面上，查看資訊，然後選擇 **"完成**"。

    ![確認備份類型](./media/backup-azure-manage-mars/confirm-backup-type.png)

1. 嚮導完成創建備份計畫後，選擇 **"關閉**"。

    ![查看備份計畫進度](./media/backup-azure-manage-mars/confirm-modify-backup-process.png)

在安裝代理的每台電腦上創建策略。

### <a name="do-the-initial-backup-offline"></a>離線執行初始備份

您可以通過網路自動運行初始備份，也可以離線備份。 如果具有大量資料，需要傳輸大量網路頻寬，則初始備份的離線種子設定非常有用。

要執行離線傳輸：

1. 將備份資料寫入暫存位置。
1. 使用 AzureOffline 備份磁片準備工具將資料從暫存位置複製到一個或多個 SATA 磁片。

    該工具將創建 Azure 導入作業。 有關詳細資訊，請參閱什麼是[Azure 導入/匯出服務](https://docs.microsoft.com/azure/storage/common/storage-import-export-service)。
1. 將 SATA 磁片發送到 Azure 資料中心。

    在資料中心，磁片資料將複製到 Azure 存儲帳戶。 Azure 備份將資料從存儲帳戶複製到保存庫，並計畫進行增量備份。

有關離線種子設定的詳細資訊，請參閱使用[Azure 資料框進行離線備份](offline-backup-azure-data-box.md)。

### <a name="enable-network-throttling"></a>啟用網路節流

您可以通過啟用網路限制來控制 MARS 代理如何使用網路頻寬。 如果需要在工作時間備份資料，但希望控制備份和還原活動使用的頻寬，則限制非常有用。

Azure 備份中的網路限制在本地作業系統上使用[服務品質 （QoS）。](https://docs.microsoft.com/windows-server/networking/technologies/qos/qos-policy-top)

備份的網路限制在 Windows Server 2012 及更高版本以及 Windows 8 及更高版本上可用。 作業系統應運行最新的服務包。

要啟用網路限制：

1. 在 MARS 代理中，選擇 **"更改屬性**"。
1. 在 [節流]**** 索引標籤上，選取 [啟用備份作業的網際網路頻寬使用節流功能]****。

    ![為備份操作設置網路限制](./media/backup-configure-vault/throttling-dialog.png)
1. 指定工作時間和非工作時間的允許頻寬。 頻寬值從 512 Kbps 開始，最高達到 1，023 MBps。 然後選擇 **"確定**"。

## <a name="run-an-on-demand-backup"></a>執行隨選備份

1. 在 MARS 代理中，選擇 **"立即備份**"。

    ![立即在 Windows 伺服器中備份](./media/backup-configure-vault/backup-now.png)

1. 如果 MARS 代理版本為 2.0.9169.0 或較新，則可以設置自訂保留日期。 在"**保留備份直到"** 部分中，從日曆中選擇日期。

   ![使用日曆自訂保留日期](./media/backup-configure-vault/mars-ondemand.png)

1. 在 **"確認"** 頁上，查看設置，然後選擇 **"備份**"。
1. 選擇 **"關閉"** 以關閉嚮導。 如果在備份完成之前關閉嚮導，嚮導將繼續在後臺運行。

初始備份完成後，**作業完成**狀態將顯示在備份主控台中。

## <a name="set-up-on-demand-backup-policy-retention-behavior"></a>設置按需備份策略保留行為

> [!NOTE]
> 此資訊僅適用于早于 2.0.9169.0 的 MARS 代理版本。
>

| 備份計畫選項 | 資料保留期限
| -- | --
| Day | **預設保留**：相當於"每日備份的保留天數"。 <br/><br/> **例外情況**：如果設置為長期保留（數周、數月或數年）的每日計畫備份失敗，則在故障後立即觸發的按需備份將被視為長期保留。 否則，將考慮下一個計畫備份以進行長期保留。<br/><br/> **示例方案**：計畫在星期四上午 8：00 備份失敗。 此備份將考慮每週、每月或每年保留。 因此，在星期五上午 8：00 的下一次計畫備份之前觸發的第一個按需備份將自動標記為每週、每月或每年的保留。 此備份代替週四上午 8：00 備份。
| 週 | **預設保留**：一天。 對於具有每週備份策略的資料來源，為按需備份在第二天將被刪除。 即使它們是資料來源的最新備份，它們也會被刪除。 <br/><br/> **例外情況**：如果設置為長期保留（數周、數月或數年）的每週計畫備份失敗，則在故障後立即觸發的按需備份將被視為長期保留。 否則，將考慮下一個計畫備份以進行長期保留。 <br/><br/> **示例方案**：計畫在星期四上午 8：00 備份失敗。 此備份將考慮每月或每年保留。 因此，在星期四上午 8：00 的下一次計畫備份之前觸發的第一個按需備份將自動標記為每月或每年保留。 此備份代替週四上午 8：00 備份。

有關詳細資訊，請參閱[創建備份策略](#create-a-backup-policy)。

## <a name="next-steps"></a>後續步驟

* 瞭解如何在[Azure 中還原檔](backup-azure-restore-windows-server.md)。
* 查找[有關備份檔案和資料夾的常見問題](backup-azure-file-folder-backup-faq.md)

