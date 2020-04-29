---
title: Azure Site Recovery 中的 azure 至 Azure 嚴重損壞修復架構
description: 當您使用 Azure Site Recovery 服務，在 azure Vm 的 Azure 區域之間設定嚴重損壞修復時，所使用的架構總覽。
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 3/13/2020
ms.author: raynew
ms.openlocfilehash: 94da1639b5398a03b36fba3ff88877468a97ec36
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80294107"
---
# <a name="azure-to-azure-disaster-recovery-architecture"></a>Azure 至 Azure 災害復原架構


本文說明您在使用 [Azure Site Recovery](site-recovery-overview.md) 服務部署 Azure 虛擬機器 (VM) 的災害復原時所使用的架構、元件和程序。 設定災害復原後，Azure VM 即可在不同的區域之間持續複寫。 如果發生中斷狀況，您可以將 VM 容錯移轉至次要區域，並從該處加以存取。 當一切都恢復正常運作後，您即可進行容錯回復，並繼續在主要位置工作。



## <a name="architectural-components"></a>架構元件

下表摘要說明 Azure VM 的災害復原所使用的元件。

**元件** | **Requirements**
--- | ---
**來源區域中的 VM** | [支援的來源區域](azure-to-azure-support-matrix.md#region-support)中的一或多個 Azure VM。<br/><br/> VM 可執行任何[支援的作業系統](azure-to-azure-support-matrix.md#replicated-machine-operating-systems)。
**來源 VM 儲存體** | Azure VM 可以是受控的，或具有分散於不同儲存體帳戶間的非受控磁碟。<br/><br/>[深入了解](azure-to-azure-support-matrix.md#replicated-machines---storage)支援的 Azure 儲存體。
**來源 VM 網路** | VM 可在來源區域中位於虛擬網路 (VNet) 內的一或多個子網路上。 [深入了解](azure-to-azure-support-matrix.md#replicated-machines---networking)網路需求。
**快取儲存體帳戶** | 您必須要有位於來源網路中的快取儲存體帳戶。 在複寫期間，VM 變更會先儲存在快取中，再傳送至目標儲存體。  快取儲存體帳戶必須是標準。<br/><br/> 使用快取可確保在 VM 上執行的生產應用程式所受到的影響會降到最低。<br/><br/> [深入了解](azure-to-azure-support-matrix.md#cache-storage)快取儲存體需求。 
**目標資源** | 在複寫期間和執行容錯移轉時，都會使用目標資源。 Site Recovery 可依預設進行目標資源設定，您也可自行加以建立/自訂。<br/><br/> 在目標區域中，請確認您能夠建立 VM，且您的訂用帳戶有足夠的資源可支援目標區域中所需的 VM 大小。 

![來源和目標複寫](./media/concepts-azure-to-azure-architecture/enable-replication-step-1.png)

## <a name="target-resources"></a>目標資源

當您為 VM 啟用複寫時，Site Recovery 會為您提供自動建立目標資源的選項。 

**目標資源** | **預設設定**
--- | ---
**目標訂用帳戶** | 與來源訂用帳戶相同。
**目標資源群組** | VM 在容錯移轉之後所屬的資源群組。<br/><br/> 此群組可位於來源區域以外的任何 Azure 區域中。<br/><br/> Site Recovery 會在目標區域中建立具有 "asr" 尾碼的新資源群組。<br/><br/>
**目標 VNet** | 複寫的 VM 在容錯移轉之後所在的虛擬網路 (VNet)。 在來源和目標的虛擬網路之間會建立網路對應，反之亦然。<br/><br/> Site Recovery 會建立具有 "asr" 尾碼的新 VNet 和子網路。
**目標儲存體帳戶** |  如果 VM 未使用受控磁碟，這將是資料複寫到的儲存體帳戶。<br/><br/> Site Recovery 會在目標區域中建立新的儲存體帳戶，以反映來源儲存體帳戶。
**複本受控磁碟** | 如果 VM 使用受控磁碟，這將是資料複寫到的受控磁碟。<br/><br/> Site Recovery 會在儲存體區域中建立複本受控磁碟，以反映來源。
**目標可用性設定組** |  複寫的 VM 在容錯移轉之後所在的可用性設定組。<br/><br/> Site Recovery 會針對來源位置中的可用性設定組所包含的 VM，在目標區域中建立具有 "asr" 尾碼的可用性設定組。 如果有可用性設定組存在，則會加以使用，而不建立新的。
**目標可用性區域** | 如果目標區域支援可用性區域，Site Recovery 就會指派在來源區域中使用的相同區域編號。

### <a name="managing-target-resources"></a>管理目標資源

您可以依照下列方式管理目標資源：

- 您可以在啟用複寫時修改目標設定。
- 您可以在複寫已正常運作後修改目標設定。 例外狀況是可用性類型 (單一執行個體、設定組或區域)。 若要變更此設定，您必須停用複寫、修改設定，然後再重新啟用。



## <a name="replication-policy"></a>複寫原則 

當您啟用 Azure VM 複寫時，Site Recovery 依預設會以下表中摘要說明的預設設定建立新的複寫原則。

**原則設定** | **詳細資料** | **預設值**
--- | --- | ---
**復原點保留期** | 指定 Site Recovery 會保留復原點多久 | 24 小時
**應用程式一致的快照頻率** | Site Recovery 建立應用程式一致快照集的頻率。 | 每四個小時

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
應用程式一致復原點可從應用程式一致快照集建立。<br/><br/> 應用程式一致的快照集包含絕對一致快照集中的所有資訊，以及記憶體和進行中的交易所包含的所有資料。 | 應用程式一致快照集會使用磁碟區陰影複製服務 (VSS)：<br/><br/>   1) 在起始快照集時，VSS 會在磁碟區上執行「寫入時複製」(COW) 作業。<br/><br/>   2) 在執行 COW 之前，VSS 會通知機器上的每個應用程式它需要將其記憶體常駐資料排清到磁碟。<br/><br/>   3) 接著，VSS 會允許備份/災害復原應用程式 (在此案例中為 Site Recovery) 讀取快照集資料並繼續執行。 | 應用程式一致快照集會依據您指定的頻率建立。 此頻率一律應小於您的保留復原點設定。 例如，如果您使用預設設定 24 小時來保留復原點，您所設定的頻率就應該小於 24 小時。<br/><br/>此類快照集比絕對一致快照集更複雜，且需要較長的時間才能完成。<br/><br/> 在 VM 上執行且已啟用複寫的應用程式，效能將會受其影響。 

## <a name="replication-process"></a>複寫程序

當您啟用 Azure VM 的複寫時，將會發生下列情況：

1. 在 VM 上自動安裝 Site Recovery 行動服務擴充功能。
2. 此擴充功能會對 Site Recovery 註冊 VM。
3. 開始為 VM 進行連續複寫。  磁碟寫入會立即傳送到來源位置的快取儲存體帳戶。
4. Site Recovery 會處理快取中的資料，並將資料傳送到目標儲存體帳戶或複本受控磁碟。
5. 資料處理完成後，會每五分鐘產生一次絕對一致復原點。 此外匯根據複寫原則中指定的設定產生應用程式一致復原點。

![啟用複寫程序，步驟 2](./media/concepts-azure-to-azure-architecture/enable-replication-step-2.png)

**複寫程序**

## <a name="connectivity-requirements"></a>連線能力需求

 您複寫的 Azure VM 需要輸出連線能力。 Site Recovery 永遠不會需要 VM 的輸入連線能力。 

### <a name="outbound-connectivity-urls"></a>輸出連線能力 (URL)

如果使用 URL 來控制 VM 的輸出存取，請允許這些 URL。

| **URL** | **詳細資料** |
| ------- | ----------- |
| *.blob.core.windows.net | 允許將資料從 VM 寫入來源區域的快取儲存體帳戶中。 |
| login.microsoftonline.com | 提供 Site Recovery 服務 URL 的授權和驗證。 |
| *.hypervrecoverymanager.windowsazure.com | 允許 VM 與 Site Recovery 服務進行通訊。 |
| *.servicebus.windows.net | 允許 VM 寫入 Site Recovery 監視和診斷資料。 |
| *.vault.azure.net | 允許存取透過入口網站啟用已啟用 ADE 之虛擬機器的複寫
| *. automation.ext.azure.com | 允許透過入口網站為複寫的專案啟用自動升級行動代理程式

### <a name="outbound-connectivity-for-ip-address-ranges"></a>IP 位址範圍的輸出連線能力

若要使用 IP 位址控制 VM 的輸出連線能力，請允許這些位址。
請注意，網路連接需求的詳細資料可在[網路技術白皮書](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)中找到 

#### <a name="source-region-rules"></a>來源區域規則

**規則** |  **詳細資料** | **服務標記**
--- | --- | --- 
允許 HTTPS 輸出：連接埠 443 | 允許對應至來源區域儲存體帳戶的範圍 | 容量.\<區功能變數名稱稱>
允許 HTTPS 輸出：連接埠 443 | 允許對應至 Azure Active Directory 的範圍（Azure AD）  | AzureActiveDirectory
允許 HTTPS 輸出：連接埠 443 | 允許對應至目的地區域中事件中樞的範圍。 | EventsHub.\<區功能變數名稱稱>
允許 HTTPS 輸出：連接埠 443 | 允許對應至 Azure Site Recovery 的範圍  | AzureSiteRecovery
允許 HTTPS 輸出：連接埠 443 | 允許對應至 Azure Key Vault 的範圍（這只有在啟用啟用 ADE 的虛擬機器透過入口網站複寫時才需要） | AzureKeyVault
允許 HTTPS 輸出：連接埠 443 | 允許對應至 Azure 自動化控制器的範圍（只有針對透過入口網站啟用複寫專案的行動代理程式自動升級時才需要） | GuestAndHybridManagement

#### <a name="target-region-rules"></a>目標區域規則

**規則** |  **詳細資料** | **服務標記**
--- | --- | --- 
允許 HTTPS 輸出：連接埠 443 | 允許與目的地區域中的儲存體帳戶對應的範圍 | 容量.\<區功能變數名稱稱>
允許 HTTPS 輸出：連接埠 443 | 允許對應至 Azure AD 的範圍  | AzureActiveDirectory
允許 HTTPS 輸出：連接埠 443 | 允許對應至來源區域中事件中樞的範圍。 | EventsHub.\<區功能變數名稱稱>
允許 HTTPS 輸出：連接埠 443 | 允許對應至 Azure Site Recovery 的範圍  | AzureSiteRecovery
允許 HTTPS 輸出：連接埠 443 | 允許對應至 Azure Key Vault 的範圍（這只有在啟用啟用 ADE 的虛擬機器透過入口網站複寫時才需要） | AzureKeyVault
允許 HTTPS 輸出：連接埠 443 | 允許對應至 Azure 自動化控制器的範圍（只有針對透過入口網站啟用複寫專案的行動代理程式自動升級時才需要） | GuestAndHybridManagement


#### <a name="control-access-with-nsg-rules"></a>使用 NSG 規則控制存取

如果您使用 [NSG 規則](https://docs.microsoft.com/azure/virtual-network/security-overview)來篩選進出於 Azure 網路/子網路的網路流量，藉以控制 VM 連線，請注意下列需求：

- 來源 Azure 區域的 NSG 規則應允許複寫流量的輸出存取。
- 建議您先在測試環境中建立規則，再將其設置於生產環境。
- 使用[服務標記](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)，而不要允許個別 IP 位址。
    - 服務標記代表一組聚集在一起的 IP 位址前置詞，用以盡可能降低建立安全性規則時的複雜性。
    - Microsoft 會不定期自動更新服務標記。 
 
請深入了解 Site Recovery 的[輸出連線能力](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)，以及如何[使用 NSG 控制連線能力](concepts-network-security-group-with-site-recovery.md)。


### <a name="connectivity-for-multi-vm-consistency"></a>多 VM 一致性的連線能力

如果您啟用多部 VM 一致性，則複寫群組中的機器會透過連接埠 20004 彼此通訊。
- 請確定並沒有防火牆應用裝置封鎖了 VM 之間透過連接埠 20004 進行的內部通訊。
- 如果您想要讓 Linux VM 成為複寫群組的一部分，請確定已根據特定 Linux 版本的指引手動開啟連接埠 20004上 的輸出流量。




## <a name="failover-process"></a>容錯移轉程序

您起始容錯移轉時，系統會在目標資源群組、目標虛擬網路，目標子網路和目標可用性設定組中建立 VM。 在容錯移轉時，您可以使用任何復原點。

![容錯移轉程序](./media/concepts-azure-to-azure-architecture/failover.png)

## <a name="next-steps"></a>後續步驟

將 Azure VM [快速複寫](azure-to-azure-quickstart.md)到次要地區。
