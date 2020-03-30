---
title: 在 Azure（大型實例）上，SAP HANA 的容錯移轉過程到災難網站 |微軟文檔
description: 如何在 Azure 上對 SAP HANA 的災害復原網站執行容錯移轉（大型實例）
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/22/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3fe3ee79318ab9fdc9f2c0e9585051439b76b5cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617145"
---
# <a name="disaster-recovery-failover-procedure"></a>災害復原容錯移轉程序


>[!IMPORTANT]
>本文不能替代 SAP HANA 管理文檔或 SAP 說明。 我們希望您在 SAP HANA 管理和操作方面有扎實的瞭解和專業知識，尤其是在備份、恢復、高可用性和災害復原 （DR） 方面。 在本文中，將顯示 SAP HANA 工作室的螢幕截圖。 SAP 管理工具和工具本身的畫面內容、結構和性質，可能會因 SAP HANA 的版本而異。

容錯移轉到 DR 網站時需要考慮兩種情況：

- 您需要將 SAP HANA 資料庫恢復到最新的資料狀態。 在這種情況下，有一個自助服務腳本，您可以使用該腳本執行容錯移轉，而無需與 Microsoft 聯繫。 對於故障恢復，您需要與 Microsoft 合作。
- 您希望還原到不是最新複製快照的存儲快照。 在此情況下，您需要與 Microsoft 合作。 

>[!NOTE]
>必須在表示 DR 單元的 HANA 大型實例單元上執行以下步驟。 
 
要還原到最新的複製存儲快照，請按照 Microsoft 快照工具中為[Azure 上的 SAP HANA](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.2/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.2.1.pdf)執行"完全 DR 容錯移轉 - azure_hana_dr_failover"中的步驟。 

如果要使多個 SAP HANA 實例失敗，則多次運行azure_hana_dr_failover命令。 請求時，輸入要容錯移轉和恢復的 SAP HANA SID。 


您還可以在不影響實際複製關係的情況下測試 DR 容錯移轉。 要執行測試容錯移轉，請按照[在 Azure 上為 SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.2/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.2.1.pdf)中的"執行測試 DR 容錯移轉 - azure_hana_test_dr_failover"中的步驟操作。 

>[!IMPORTANT]
>*不要*通過**測試容錯移轉**在 DR 網站中創建的實例上運行任何生產事務。 命令azure_hana_test_dr_failover創建一組與主網站沒有關系的卷。 如此一來，「不」** 可能同步回主要站台。 

如果要測試多個 SAP HANA 實例，則多次運行腳本。 請求時，輸入要測試容錯移轉的實例的 SAP HANA SID。 

>[!NOTE]
>如果需要容錯移轉到 DR 網站以拯救幾小時前刪除並需要將 DR 卷設置為較早的快照的一些資料，則此過程適用。 

1. 關閉正在運行的 HANA 大型實例的災害復原單元上的 HANA 非生產實例。 預裝休眠的 HANA 生產實例。
1. 確定沒有任何 SAP HANA 程序處於執行狀態。 使用此檢查使用以下命令：

      `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`.

      您應該會從輸出中看到 **hdbdaemon** 程序處於已停止狀態，而且已沒有其他 HANA 程序處於執行中或已啟動狀態。
1. 決定要讓災害復原網站還原到哪個快照集名稱或 SAP HANA 備份識別碼。 在真實的災害復原案例中，此快照集通常會是最新的快照集。 如果您需要復原遺失的資料，請挑選較早的快照集。
1. 透過高優先順序的支援要求連絡 Azure 支援。 請求還原該快照，並還原快照的名稱和日期或 DR 網站上的 HANA 備份 ID。 預設值是作業端只會還原 /hana/data 磁碟區。 如果也希望有 /hana/log 備份卷，則需要專門說明這一點。 請勿還原 /hana/shared 磁碟區。** 相反，在重新裝入 PRD 的 /hana/shared 卷後，從 **.snapshot**目錄及其子目錄中選擇特定檔，如 global.ini。 

   在作業端，系統將會進行下列步驟：

   a. 停止將快照集從生產磁碟區複寫至災害復原磁碟區。 如果生產網站中斷是您需要執行災害復原程序的原因，則可能已經發生中斷。
   
   b. 此儲存體快照集名稱或具有您所選備份識別碼的快照集會在災害復原磁碟區上還原。
   
   c. 還原後，災害復原磁碟區即可供掛接到災害復原區域中的 HANA 大型執行個體單位。
      
