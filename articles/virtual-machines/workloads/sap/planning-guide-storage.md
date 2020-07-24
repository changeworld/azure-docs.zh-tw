---
title: 適用于 SAP 工作負載的 Azure 儲存體類型
description: 規劃適用于 SAP 工作負載的 Azure 儲存體類型
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/23/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 65fbd84a6fa4b03db9f5dfce81eeba23aceebbc9
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87042305"
---
# <a name="azure-storage-types-for-sap-workload"></a>SAP 工作負載的 Azure 儲存體類型
Azure 有許多不同的儲存體類型，在功能、輸送量、延遲和價格方面都有很大的差異。 某些儲存體類型不是，或不適用於 SAP 案例。 不過，有數個 Azure 儲存體類型很適合或已針對特定的 SAP 工作負載案例進行優化。 特別針對 SAP Hana，某些 Azure 儲存體類型已通過 SAP Hana 使用認證。 在本檔中，我們將逐步解說不同類型的儲存體，並說明其與 SAP 工作負載和 SAP 元件的功能和可用性。

這篇文章中所使用之單位的相關批註。 公用雲端廠商已移至使用 GiB （[gib](https://en.wikipedia.org/wiki/Gibibyte)）或 TiB （[個](https://en.wikipedia.org/wiki/Tebibyte)做為大小單位，而不是 gb 或 tb。 因此，所有 Azure 檔和獎項都會使用這些單位。  在整份檔中，我們會以獨佔方式參考 MiB、GiB 和 TiB 單位的這些大小單位。 您可能需要使用 MB、GB 和 TB 進行規劃。 因此，如果您需要使用 400 MiB/秒輸送量的大小，而不是 250 MiB/秒的輸送量，請留意計算的一些小差異。

## <a name="microsoft-azure-storage-resiliency"></a>Microsoft Azure 儲存體復原

Microsoft Azure 標準 HDD、標準 SSD、Azure premium 儲存體和 Ultra 磁片的儲存體，會將基底 VHD （含 OS）和 VM 連接的資料磁片或 Vhd，在三個不同的儲存體節點上保留三個複本。 在儲存節點失敗時，容錯移轉至另一個複本並植入新複本，是透明的。 由於此重複，**不**需要在多個 Azure 磁片上使用任何類型的儲存體冗余層。 這項事實稱為「本機備援儲存體 (LRS)」。 在 Azure 中，這些儲存體類型的 LRS 是預設值。 [Azure NetApp Files](https://azure.microsoft.com/services/netapp/)提供足夠的冗余，以達成與其他原生 Azure 儲存體相同的 sla。

另外還有幾個多餘的方法，它們都在適用于 Azure 必須提供的各種不同儲存體類型的[Azure 儲存體](../../../storage/common/storage-redundancy.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)複寫一文中說明。 

### <a name="azure-managed-disks"></a>Azure 受控磁片

受控磁片是 Azure Resource Manager 中的資源類型，可以用來取代儲存在 Azure 儲存體帳戶中的 Vhd。 受控磁碟會自動配合其所連接之虛擬機器的 [可用性設定組] [虛擬機器-管理-可用性]，進而增加虛擬機器和虛擬機器上執行之服務的可用性。 如需詳細資訊，請參閱[概觀文章](../../windows/managed-disks-overview.md)。

與復原相關的是，這個範例示範受控磁片的優點：

- 您要在 Azure 可用性設定組中部署您的兩個適用于 SAP 系統的 DBMS Vm 
- 當 Azure 部署 Vm 時，具有 OS 映射的磁片將會放在不同的存放裝置叢集中。 這可避免這兩個 Vm 受到單一 Azure 儲存體叢集的問題影響
- 當您建立指派給這些 Vm 的新受控磁片來儲存資料庫的資料和記錄檔時，這兩個 Vm 的新磁片也會部署在個別的儲存體叢集中，因此，第一個 VM 的磁片都不會與第二個 VM 的磁片共用儲存體叢集

在客戶定義的儲存體帳戶中不使用受控磁片進行部署，磁片配置是任意的，而且不知道 Vm 是在 A v 內部署，以供復原之用。

> [!NOTE]
> 基於此原因和其他透過受控磁片提供的其他改良功能，我們需要針對其磁片使用 Azure 區塊儲存體的 Vm 新部署（Azure NetApp Files 除外的所有 Azure 儲存體）必須針對基底 VHD/OS 磁片使用 Azure 受控磁片，也就是包含 SAP 資料庫檔案的資料磁片。 獨立于您是否透過可用性設定組部署 Vm，跨可用性區域或獨立于集合和區域。 用於儲存備份的磁片不一定要是受控磁片。

> [!NOTE]
> Azure 受控磁片只提供本機冗余（LRS）。 


## <a name="storage-scenarios-with-sap-workloads"></a>SAP 工作負載的儲存案例
在您于 Azure 中部署之堆疊的各種元件中，SAP 工作負載需要保存的儲存體。 這些案例最少列出如下：

- 保存您 VM 的基底 VHD，其中包含作業系統和您安裝在該磁片中的其他軟體。 此磁片/VHD 是 VM 的根目錄。 對其進行的任何變更都必須保存。 因此，下次您停止並重新啟動 VM 時，仍會有所有變更。 尤其是在 VM 正由 Azure 部署到另一部主機，而不是原本執行的情況下
- 保存的資料磁片。 這些磁片是您附加來儲存應用程式資料的 Vhd。 此應用程式資料可能是資料庫、備份檔案或軟體安裝的資料和記錄/重做檔案。 表示包含作業系統的基底 VHD 以外的任何磁片
- 檔案共用或共用磁片，其中包含 NetWeaver 或 S/4HANA 的全域傳輸目錄。 這些共用的內容可能由在多個 Vm 中執行的軟體取用，或用來建立高可用性的容錯移轉叢集案例
- /Sapmnt 目錄或 EDI 進程的一般檔案共用，或類似。 這些共用的內容可能由在多個 Vm 中執行的軟體取用，或用來建立高可用性的容錯移轉叢集案例

在接下來的幾節中，會討論適用于上述四個案例的不同 Azure 儲存體類型和其可用性的 SAP 工作負載。 有關不同 Azure 儲存體類型應如何使用的一般分類，請參閱[Azure 中有哪些磁片類型可用？](../../linux/disks-types.md)一文。 針對 SAP 工作負載使用不同 Azure 儲存體類型的建議不會 majorly 不同。

如需適用于 S/4HANA 的 SAP NetWeaver/應用層的 Azure 儲存體類型支援限制，請參閱[SAP Hana azure 虛擬機器儲存體](./hana-vm-operations-storage.md)設定一文中的[sap 支援附注 2015553](https://launchpad.support.sap.com/#/notes/2015553) ，以取得 SAP Hana 認證和支援的 azure 儲存體類型。

描述不同 Azure 儲存體類型的各節將提供您使用 SAP 支援的儲存體之限制和可能性的背景資訊。 

## <a name="storage-recommendations-for-sap-storage-scenarios"></a>SAP 儲存案例的儲存體建議
在進入詳細資料之前，我們會先介紹檔開頭的摘要和建議。 而特定 Azure 儲存體類型的詳細資料會遵循檔的這一節。 摘要說明資料表中 SAP 儲存案例的儲存體建議，如下所示：

| 使用方式情節 | 標準 HDD | 標準 SSD | 進階儲存體 | Ultra 磁碟 | Azure NetApp Files |
| --- | --- | --- | --- | --- | --- |
| OS 磁碟 | 不適用 |  受限制的適當（非生產） | 建議使用 | 不可能 | 不可能 |
| 全域傳輸目錄 | 不支援 | 不支援 | 建議使用 | 建議使用 | 建議使用 |
| /sapmnt | 不適用 | 受限制的適當（非生產） | 建議使用 | 建議使用 | 建議使用 |
| DBMS 資料量 SAP Hana M/Mv2 VM 系列 | 不支援 | 不支援 | 建議使用 | 建議使用 | 建議<sup>2</sup> |
| DBMS 記錄磁片區 SAP Hana M/Mv2 VM 系列 | 不支援 | 不支援 | 建議<sup>1</sup> | 建議使用 | 建議<sup>2</sup> | 
| DBMS 資料量 SAP Hana Esv3/Edsv4 VM 系列 | 不支援 | 不支援 | 建議使用 | 建議使用 | 建議<sup>2</sup> |
| DBMS 記錄磁片區 SAP Hana Esv3/Edsv4 VM 系列 | 不支援 | 不支援 | 不支援 | 建議使用 | 建議<sup>2</sup> | 
| DBMS 資料磁片區非 HANA | 不支援 | 受限制的適當（非生產） | 建議使用 | 建議使用 | 不支援 |
| DBMS 記錄磁片區非 HANA M/Mv2 VM 系列 | 不支援 | 受限制的適當（非生產） | 建議<sup>1</sup> | 建議使用 | 不支援 |
| DBMS 記錄磁片區非 HANA 非 M/Mv2 VM 系列 | 不支援 | 受限制的適當（非生產） | 適用于最多中型工作負載 | 建議使用 | 不支援 |


<sup>1</sup>使用[Azure 寫入加速器](../../windows/how-to-enable-write-accelerator.md)FOR M/Mv2 VM 系列進行記錄/重做記錄磁片區<sup>2</sup>使用及時，需要/HANA/DATA 和/hana/log 才會位於及 

您可以從不同的儲存類型清單中預期的特性，例如：

| 使用方式情節 | 標準 HDD | 標準 SSD | 進階儲存體 | Ultra 磁碟 | Azure NetApp Files |
| --- | --- | --- | --- | --- | --- |
| 輸送量/IOPS SLA | 否 | 否 | 是 | 可以 | 是 |
| 延遲讀取 | high | 中到高 | low | 子毫秒 | 子毫秒 |
| 延遲寫入 | high | 中到高  | 低（子毫秒<sup>1</sup>） | 子毫秒 | 子毫秒 |
| 支援 HANA | 否 | 否 | 是<sup>1</sup> | 可以 | 是 |
| 可能的磁片快照集 | 可以 | 可以 | 是 | 不可以 | 是 |
| 使用可用性設定組時，在不同的存放裝置叢集上配置磁片 | 透過受控磁片 | 透過受控磁片 | 透過受控磁片 | 透過可用性設定組部署的 Vm 不支援的磁片類型 | 否<sup>3</sup> |
| 與可用性區域對齊 | 可以 | 可以 | 可以 | 是 | 需要 Microsoft 的參與 |
| 區域性冗余 | 不適用於受控磁片 | 不適用於受控磁片 | 不適用於受控磁片 | 否 | 否 |
| 異地冗余 | 不適用於受控磁片 | 不適用於受控磁片 | 否 | 否 | 否 |


<sup>1</sup>使用適用于 M/Mv2 VM 系列的[Azure 寫入加速器](../../windows/how-to-enable-write-accelerator.md)進行記錄/重做記錄檔磁片區

<sup>2</sup>成本取決於布建的 IOPS 和輸送量

<sup>3</sup>建立不同的及容量集區並不保證將容量集區部署到不同的儲存體單位


> [!IMPORTANT]
> 若要使用 Azure NetApp Files （及）達到小於1毫秒的 i/o 延遲，您必須與 Microsoft 合作，根據及安排 Vm 與 NFS 共用之間的正確位置。 到目前為止，沒有任何機制可在部署的 VM 與及上裝載的 NFS 磁片區之間自動進行鄰近性。 假設不同的 Azure 區域有不同的設定，則新增的網路延遲可能會將 i/o 延遲推送至超過1毫秒，如果 VM 和 NFS 共用未配置於鄰近性中。


> [!IMPORTANT]
> 目前未提供以 Azure 區塊儲存體為基礎的受控磁片，或 Azure NetApp Files 提供任何區域性或地理位置的冗余。 因此，您必須確定您的高可用性和嚴重損壞修復架構不依賴任何類型的 Azure 原生儲存體複寫，適用于這些受控磁片、NFS 或 SMB 共用。


## <a name="azure-premium-storage"></a>Azure premium 儲存體
Azure premium SSD 儲存體的目的是為了提供：

* 低 i/o 延遲
* IOPS 和輸送量的 Sla
* I/o 延遲的變動較少

這種類型的儲存體是以 DBMS 工作負載為目標、需要低個位數毫秒延遲的儲存體流量，以及在 Azure premium 儲存體的情況下，IOPS 和輸送量成本基礎的 Sla 不是儲存在這類磁片中的實際資料磁片區，而是與儲存在磁片中的資料量無關。 您也可以在高階儲存體上建立磁片，而這些磁片不會直接對應至文章[進階 SSD](../../linux/disks-types.md#premium-ssd)所顯示的大小分類。 這篇文章的結論如下：

- 儲存體會組織成不同的範圍。 例如，範圍 513 GiB 到 1024 GiB 容量的磁片會共用相同的功能和相同的每月成本
- 每個 GiB 的 IOPS 不會跨大小分類來追蹤線性。 低於 32 GiB 的較小磁片，每個 GiB 的 IOPS 速率越高。 針對超過 32 GiB 至 1024 GiB 的磁片，每個 GiB 的 IOPS 速率介於每個 GiB 的 4-5 IOPS 之間。 對於最多 32767 GiB 的較大磁片，每個 GiB 的 IOPS 速率低於1
- 此儲存體的 i/o 輸送量不是具有磁片類別目錄大小的線性。 對於較小的磁片（例如 65 GiB 和 128 GiB 容量之間的類別），輸送量大約是 780KB/GiB。 而對於極端的大型磁片（例如 32767 GiB 磁片）而言，輸送量大約是 28KB/GiB
- 無法變更 IOPS 和輸送量 Sla，而不需要變更磁片的容量

Azure 具有99.9% 的單一實例 VM SLA，其系結至 Azure premium 儲存體或 Azure Ultra 磁片儲存體的使用量。 SLA 記載于虛擬機器的[sla](https://azure.microsoft.com/support/legal/sla/virtual-machines/)中。 為了符合此單一 VM SLA，基底 VHD 磁片和**所有**連接的磁片都必須是 azure premium 儲存體或 azure Ultra 磁片儲存體。

SAP 工作負載的功能矩陣看起來像這樣：

| 功能| 註解| 附注/連結 | 
| --- | --- | --- | 
| OS 基底 VHD | 適宜 | 所有系統 |
| 資料磁碟 | 適宜 | 所有系統-[特別針對 SAP Hana](../../windows/how-to-enable-write-accelerator.md) |
| SAP 全域傳輸目錄 | YES | [支援](https://launchpad.support.sap.com/#/notes/2015553) |
| SAP sapmnt | 適宜 | 所有系統 |
| 備份儲存體 | 適宜 | 適用于備份的短期儲存 |
| 共用/共用磁片 | 無法使用 | 需要 Azure Premium 檔案或協力廠商 |
| 復原 | LRS | 磁片沒有可用的 GRS 或 ZRS |
| Latency | 低至中 | - |
| IOPS SLA | YES | - |
| 從線性到容量的 IOPS | 以方括弧括住的半線性  | [受控磁片價格](https://azure.microsoft.com/pricing/details/managed-disks/) |
| 每一磁片的 IOPS 上限 | 20000[相依于磁片大小](https://azure.microsoft.com/pricing/details/managed-disks/) | 也請考慮[VM 限制](../../linux/sizes.md) |
| 輸送量 SLA | YES | - |
| 線性到容量的輸送量 | 以方括弧括住的半線性 | [受控磁片價格](https://azure.microsoft.com/pricing/details/managed-disks/) |
| HANA 認證 | YES | [特別針對 SAP Hana](../../windows/how-to-enable-write-accelerator.md) |
| 可能的磁片快照集 | YES | - |
| 可能 Azure 備份 VM 快照集 | YES | [寫入加速器](../../windows/how-to-enable-write-accelerator.md)快取的磁片除外  |
| 成本 | MEDIUM | - |

Azure premium 儲存體不會使用 Azure premium 儲存體所提供的通用快取類型來滿足 SAP Hana 儲存體延遲 Kpi。 為了滿足 SAP Hana 記錄寫入的儲存體延遲 Kpi，您需要使用 Azure 寫入加速器快取，如[啟用寫入加速器](../../windows/how-to-enable-write-accelerator.md)一文所述。 Azure 寫入加速器會針對其交易記錄寫入和重做記錄檔寫入，提供所有其他 DBMS 系統的優勢。 因此，建議您在所有 SAP DBMS 部署中使用它。 針對 SAP Hana，Azure 寫入加速器與 Azure premium 儲存體搭配使用是強制性的。



**摘要：** Azure premium 儲存體是建議用於 SAP 工作負載的其中一種 Azure 儲存體類型。 這項建議適用于非生產環境和生產系統。 Azure premium 儲存體適合用來處理資料庫工作負載。 Azure 寫入加速器的使用將大幅改善對 Azure premium 磁片的寫入延遲。 不過，對於具有高 IOPS 和輸送量速率的 DBMS 系統，您需要過度布建儲存體容量，或需要使用 Windows 儲存空間或 Linux 中的邏輯磁片區管理員之類的功能來建立等量集合，以便在一側提供所需的容量，同時也能以最符合成本效益的方式取得所需的 IOPS 或輸送量。


### <a name="azure-burst-functionality-for-premium-storage"></a>Premium 儲存體的 Azure 高載功能
針對容量較小或等於 512 GiB 的 Azure premium 儲存體磁片，會提供高載功能。 磁片高載運作方式的確切方式如下所[述。](../../linux/disk-bursting.md) 當您閱讀文章時，您瞭解當 i/o 工作負載低於磁片的名義 IOPS 和輸送量時，產生 IOPS 和輸送量的概念（如需有關名義輸送量的詳細資訊，請參閱[受控磁片定價](https://azure.microsoft.com/pricing/details/managed-disks/)）。 您將會在目前的使用量和磁片的名義值之間，累積 IOPS 和輸送量的差異。 高載限制為30分鐘。

在中，可規劃此高載功能的理想情況可能是包含不同 DBMS 資料檔案的磁片區。 對這些磁片區預期的 i/o 工作負載，特別是小型到中型的系統，應該看起來像這樣：

- 較低到適中的讀取工作負載，因為資料的理想情況是快取在記憶體中，或類似于 HANA 的情況應該完全在記憶體中
- 定期發出的資料庫檢查點或儲存點所觸發的寫入的高載
- 在不透過儲存體快照集執行備份的情況下，讀取連續串流的備份工作負載
- 針對 SAP Hana，在實例重新開機後將資料載入記憶體中

特別是在您的工作負載只處理每秒數百筆交易的較小 DBMS 系統上，這類高載功能對於儲存交易或重做記錄的磁片或磁片區也很有意義。 針對這類磁片或磁片區所預期的工作負載看起來像這樣：

- 定期寫入相依于工作負載的磁片和工作負載的本質，因為應用程式所發出的每個認可都可能會觸發 i/o 作業
- 作業工作案例的輸送量較高（例如建立或重建索引）
- 執行交易記錄或重做記錄備份時，讀取高載


## <a name="azure-ultra-disk"></a>Azure Ultra 磁片
Azure Ultra 磁碟可為 Azure IaaS VM 提供高輸送量、高 IOPS 以及一致的低延遲磁碟儲存體。 Ultra 磁片的一些額外優點包括能夠以動態方式變更磁片的 IOPS 和輸送量，以及您的工作負載，而不需要重新開機您的虛擬機器（VM）。 Ultra 磁片適用于資料密集的工作負載，例如 SAP DBMS 工作負載。 Ultra 磁片只能做為資料磁片使用，無法做為儲存作業系統的基礎 VHD 磁片。 我們建議使用 Azure premium 儲存體作為基礎的 VHD 磁片。

當您建立 ultra 磁片時，您有三個可定義的維度：

- 磁片的容量。 範圍是從 4 GiB 到 65536 GiB
- 已布建磁片的 IOPS。 不同的最大值適用于磁片的容量。 如需詳細資訊，請參閱[Ultra 磁片](../../linux/disks-types.md#ultra-disk)一文
- 已布建的儲存體頻寬。 不同的頻寬上限會視磁片的容量而定。 如需詳細資訊，請參閱[Ultra 磁片](../../linux/disks-types.md#ultra-disk)一文

單一磁片的成本取決於您可以分別為特定磁片定義的三個維度。 


SAP 工作負載的功能矩陣看起來像這樣：

| 功能| 註解| 附注/連結 | 
| --- | --- | --- | 
| OS 基底 VHD | 無法運作 | - |
| 資料磁碟 | 適宜 | 所有系統  |
| SAP 全域傳輸目錄 | YES | [支援](https://launchpad.support.sap.com/#/notes/2015553) |
| SAP sapmnt | 適宜 | 所有系統 |
| 備份儲存體 | 適宜 | 適用于備份的短期儲存 |
| 共用/共用磁片 | 無法使用 | 需要協力廠商 |
| 復原 | LRS | 磁片沒有可用的 GRS 或 ZRS |
| Latency | 非常低 | - |
| IOPS SLA | YES | - |
| 從線性到容量的 IOPS | 以方括弧括住的半線性  | [受控磁片價格](https://azure.microsoft.com/pricing/details/managed-disks/) |
| 每一磁片的 IOPS 上限 | 1200到160000 | 依存于磁片容量 |
| 輸送量 SLA | YES | - |
| 線性到容量的輸送量 | 以方括弧括住的半線性 | [受控磁片價格](https://azure.microsoft.com/pricing/details/managed-disks/) |
| HANA 認證 | YES | - |
| 可能的磁片快照集 | 否 | - |
| 可能 Azure 備份 VM 快照集 | 否 | - |
| 成本 | 高於 Premium 儲存體 | - |



**摘要：** Azure ultra 磁片是適合各種 SAP 工作負載的低延遲儲存體。 到目前為止，Ultra 磁片只能搭配已透過可用性區域（區域性部署）部署的 Vm 使用。 Ultra 磁片在此時間點不支援儲存體快照集。 與其他所有存放裝置相反，Ultra 磁片無法用於基底 VHD 磁片。 Ultra 磁片適用于 i/o 工作負載不斷波動的情況，而您想要調整已部署的儲存體輸送量或 IOPS 以儲存工作負載模式，而不是調整頻寬和 IOPS 的最大使用量。


## <a name="azure-netapp-files-anf"></a>Azure NetApp files （及）
[Azure NetApp Files](https://azure.microsoft.com/services/netapp/)是 Microsoft 與 NetApp 之間的一項合作結果，其目標是要提供高效能的 Azure 原生 NFS 和 SMB 共用。 重點在於提供高頻寬和低延遲的儲存體，以啟用 DBMS 部署案例，並在一段時間後透過 Azure 啟用 NetApp storage 的一般操作功能。 NFS/SMB 共用提供三種不同的服務層級，可區分儲存體輸送量和價格。 服務層級記載于[Azure NetApp Files 的服務層級](../../../azure-netapp-files/azure-netapp-files-service-levels.md)一文。 針對不同類型的 SAP 工作負載，強烈建議使用下列服務等級：

- SAP DBMS 工作負載：效能，理想的超級
- SAPMNT share：效能，理想的超級
- 全域傳輸目錄：效能，理想的超級

> [!NOTE]
> 布建大小下限為4個 TiB 單位，稱為容量集區。 然後，您可以在此容量集區中建立磁片區。 而您可以建立的最小磁片區是 100 GiB。 您可以在 TiB 步驟中展開容量集區。 如需定價，請參閱[Azure NetApp Files 定價](https://azure.microsoft.com/pricing/details/netapp/)一文

及儲存體目前支援數個 SAP 工作負載案例：

- 為 SAP 的全域傳輸目錄提供 SMB 或 NFS 共用
- 在高可用性案例中的共用 sapmnt，如中所述：
    - [使用適用于 SAP 應用程式的 Azure NetApp Files （SMB）在 Windows 上的 Azure Vm 上進行 SAP NetWeaver 的高可用性](./high-availability-guide-windows-netapp-files-smb.md)
    - [針對 SAP 應用程式使用 Azure NetApp Files 在 SUSE Linux Enterprise Server 上的 Azure VM 達到 SAP NetWeaver 高可用性](./high-availability-guide-suse-netapp-files.md)
    - [Azure 虛擬機器高可用性，適用于 Red Hat Enterprise Linux 上的 SAP NetWeaver，以及適用于 SAP 應用程式的 Azure NetApp Files](./high-availability-guide-rhel-netapp-files.md)
- 使用適用于/hana/data 和/hana/log 磁片區及/或 NFS 4.1 或/hana/shared 磁片區的 nfs v3 磁片區（如[SAP Hana Azure 虛擬機器儲存體](./hana-vm-operations-storage.md)設定一文所述），來 SAP Hana 部署

> [!NOTE]
> Azure NetApp Files 型 NFS 或 SMB 共用不支援其他 DBMS 工作負載。 如果即將變更，將會提供更新和變更。

如同已經使用 Azure premium 儲存體，當您需要遵循輸送量中的幾個最小數目時，每 GB 的固定或線性輸送量大小可能會有問題。 這就是 SAP Hana 的情況。 透過及，此問題可能會比使用 Azure premium 磁片更明顯。 在 Azure premium 磁片的情況下，您可以採用數個較小的磁片，每個 GiB 都有相對高的輸送量，並將等量分散到不同的位置，以符合成本效益，並以較低的容量 這種等量分割不適用於裝載于及上的 NFS 或 SMB 共用。 這項限制會導致部署 overcapacity，例如：

- 例如，若要在裝載于及的 NFS 磁片區上達到 250 MiB/秒的輸送量，您必須部署 Ultra 服務層級的 1.95 TiB 容量。 
- 若要達到 400 MiB/秒，您必須部署 3.125 TiB 容量。 但您可能需要過度布建容量，以達到您的磁片區所需的輸送量。 這項過度布建的容量會影響較小 HANA 實例的價格。 
- 在 SAP/sapmnt 目錄的及上使用 NFS 的空間中，通常會有 Azure NetApp Files 強制執行的最小容量 100 GiB 至 150 GiB。 不過，客戶經驗顯示 12.8 MiB/秒的相關輸送量（使用 Ultra 服務層級）可能不夠，而且可能會對 SAP 系統的穩定性造成負面影響。 在這種情況下，客戶可以藉由增加/sapmnt 磁片區的數量來避免問題，因此會提供更多的輸送量給該磁片區。

SAP 工作負載的功能矩陣看起來像這樣：

| 功能| 註解| 附注/連結 | 
| --- | --- | --- | 
| OS 基底 VHD | 無法運作 | - |
| 資料磁碟 | 適宜 | 僅 SAP Hana  |
| SAP 全域傳輸目錄 | YES | SMB 和 NFS |
| SAP sapmnt | 適宜 | 所有系統 SMB （僅限 Windows）或 NFS （僅限 Linux） |
| 備份儲存體 | 適宜 | - |
| 共用/共用磁片 | YES | SMB 3.0、NFS v3 和 NFS 4。1 |
| 復原 | LRS | 磁片沒有可用的 GRS 或 ZRS |
| Latency | 非常低 | - |
| IOPS SLA | YES | - |
| 從線性到容量的 IOPS | 嚴格的線性  | 相依于[服務層級](../../../azure-netapp-files/azure-netapp-files-service-levels.md) |
| 輸送量 SLA | YES | - |
| 線性到容量的輸送量 | 以方括弧括住的半線性 | 相依于[服務層級](../../../azure-netapp-files/azure-netapp-files-service-levels.md) |
| HANA 認證 | YES | - |
| 可能的磁片快照集 | YES | - |
| 可能 Azure 備份 VM 快照集 | 否 | - |
| 成本 | 高於 Premium 儲存體 | - |


及儲存體的其他內建功能：

- 執行磁片區快照集的功能
- 從快照集複製及的磁片區
- 從快照集復原磁碟區（貼齊-還原）

**摘要**： Azure NetApp FILES 是 HANA 認證的低延遲儲存體，可讓您部署 NFS 和 SMB 磁片區或共用。 儲存體隨附三個不同的服務層級，每個 GiB 的磁片區容量以線性方式提供不同的輸送量和 IOPS。 及儲存體可讓您部署具有待命節點 SAP Hana 相應放大案例。 儲存體適合提供/sapmnt 或 SAP 全域傳輸目錄所需的檔案共用。 及儲存體隨附功能可用性，以原生 NetApp 功能的形式提供。  



## <a name="azure-standard-ssd-storage"></a>Azure 標準 SSD 儲存體
相較于 Azure 標準 HDD 儲存體，Azure 標準 SSD 儲存體提供更佳的可用性、一致性、可靠性及延遲。 它已針對在較低 IOPS 層級需要一致效能的工作負載進行優化。 此儲存體是針對具有低 IOPS 和輸送量需求的非生產 SAP 系統所使用的最小儲存體。 SAP 工作負載的功能矩陣看起來像這樣：

| 功能| 註解| 附注/連結 | 
| --- | --- | --- | 
| OS 基底 VHD | 適用限制 | 非生產系統 |
| 資料磁碟 | 適用限制 | 具有低 IOPS 和延遲需求的部分非生產系統 |
| SAP 全域傳輸目錄 | 否 | [不支援](https://launchpad.support.sap.com/#/notes/2015553) |
| SAP sapmnt | 適用限制 | 非生產系統 |
| 備份儲存體 | 適宜 | - |
| 共用/共用磁片 | 無法使用 | 需要協力廠商 |
| 復原 | LRS、GRS | 磁片沒有可用的 ZRS |
| Latency | high | 對於 SAP 全域傳輸目錄或生產系統而言太高 |
| IOPS SLA | 否 | - |
| 每一磁片的 IOPS 上限 | 500 | 與磁片大小無關 |
| 輸送量 SLA | 否 | - |
| HANA 認證 | 否 | - |
| 可能的磁片快照集 | YES | - |
| 可能 Azure 備份 VM 快照集 | YES | - |
| 成本 | LOW | - |



**摘要：** Azure 標準 SSD 儲存體是基底 VHD 的非生產 Vm 的最低建議、具有相對延遲不區分的最終 DBMS 部署，以及/或低 IOPS 和輸送量速率。 此 Azure 儲存體類型已不再支援裝載 SAP 全域傳輸目錄。 



## <a name="azure-standard-hdd-storage"></a>Azure 標準 HDD 儲存體
當 Azure 基礎結構在2014年獲得 SAP NetWeaver 工作負載認證時，Azure 標準 HDD 儲存體是唯一的儲存體類型。 在2014年，Azure 虛擬機器的儲存體輸送量很小且低。 因此，這種儲存類型就能夠跟上需求。 儲存體適用于不區分延遲的工作負載，在 SAP 空間中幾乎沒有經驗。 隨著 Azure Vm 的輸送量增加，以及這些 Vm 所產生的工作量增加，此儲存體類型就不再適用于 SAP 案例的使用量。 SAP 工作負載的功能矩陣看起來像這樣：

| 功能| 註解| 附注/連結 | 
| --- | --- | --- | 
| OS 基底 VHD | 不適用 | - |
| 資料磁碟 | 不適用 | - |
| SAP 全域傳輸目錄 | 否 | [不支援](https://launchpad.support.sap.com/#/notes/2015553) |
| SAP sapmnt | 否 | 不支援 |
| 備份儲存體 | 適宜 | - |
| 共用/共用磁片 | 無法使用 | Azure 檔案儲存體或協力廠商的需求 |
| 復原 | LRS、GRS | 磁片沒有可用的 ZRS |
| Latency | high | 對 DBMS 使用、SAP 全域傳輸目錄或 sapmnt/saploc 而言太高 |
| IOPS SLA | 否 | - |
| 每一磁片的 IOPS 上限 | 500 | 與磁片大小無關 |
| 輸送量 SLA | 否 | - |
| HANA 認證 | 否 | - |
| 可能的磁片快照集 | YES | - |
| 可能 Azure 備份 VM 快照集 | YES | - |
| 成本 | LOW | - |



**摘要：** 標準 HDD 是只應用來儲存 SAP 備份的 Azure 儲存體類型。 它應該只作為非使用中系統的基底 VHD，例如在這裡和那裡用來查閱資料的已淘汰系統。 但沒有作用中的開發、QA 或生產 Vm 應該以該儲存體為基礎。 也不應該在該儲存體上裝載資料庫檔案


## <a name="azure-vm-limits-in-storage-traffic"></a>儲存體流量中的 Azure VM 限制
與內部部署案例相反，您所選取的個別 VM 類型，在您可以達成的儲存體頻寬中扮演重要的角色。 針對不同的儲存體類型，您需要考慮：

| 儲存體類型| Linux | Windows | 註解 |
| --- | --- | --- | --- |
| 標準 HDD | [Azure 中 Linux Vm 的大小](../../linux/sizes.md) | [Azure 中的 Windows Vm 大小](../../windows/sizes.md) | 很難觸及中型或大型 Vm 的儲存體限制 |
| 標準 SSD | [Azure 中 Linux Vm 的大小](../../linux/sizes.md) | [Azure 中的 Windows Vm 大小](../../windows/sizes.md) | 很難觸及中型或大型 Vm 的儲存體限制 |
| 進階儲存體 | [Azure 中 Linux Vm 的大小](../../linux/sizes.md) | [Azure 中的 Windows Vm 大小](../../windows/sizes.md) | 使用儲存體設定輕鬆地叫用 IOPS 或儲存體輸送量 VM 限制 |
| Ultra 磁片儲存體 | [Azure 中 Linux Vm 的大小](../../linux/sizes.md) | [Azure 中的 Windows Vm 大小](../../windows/sizes.md) | 使用儲存體設定輕鬆地叫用 IOPS 或儲存體輸送量 VM 限制 |
| Azure NetApp Files | [Azure 中 Linux Vm 的大小](../../linux/sizes.md) | [Azure 中的 Windows Vm 大小](../../windows/sizes.md) | 儲存體流量會使用網路輸送量頻寬，而不是儲存體頻寬！ |

如有任何限制，您可以注意：

- VM 越小，您可以附加的磁片就越少。 這並不適用于及。 由於您掛接了 NFS 或 SMB 共用，因此不會遇到要附加的共用磁片區數目限制
- Vm 具有 i/o 輸送量和 IOPS 限制，很容易就能透過 premium 儲存體磁片和 Ultra 磁片來達到
- 使用及時，共用磁片區的流量會耗用 VM 的網路頻寬，而不是儲存體頻寬
- 在雙數位 TiB 容量空間中的大型 NFS 磁片區中，從單一 VM 存取這類磁片區的輸送量，會根據與共享磁片區互動之單一會話的 Linux 限制而高原。 

當您在 SAP 系統的生命週期中調整 Azure Vm 的大小時，您應該評估新和較大 VM 類型的 IOPS 和儲存體輸送量限制。 在某些情況下，將儲存體設定調整為 Azure VM 的新功能也是合理的。 


## <a name="striping-or-not-striping"></a>等量或不等量分割
將多個 Azure 磁片中的等量集建立到一個較大的磁片區，可讓您將個別磁片的 IOPS 和輸送量累積成一個磁片區。 僅供 Azure 標準儲存體和 Azure premium 儲存體使用。 Azure Ultra 磁片，您可以在其中設定與磁片容量無關的輸送量和 IOPS，而不需要使用等量集合。 以 NFS 或 SMB 為基礎的共用磁片區不可以是等量。 由於 Azure premium 儲存體輸送量和 IOPS 的非線性本質，您可以使用與大型單一 Azure premium 儲存體磁片相同的 IOPS 和輸送量來布建較小的容量。 這是使用 Azure premium 儲存體以較低成本達到較高輸送量或 IOPS 的方法。 例如:

- 跨兩個 P15 premium 儲存體磁片的等量，可讓您獲得輸送量 
- 250 MiB/秒。這類磁片區會有 512 GiB 的容量。 如果您想要讓單一磁片每秒提供 250 MiB 輸送量，您必須挑選具有2個 TiB 容量的 P40 磁片。 
- 或者，您可以透過等量分割四個 P10 premium 儲存體磁片，並將其整體容量為 512 GiB，以達到 400 MiB/秒的輸送量。 如果您想要讓單一磁片每秒最少 500 MiB 輸送量，您必須挑選具有 8 TiB 的 P60 premium 儲存體磁片。 由於成本或高階儲存體的容量接近線性，因此您可以使用等量來節省成本。

有些規則必須遵循等量：

- 因為 Azure 儲存體已將資料保留為多餘，所以不應使用 VM 中設定的儲存體
- 適用于等量設定的磁片必須是相同大小

在多個較小的磁片上進行等量是使用 Azure premium 儲存體達成良好的價格/效能比率的最佳方式。 瞭解等量會有一些額外的部署和管理額外負荷。

如需特定等量大小建議，請參閱不同 DBMS 的檔，例如[SAP Hana Azure 虛擬機器儲存體](./hana-vm-operations-storage.md)設定。




## <a name="next-steps"></a>後續步驟
閱讀下列文章：

- [適用於 SAP 工作負載的 Azure 虛擬機器 DBMS 部署考量](./dbms_guide_general.md)
- [SAP HANA Azure 虛擬機器儲存體設定](./hana-vm-operations-storage.md)
 
