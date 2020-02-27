---
title: Azure （大型實例）上 SAP Hana 的災難網站的 HANA 容錯移轉程式 |Microsoft Docs
description: 如何執行容錯移轉至 Azure （大型實例）上 SAP Hana 的嚴重損壞修復網站
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
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2020
ms.locfileid: "77617145"
---
# <a name="disaster-recovery-failover-procedure"></a>災害復原容錯移轉程序


>[!IMPORTANT]
>本文不是 SAP Hana 系統管理檔或 SAP Notes 的替代方案。 我們預期您對 SAP Hana 系統管理和作業有深刻的瞭解和專長，特別是針對備份、還原、高可用性和嚴重損壞修復（DR）。 在本文中，會顯示 SAP Hana Studio 的螢幕擷取畫面。 SAP 管理工具和工具本身的畫面內容、結構和性質，可能會因 SAP HANA 的版本而異。

當您故障切換到 DR 網站時，需要考慮兩個案例：

- 您需要將 SAP HANA 資料庫恢復到最新的資料狀態。 在此情況下，您可以使用自助腳本來執行容錯移轉，而不需要與 Microsoft 聯繫。 針對容錯回復，您需要與 Microsoft 合作。
- 您想要還原到不是最新複寫快照集的儲存體快照集。 在此情況下，您需要與 Microsoft 合作。 

>[!NOTE]
>必須在代表 DR 單位的 HANA 大型實例單位上執行下列步驟。 
 
若要還原至最新複寫的儲存體快照集，請遵循[azure 上 SAP Hana 的 Microsoft 快照集工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.2/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.2.1.pdf)中的 < 執行完整 DR 容錯移轉-azure_hana_dr_failover」中的步驟。 

如果您想要將多個 SAP Hana 實例故障處理，請執行 azure_hana_dr_failover 命令數次。 當要求時，請輸入您想要故障移交和還原的 SAP Hana SID。 


您也可以測試 DR 容錯移轉，而不會影響實際的複寫關聯性。 若要執行測試容錯移轉，請依照[azure 上 SAP Hana 的 Microsoft 快照集工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.2/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.2.1.pdf)中的 < 執行測試 DR 容錯移轉-azure_hana_test_dr_failover 中的步驟進行。 

>[!IMPORTANT]
>請勿透過**測試容錯移轉**的程式，在您于 DR 網站中建立的實例*上執行任何*生產交易。 命令 azure_hana_test_dr_failover 會建立與主要網站沒有關聯性的一組磁片區。 如此一來，「不」可能同步回主要站台。 

如果您想要測試多個 SAP Hana 實例，請執行腳本數次。 當要求時，請輸入您想要測試容錯移轉之實例的 SAP Hana SID。 

>[!NOTE]
>如果您需要故障切換到 DR 網站，以修復已刪除的幾小時之前的資料，而且需要將 DR 磁片區設定為較早的快照集，則適用此程式。 

1. 在您正在執行的 HANA 大型實例的嚴重損壞修復單位上關閉 HANA 的非生產實例。 已預先安裝休眠中的 HANA 生產實例。
1. 確定沒有任何 SAP HANA 程序處於執行狀態。 請使用下列命令進行這項檢查：

      `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`第 1 課：建立 Windows Azure 儲存體物件{2}。

      您應該會從輸出中看到 **hdbdaemon** 程序處於已停止狀態，而且已沒有其他 HANA 程序處於執行中或已啟動狀態。
1. 決定要讓災害復原網站還原到哪個快照集名稱或 SAP HANA 備份識別碼。 在真實的災害復原案例中，此快照集通常會是最新的快照集。 如果您需要復原遺失的資料，請挑選較早的快照集。
1. 透過高優先順序的支援要求連絡 Azure 支援。 在 DR 網站上，要求以快照集的名稱和日期或 HANA 備份識別碼來還原該快照集。 預設值是作業端只會還原 /hana/data 磁碟區。 如果您也想要擁有/hana/logbackups 的磁片區，您必須特別陳述。 請勿還原 /hana/shared 磁碟區。 相反地，在您重新掛接 PRD 的/hana/shared 磁片區之後，請從快照集目錄及其子目錄中選擇特定**的**檔案，例如 global.asa。 

   在作業端，系統將會進行下列步驟：

   a. 停止將快照集從生產磁碟區複寫至災害復原磁碟區。 如果生產網站中斷是您需要執行災害復原程序的原因，則可能已經發生中斷。
   
   b. 此儲存體快照集名稱或具有您所選備份識別碼的快照集會在災害復原磁碟區上還原。
   
   c. 還原後，災害復原磁碟區即可供掛接到災害復原區域中的 HANA 大型執行個體單位。
      
