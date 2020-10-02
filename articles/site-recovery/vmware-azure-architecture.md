---
title: Azure Site Recovery 中的 VMware VM 嚴重損壞修復架構
description: 本文概述使用 Azure Site Recovery 來設定從內部部署 VMware VM 至 Azure 的災害復原時，所使用的元件和架構
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 11/06/2019
ms.author: raynew
ms.openlocfilehash: 45baee286fede0ab16da62b7c2e84008d58690b1
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2020
ms.locfileid: "91626491"
---
# <a name="vmware-to-azure-disaster-recovery-architecture"></a>VMware 至 Azure 災害復原架構

本文說明使用 [Azure Site Recovery](site-recovery-overview.md) 服務在內部部署 VMware 網站與 Azure 之間部署 VMware 虛擬機器 (VM) 的災害復原複寫、容錯移轉和復原時，所使用的架構和程序。


## <a name="architectural-components"></a>架構元件

下表和圖形提供將 VMware 災害復原至 Azure 時所用元件的高層級檢視。

**元件** | **需求** | **詳細資料**
--- | --- | ---
**Azure** | Azure 訂用帳戶、快取、受控磁片的 Azure 儲存體帳戶，以及 Azure 網路。 | 從內部部署 Vm 複寫的資料會儲存在 Azure 儲存體中。 從內部部署環境容錯移轉至 Azure 時，會以複寫的資料建立 Azure VM。 Azure VM 在建立後會連線到 Azure 虛擬網路。
**組態伺服器電腦** | 單一的內部部署電腦。 建議您將其當作可從下載的 OVF 範本部署的 VMware VM 來執行。<br/><br/> 此機器會執行所有的內部部署 Site Recovery 元件，包括組態伺服器、處理序伺服器和主要目標伺服器。 | **組態伺服器**：負責協調內部部署與 Azure 之間的通訊，以及管理資料複寫。<br/><br/> **處理序伺服器**：依預設會安裝在組態伺服器上。 負責接收複寫資料，以快取、壓縮和加密進行最佳化，然後將複寫資料傳送至 Azure 儲存體。 處理序伺服器也會在您要複寫的 VM 上安裝 Azure Site Recovery 行動服務，並自動探索內部部署機器。 隨著部署規模擴大，您可以新增額外的個別處理序伺服器，以處理日較大的複寫流量。<br/><br/> **主要目標伺服器**：預設會安裝在組態伺服器上。 負責處理從 Azure 進行容錯回復期間的複寫資料。 針對大型部署，您可以新增額外的個別主要目標伺服器進行容錯回復。
**VMware 伺服器** | VMware VMs 會裝載於內部部署 vSphere ESXi 伺服器。 我們建議以 vCenter 伺服器管理主機。 | 在 Site Recovery 部署期間，您可將 VMware 伺服器新增至復原服務保存庫。
**複寫的機器** | 行動服務會安裝在您複寫的每個 VMware VM 上。 | 建議您允許從處理序伺服器自動安裝。 或者，您可以手動安裝服務，或使用自動化的部署方法，例如 Configuration Manager。

![顯示 VMware 至 Azure 複寫架構關聯性的圖表。](./media/vmware-azure-architecture/arch-enhanced.png)

## <a name="set-up-outbound-network-connectivity"></a>設定輸出網路連線能力

如需 Site Recovery 如預期般運作，您必須修改輸出網路連線能力，以允許您的環境進行複寫。

> [!NOTE]
> Site Recovery 不支援使用驗證 Proxy 來控制網路連線能力。

### <a name="outbound-connectivity-for-urls"></a>URL 的輸出連線能力

如果您使用以 URL 為基礎的防火牆 Proxy 來控制輸出連線能力，請允許存取這些 URL：