1. 將災害復原磁碟區掛接至災害復原網站中的 HANA 大型執行個體單位。 
1. 啟動休眠的 SAP HANA 生產執行個體。
1. 如果選擇複製事務記錄備份日誌以減少 RPO 時間，請將事務記錄備份合併到新安裝的 DR/hana/記錄備份目錄中。 請勿覆寫現有的備份。 複製未使用存儲快照的最新複製複製的較新的備份。
1. 您還可以從未複製到 DR Azure 區域中的 /hana/shared/PRD 卷的快照中還原單個檔。

以下步驟演示如何基於還原的存儲快照和可用的事務記錄備份恢復 SAP HANA 生產實例。

1. 使用 SAP HANA Studio，將備份位置變更為 **/hana/logbackups**。

   ![變更 DR 復原的備份位置](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

1. SAP HANA 會掃描所有備份檔案位置，並建議可作為還原目的地的最新交易記錄備份。 掃描可能需要幾分鐘時間，直到出現如下所示的螢幕：

   ![用於 DR 恢復的事務記錄備份清單](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

1. 調整某些預設設定：

      - 清除 [使用差異備份]****。
      - 選取 [初始化記錄區域]****。

   ![設定初始化記錄區域](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

1. 選取 [完成]****。

   ![完成 DR 還原](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

此時應該會出現進度視窗，如下所示。 請記住，這是具有 3 節點相應放大 SAP HANA 組態的災害復原還原範例。

![還原進度](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

如果還原停止在 **"完成"** 螢幕回應，並且未顯示進度螢幕，請確認輔助節點上的所有 SAP HANA 實例都在運行。 如有必要，請手動啟動 SAP HANA 執行個體。


## <a name="failback-from-a-dr-to-a-production-site"></a>從 DR 容錯回復到生產網站
您可以從 DR 容錯回復到生產網站。 讓我們看看以下情況：因為生產 Azure 區域發生問題而容錯移轉至災害復原網站，而不是因為您需要復原遺失的資料。 

您在災害復原網站中運行 SAP 生產工作負載已有一段時間了。 由於生產網站中的問題已經解決，因此您想容錯回復到生產網站。 因為您不能遺失資料，所以退回生產網站的作業會涉及許多步驟，並且要與 SAP HANA on Azure 營運小組密切合作。 在問題獲得解決之後，由您決定是否觸發營運小組來開始反向同步處理到生產網站的程序。

請遵循下列步驟：

1. SAP HANA on Azure 營運小組取得觸發程序，以從災害復原存放磁碟區 (目前代表生產狀態) 同步處理生產存放磁碟區。 處於此狀態時，生產網站中的 HANA 大型執行個體單位會關閉。
1. Azure 操作團隊的 SAP HANA 監視複製，並確保在它們通知您之前將其捕獲。
1. 您會關閉使用災害復原網站中生產 HANA 執行個體的應用程式。 接著執行 HANA 交易記錄備份。 接下來，您停止在災害復原網站的 HANA 大型實例單元上運行的 HANA 實例。
1. 關閉災害復原網站中 HANA 大型實例單元中的 HANA 實例後，操作團隊將再次手動同步磁片卷。
1. Azure 操作團隊的 SAP HANA 在生產網站中再次啟動 HANA 大型實例單元。 他們把它交給你 在 HANA 大型實例單元的啟動時間，請確保 SAP HANA 實例處於關閉狀態。
1. 執行與以前容錯移轉到災害復原網站時相同的資料庫還原步驟。

## <a name="monitor-disaster-recovery-replication"></a>監視災害復原複寫

要監視存儲複製進度的狀態，請運行腳本`azure_hana_replication_status`。 此命令必須從在災害復原位置運行的單位運行，才能按預期運行。 無論複製是否處於活動狀態，該命令都有效。 可以為災害復原位置的租戶的每個 HANA 大型實例單元運行該命令。 它不能用於獲取有關引導卷的詳細資訊。 

有關命令及其輸出的詳細資訊，請參閱[在 Azure 上的 SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.2/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.2.1.pdf)中"獲取 DR 複製狀態 - azure_hana_replication_status"。


## <a name="next-steps"></a>後續步驟
- 請參閱[從 HANA 側進行監視和故障排除](hana-monitor-troubleshoot.md)。