1. 將災害復原磁碟區掛接至災害復原網站中的 HANA 大型執行個體單位。 
1. 啟動休眠的 SAP HANA 生產執行個體。
1. 如果您選擇複製交易記錄備份記錄以降低 RPO 時間，請將交易記錄備份合併到新掛接的 DR/hana/logbackups 目錄中。 請勿覆寫現有的備份。 複製未使用儲存體快照集最新複寫複寫的較新備份。
1. 您也可以從不會複寫到 DR Azure 區域中/hana/shared/PRD 磁片區的快照集，還原單一檔案。

下列步驟示範如何根據所還原的儲存體快照集和可用的交易記錄備份來復原 SAP Hana 的生產實例。

1. 使用 SAP HANA Studio，將備份位置變更為 **/hana/logbackups**。

   ![變更 DR 復原的備份位置](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

1. SAP HANA 會掃描所有備份檔案位置，並建議可作為還原目的地的最新交易記錄備份。 掃描可能需要幾分鐘的時間，直到出現如下畫面：

   ![DR 復原的交易記錄備份清單](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

1. 調整某些預設設定：

      - 清除 [使用差異備份]。
      - 選取 [初始化記錄區域]。

   ![設定初始化記錄區域](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

1. 選取 [完成]。

   ![完成 DR 還原](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

此時應該會出現進度視窗，如下所示。 請記住，這是具有 3 節點相應放大 SAP HANA 組態的災害復原還原範例。

![還原進度](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

如果還原在 [**完成]** 畫面上停止回應，而且未顯示進度畫面，請確認背景工作節點上的所有 SAP Hana 實例都在執行中。 如有必要，請手動啟動 SAP HANA 執行個體。


## <a name="failback-from-a-dr-to-a-production-site"></a>從 DR 容錯回復到生產網站
您可以從 DR 容錯回復到生產網站。 讓我們看看以下情況：因為生產 Azure 區域發生問題而容錯移轉至災害復原網站，而不是因為您需要復原遺失的資料。 

您已在損毀修復網站中執行您的 SAP 生產工作負載一段時間。 由於生產網站中的問題已經解決，因此您想容錯回復到生產網站。 因為您不能遺失資料，所以退回生產網站的作業會涉及許多步驟，並且要與 SAP HANA on Azure 營運小組密切合作。 在問題獲得解決之後，由您決定是否觸發營運小組來開始反向同步處理到生產網站的程序。

請遵循下列步驟：

1. SAP HANA on Azure 營運小組取得觸發程序，以從災害復原存放磁碟區 (目前代表生產狀態) 同步處理生產存放磁碟區。 處於此狀態時，生產網站中的 HANA 大型執行個體單位會關閉。
1. Azure 作業小組上的 SAP Hana 會監視複寫，並確定在通知您之前，它已趕上。
1. 您會關閉使用災害復原網站中生產 HANA 執行個體的應用程式。 接著執行 HANA 交易記錄備份。 接下來，您會停止在嚴重損壞修復網站的 HANA 大型實例單位上執行的 HANA 實例。
1. 在嚴重損壞修復網站的 HANA 大型實例單位中執行的 HANA 實例關閉後，作業小組會再次手動同步處理磁片區。
1. Azure 作業小組的 SAP Hana 會再次啟動生產網站中的 HANA 大型實例單位。 他們會將它交給您。 在 HANA 大型實例單位的啟動時，請確定 SAP Hana 實例處於關閉狀態。
1. 您執行的資料庫還原步驟與先前損毀修復網站時所進行的相同。

## <a name="monitor-disaster-recovery-replication"></a>監視災害復原複寫

若要監視儲存體複寫進度的狀態，請執行腳本 `azure_hana_replication_status`。 此命令必須從嚴重損壞修復位置中執行的單位執行，才能如預期般運作。 無論複寫是否為作用中，此命令都可以運作。 您可以在嚴重損壞修復位置中，為租使用者的每個 HANA 大型實例單位執行命令。 它無法用來取得有關開機磁碟區的詳細資料。 

如需有關命令和其輸出的詳細資訊，請參閱[azure 上 SAP Hana 的 Microsoft 快照集工具中的](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.2/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.2.1.pdf)「取得 DR 複寫狀態-azure_hana_replication_status」。


## <a name="next-steps"></a>後續步驟
- 請參閱[從 HANA 端進行監視和疑難排解](hana-monitor-troubleshoot.md)。