| **名稱**                  | **商業**                               | **政府**                                 | **說明** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| 儲存體                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net`              | 允許將資料從 VM 寫入來源區域的快取儲存體帳戶中。 |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | 提供 Site Recovery 服務 URL 的授權和驗證。 |
| 複寫               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | 允許 VM 與 Site Recovery 服務進行通訊。 |
| 服務匯流排               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | 允許 VM 寫入 Site Recovery 監視和診斷資料。 |

如需要針對內部部署 Azure Site Recovery 基礎結構和 Azure 服務之間的通訊進行篩選的詳盡 Url 清單，請參閱 [必要條件文章中的網路需求一節](vmware-azure-deploy-configuration-server.md#prerequisites)。

## <a name="replication-process"></a>複寫程序

1. 當您啟用 VM 複寫時，首先會使用指定的複寫原則開始複寫至 Azure 儲存體。 請注意：
    - 如為 VMware VM，複寫程序為區塊層級、幾乎連續性，並會使用在 VM 執行的流動性服務代理程式。
    - 任何複寫原則設定均會套用：
        - **RPO 臨界值**。 此設定不會影響複寫。 其可協助進行監視。 如果目前的 RPO 超過您指定的閾值限制，則系統會引發事件並選擇性傳送電子郵件。
        - **復原點保留期**。 發生中斷時，此設定會指定您所希望回溯的時間。 進階儲存體中的保留期上限為 24 小時。 標準儲存體則為 72 小時。 
        - **應用程式一致快照集**。 視您的應用程式需求而定，您可以每隔1到12小時執行一次應用程式一致快照集。 快照集為標準的 Azure blob 快照集。 在 VM 執行的流動性代理程式會根據此設定要求 VSS 快照集，並在複寫串流中將該時間點標記為應用程式一致時間點。

2. 流量會透過網際網路複寫到 Azure 儲存體的公用端點。 或者，您可以使用 Azure ExpressRoute 搭配 [Microsoft 對等互連](../expressroute/expressroute-circuit-peerings.md#microsoftpeering)。 不支援從內部部署網站透過站對站虛擬私人網路 (VPN) 將流量複寫至 Azure。
3. 初始複寫作業可確保在啟用複寫時，電腦上的整個資料都會傳送至 Azure。 初始複寫完成之後，就會開始將差異變更複寫到 Azure。 機器的追蹤變更會傳送至流程伺服器。
4. 進行通訊的過程如下：

    - VM 會在輸入連接埠 HTTPS 443 上與內部部署設定伺服器進行通訊，以進行複寫管理。
    - 設定伺服器會透過輸出連接埠 HTTPS 443 與 Azure 協調複寫。
    - VM 會透過輸入連接埠 HTTPS 9443 將複寫資料傳送至處理伺服器 (在設定伺服器電腦上執行)。 您可以修改此連接埠。
    - 進程伺服器會接收復寫資料、將其優化並加密，然後透過埠443輸出將它傳送至 Azure 儲存體。
5. 複寫資料記錄會先進入 Azure 中的快取儲存體帳戶。 系統會處理這些記錄，並將資料儲存在 Azure 受控磁片中， (稱為 asr 植入磁片) 。 系統會在此磁片上建立復原點。

![此圖顯示 VMware 至 Azure 的複寫程式。](./media/vmware-azure-architecture/v2a-architecture-henry.png)

## <a name="resynchronization-process"></a>重新同步處理程序

1. 有時，在初始複寫期間或傳輸差異變更時，來源電腦與進程伺服器之間，或在進程伺服器與 Azure 之間可能會發生網路連線問題。 這兩種情況可能會導致資料傳輸至 Azure 失敗。
2. 為了避免資料完整性問題，並將資料傳輸成本降至最低，Site Recovery 會將機器標示為重新同步處理。
3. 在下列情況下，您也可以將機器標示為重新同步處理，以維持來源電腦與 Azure 中所儲存資料之間的一致性。
    - 如果電腦經歷強制關機
    - 如果電腦經歷 configurational 的變更，例如調整磁片大小 (將磁片大小從 2 TB 修改為 4 TB) 
4. 重新同步處理只會將差異資料傳送至 Azure。 在內部部署與 Azure 之間傳輸資料，方法是在來源電腦與 Azure 中儲存的資料之間計算總和檢查碼，藉以最小化。
5. 根據預設，重新同步處理會排程在上班時間以外的時間自動執行。 如果您不想等候預設外部重新同步處理時數，您可以手動重新同步處理 VM。 若要這樣做，請移至 Azure 入口網站，選取 VM > 重新 **同步**處理。
6. 如果預設重新同步處理在辦公時間以外失敗，而且需要手動介入，則 Azure 入口網站中的特定電腦上會產生錯誤。 您可以解決此錯誤，並手動觸發重新同步處理。
7. 重新同步處理完成之後，差異變更的複寫將會繼續。

## <a name="replication-policy"></a>複寫原則 

當您啟用 Azure VM 複寫時，Site Recovery 依預設會以下表中摘要說明的預設設定建立新的複寫原則。

**原則設定** | **詳細資料** | **預設值**
--- | --- | ---
**復原點保留期** | 指定 Site Recovery 會保留復原點多久 | 24 小時
**應用程式一致的快照頻率** | Site Recovery 建立應用程式一致快照集的頻率。 | 每四小時

### <a name="managing-replication-policies"></a>管理複寫原則

您可以管理和修改預設複寫原則設定，如下所示：
- 您可以在啟用複寫時修改這些設定。
- 您可以隨時建立複寫原則，然後在啟用複寫時加以套用。

### <a name="multi-vm-consistency"></a>多 VM 一致性

如果您要一起複寫多個 VM，並且在容錯移轉時有共用的絕對一致和應用程式一致的復原點，您可以將這些 VM 聚集到一個複寫群組中。 啟用多 VM 一致性會影響工作負載效能，建議只有在所有執行工作負載的 VM 間需保有一致性時，才將其用於這些機器。 



## <a name="snapshots-and-recovery-points"></a>快照集和復原點

復原點可從您在特定點時間取得的 VM 磁碟快照集建立。 在進行 VM 的容錯移轉時，您可以使用復原點在目標位置還原 VM。

進行容錯移轉時，我們通常會想要確定 VM 在啟動時不會損毀或遺失資料，且 VM 資料在作業系統上和執行於 VM 的應用程式間均保有一致性。 這取決於您所建立的快照集類型。

Site Recovery 會依照下列方式建立快照集：

1. Site Recovery 依預設會為資料建立絕對一致的快照集，而如果您為其指定了頻率，則建立應用程式一致的快照集。
2. 復原點可從快照集建立，並根據複寫原則中的保留設定進行儲存。

### <a name="consistency"></a>一致性

下表說明不同類型的一致性。

### <a name="crash-consistent"></a>絕對一致

**說明** | **詳細資料** | **建議**
--- | --- | ---
絕對一致的快照集會擷取在快照建立時位於磁碟上的資料。 其中不含記憶體中的任何資料。<br/><br/> 其中所含的資料，相當於設若在建立快照集時發生 VM 當機的狀況，或從伺服器上拔開電源線，所將存在於磁碟上的資料。<br/><br/> 「絕對一致」並不保證資料在作業系統上或 VM 的應用程式間可保有一致性。 | Site Recovery 依預設會每五分鐘建立一次絕對一致復原點。 此設定無法修改。<br/><br/>  | 現在，大部分的應用程式都可以從絕對一致復原點妥善復原。<br/><br/> 對作業系統的複寫，以及 DHCP 伺服器和列印伺服器之類的應用程式而言，使用絕對一致復原點通常就已足夠。

### <a name="app-consistent"></a>應用程式一致

**說明** | **詳細資料** | **建議**
--- | --- | ---
應用程式一致復原點可從應用程式一致快照集建立。<br/><br/> 應用程式一致的快照集包含絕對一致快照集中的所有資訊，以及記憶體和進行中的交易所包含的所有資料。 | 應用程式一致快照集會使用磁碟區陰影複製服務 (VSS)：<br/><br/>   1) Azure Site Recovery 使用「僅複本備份」 (VSS_BT_COPY) 不會變更 Microsoft SQL 交易記錄備份時間和序號的方法 </br></br> 2) 起始快照集時，VSS 會在磁片區上執行寫入時複製 (牛) 作業。<br/><br/>   3) 執行牛之前，VSS 會通知電腦上的每個應用程式，它需要將其記憶體常駐資料排清到磁片。<br/><br/>   4) VSS 接著會在此情況下允許備份/嚴重損壞修復應用程式 (Site Recovery) 讀取快照集資料並繼續進行。 | 應用程式一致快照集會依據您指定的頻率建立。 此頻率一律應小於您的保留復原點設定。 例如，如果您使用預設設定 24 小時來保留復原點，您所設定的頻率就應該小於 24 小時。<br/><br/>此類快照集比絕對一致快照集更複雜，且需要較長的時間才能完成。<br/><br/> 在 VM 上執行且已啟用複寫的應用程式，效能將會受其影響。 

## <a name="failover-and-failback-process"></a>容錯移轉和容錯回復程序

設定複寫並執行災害復原演練 (測試容錯移轉) 來檢查一切如預期般運作之後，您可以視需要執行容錯移轉和容錯回復。

1. 您可以容錯移轉單一電腦，或建立復原方案來同時容錯移轉多個 VM。 與單一電腦容錯移轉相比，復原方案的優點包括	：
    - 您可以藉由將應用程式的所有 VM 都納入單一復原方案中，來建立應用程式相依性模型。
    - 您可以新增指令碼、Azure Runbook 及暫停來進行手動動作。
2. 觸發初始容錯移轉之後，您要認可讓它開始從 Azure VM 存取工作負載。
3. 當主要內部部署網站恢復可用狀態時，您就可以準備容錯回復。 若要進行容錯回復，您必須設定容錯回復基礎結構，包括：

    * **Azure 中的暫時處理序伺服器**︰若要從 Azure 容錯回復，您可以設定 Azure VM 作為處理序伺服器，以處理來自 Azure 的複寫。 容錯回復完成後，您可以刪除此 VM。
    * **VPN 連線**：若要容錯回復，您必須要有從 Azure 網路到內部部署網站的 VPN 連線 (或 ExpressRoute)。
    * **個別的主要目標伺服器**︰根據預設，隨組態伺服器安裝的主要目標伺服器 (在內部部署 VMware VM 上) 會處理容錯回復。 如果您需要容錯回復大量流量，請針對此用途設定個別的內部部署主要目標伺服器。
    * **容錯回復原則**︰若要複寫回到內部部署網站，您需要容錯回復原則。 當您建立從內部部署至 Azure 的複寫原則後，此原則會自動建立。
4. 備妥元件之後，容錯回復會分三個階段進行：

    - 第 1 階段：重新保護 Azure VM，使其能從 Azure 複寫回到內部部署 VMware VM。
    -  第 2 階段：執行容錯移轉至內部部署網站。
    - 第 3 階段：工作負載進行容錯回復之後，您必須重新啟用內部部署 VM 的複寫。
    
 

![此圖顯示從 Azure 到 VMware 的容錯回復。](./media/vmware-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>後續步驟

請遵循[此教學課程](vmware-azure-tutorial.md)，以啟用 VMware 至 Azure 的複寫。
