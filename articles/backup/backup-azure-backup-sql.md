---
title: 將 SQL Server 備份至 Azure 做為 DPM 工作負載
description: 使用 Azure 備份服務備份 SQL Server 資料庫的簡介
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 8cbb8c833bc2933afac300bcc848fd50861011d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77505927"
---
# <a name="back-up-sql-server-to-azure-as-a-dpm-workload"></a>將 SQL Server 備份至 Azure 做為 DPM 工作負載

本文將引導您完成使用 Azure 備份備份 SQL Server 資料庫的配置步驟。

若要將 SQL Server 資料庫備份至 Azure，您需要 Azure 帳戶。 如果沒有，只需幾分鐘即可創建免費帳戶。 有關詳細資訊，請參閱創建[Azure 免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。

要將 SQL Server 資料庫備份到 Azure 並從 Azure 恢復它，需要執行：

1. 創建備份策略以保護 Azure 中的 SQL Server 資料庫。
1. 在 Azure 中創建按需備份副本。
1. 從 Azure 復原資料庫。

## <a name="before-you-start"></a>開始之前

開始之前，請確保滿足使用 Azure 備份保護工作負荷[的先決條件](backup-azure-dpm-introduction.md#prerequisites-and-limitations)。 下面是一些先決條件任務： 
* 創建備份保存庫。
* 下載保存庫憑據。 
* 安裝 Azure 備份代理。
* 將伺服器註冊到保存庫。

## <a name="create-a-backup-policy"></a>建立備份原則  

要保護 Azure 中的 SQL Server 資料庫，請先創建備份策略：

1. 在資料保護管理器 （DPM） 伺服器上，選擇 **"保護**工作區"。
1. 選擇 **"新建"** 以創建保護組。

    ![建立保護群組](./media/backup-azure-backup-sql/protection-group.png)
1. 在起始頁上，查看有關創建保護組的指導。 然後選擇 **"下一步**"。
1. 選擇**伺服器**。

    ![選擇伺服器保護組類型](./media/backup-azure-backup-sql/pg-servers.png)
1. 展開要備份的資料庫所在的 SQL Server 電腦。 您將看到可以從該伺服器備份的資料來源。 展開**所有 SQL 共用**，然後選擇要備份的資料庫。 在此示例中，我們選擇報表伺服器$MSDPM2012 和報表伺服器$MSDPM2012TempDB。 然後選擇 **"下一步**"。

    ![選擇 SQL Server 資料庫](./media/backup-azure-backup-sql/pg-databases.png)
1. 命名保護組，然後選擇 **"我想要連線保護**"。

    ![選擇資料保護方法 - 短期磁片保護或線上 Azure 保護](./media/backup-azure-backup-sql/pg-name.png)
1. 在 **"指定短期目標"** 頁上，包括創建磁片備份點所需的輸入。

    在此示例中，**保留範圍**設置為*5 天*。 備份**同步頻率**設置為每*15 分鐘*一次。 **快速完整備份**設置為晚上*8：00。*

    ![設置備份保護的短期目標](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > 在此示例中，每天晚上 8：00 創建一個備份點。 已修改的資料，因為前一天的晚上 8：00 備份點已傳輸。 這個程序稱為 [快速完整備份] ****。 儘管事務日誌每 15 分鐘同步一次，但如果我們需要在晚上 9：00 恢復資料庫，則通過重播最後一個快速完整備份點的日誌來創建該點，在此示例中為 8：00 PM。
   >
   >

1. 選取 [下一步]****。 DPM 顯示可用的總體存儲空間。 它還顯示了潛在的磁碟空間利用率。

    ![設置磁片分配](./media/backup-azure-backup-sql/pg-storage.png)

    預設情況下，DPM 每個資料來源（SQL Server 資料庫）創建一個卷。 該卷用於初始備份副本。 在此配置中，邏輯磁片管理器 （LDM） 將 DPM 保護限制為 300 個數據源（SQL Server 資料庫）。 若要因應這項限制，請選取 [將資料共置在 DPM 存放集區中]****。 如果使用此選項，DPM 會對多個資料來源使用單個卷。 此設置允許 DPM 保護多達 2，000 個 SQL Server 資料庫。

    如果選擇 **"自動增長卷**"，則 DPM 可以隨著生產資料的增長考慮增加的備份量。 如果不選擇 **"自動增長卷**"，則 DPM 將備份存儲限制為保護組中的資料來源。

1. 如果您是管理員，您可以選擇**通過網路自動**傳輸此初始備份，並選擇傳輸時間。 或者選擇**手動**傳輸備份。 然後選擇 **"下一步**"。

    ![選擇複本建立方法](./media/backup-azure-backup-sql/pg-manual.png)

    初始備份副本需要傳輸整個資料來源（SQL Server 資料庫）。 備份資料從生產伺服器（SQL Server 電腦）移動到 DPM 服務器。 如果此備份較大，則通過網路傳輸資料可能會導致頻寬擁塞。 因此，管理員可以選擇使用卸除式媒體**手動**傳輸初始備份。 或者，他們可以在指定時間**通過網路自動**傳輸資料。

    初始備份完成後，備份在初始備份副本上以增量方式繼續。 增量備份通常都非常小，因此有利於透過網路傳輸。
    
1. 選擇何時運行一致性檢查。 然後選擇 **"下一步**"。

    ![選擇何時運行一致性檢查](./media/backup-azure-backup-sql/pg-consistent.png)

    DPM 可以對備份點的完整性運行一致性檢查。 它計算生產伺服器上的備份檔案的校驗和（本示例中的 SQL Server 電腦）和 DPM 中該檔的備份資料。 如果檢查發現衝突，則假定 DPM 中的備份檔案已損壞。 DPM 通過發送對應于校驗和不匹配的塊來修復備份的資料。 由於一致性檢查是一項性能密集型操作，因此管理員可以選擇計畫一致性檢查或自動運行一致性檢查。

1. 選擇要在 Azure 中保護的資料來源。 然後選擇 **"下一步**"。

    ![選擇要在 Azure 中保護的資料來源](./media/backup-azure-backup-sql/pg-sqldatabases.png)
1. 如果您是管理員，則可以選擇適合組織策略的備份計畫和保留原則。

    ![選擇計畫和保留原則](./media/backup-azure-backup-sql/pg-schedule.png)

    在此示例中，備份每天在 12：00 PM 和 8：00 PM 進行。

    > [!TIP]
    > 要快速恢復，請保留一些短期復原點。 這些復原點可用於操作復原。 Azure 充當良好的異地位置，提供更高的 SL 和保證的可用性。
    >
    > 使用 DPM 在本地磁片備份完成後安排 Azure 備份。 遵循這種做法後，最新的磁片備份將複製到 Azure。
    >

1. 選擇保留原則排程。 有關保留原則的工作原理的詳細資訊，請參閱使用 Azure[備份替換磁帶基礎結構](backup-azure-backup-cloud-as-tape.md)。

    ![選擇保留原則](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    在此範例中：

    * 備份每天在中午 12：00 PM 和 8：00 PM 進行。 他們被保存了180天
    * 週六下午 12：00 的備份將保留 104 周。
    * 每月最後一個星期六的備份在中午 12：00 保留 60 個月。
    * 從3月的最後一個星期六下午12：00備份保存10年。
    
    選擇保留原則後，選擇 **"下一步**"。

1. 選擇如何將初始備份副本傳輸到 Azure。

    * **"通過網路自動"** 選項遵循備份計畫將資料傳輸到 Azure。
    * 有關**離線備份**的詳細資訊，請參閱[離線備份概述](offline-backup-overview.md)。

    選擇傳輸機制後，選擇 **"下一步**"。

1. 在 **"摘要"** 頁上，查看策略詳細資訊。 然後選擇 **"創建組**"。 您可以在 **"監視"** 工作區中選擇 **"關閉**"並監視作業進度。

    ![保護組創建的進展](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="create-on-demand-backup-copies-of-a-sql-server-database"></a>創建 SQL Server 資料庫的按需備份副本

發生第一次備份時，將創建一個復原點。 您可以手動觸發復原點的創建，而不是等待計畫運行：

1. 在保護組中，請確保資料庫狀態**正常**。

    ![顯示資料庫狀態的保護組](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
1. 按右鍵資料庫，然後選擇 **"創建復原點**"。

    ![選擇創建連線復原點](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
1. 在下拉式功能表中，選擇 **"連線保護**"。 然後選擇 **"確定"** 以開始在 Azure 中創建復原點。

    ![開始在 Azure 中創建復原點](./media/backup-azure-backup-sql/sqlbackup-azure.png)
1. 您可以在 **"監視"** 工作區中查看作業進度。

    ![在監視主控台中查看作業進度](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>從 Azure 復原 SQL Server 資料庫

要從 Azure 恢復受保護的實體（如 SQL Server 資料庫）：：

1. 打開 DPM 服務器管理主控台。 轉到**恢復**工作區以查看 DPM 備份的伺服器。 選擇資料庫（在此示例中，報表伺服器$MSDPM2012）。 選擇以**連線**結尾**的恢復時間**。

    ![選取復原點](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
1. 按右鍵資料庫名稱並選擇 **"恢復**"。

    ![從 Azure 恢復資料庫](./media/backup-azure-backup-sql/sqlbackup-recover.png)
1. DPM 會顯示復原點的詳細資料。 選取 [下一步]****。 若要覆寫資料庫，請選取復原類型 [復原到原始的 SQL Server 執行個體] ****。 然後選擇 **"下一步**"。

    ![將資料庫恢復到其原始位置](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    在此示例中，DPM 允許將資料庫恢復到另一個 SQL Server 實例或獨立網路資料夾。
1. 在 **"指定恢復選項"** 頁上，您可以選擇恢復選項。 例如，您可以選擇**網路頻寬使用限制**以限制恢復使用的頻寬。 然後選擇 **"下一步**"。
1. 在 **"摘要"** 頁上，您將看到當前恢復配置。 選擇 **"恢復**"。

    恢復狀態顯示正在恢復的資料庫。 您可以選擇 **"關閉"** 以關閉嚮導，並在 **"監視"** 工作區中查看進度。

    ![啟動恢復過程](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    恢復完成後，還原的資料庫與應用程式一致。

## <a name="next-steps"></a>後續步驟

有關詳細資訊，請參閱[Azure 備份常見問題解答](backup-azure-backup-faq.md)。
