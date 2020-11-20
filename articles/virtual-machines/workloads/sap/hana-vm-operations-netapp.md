---
title: SAP Hana Azure 虛擬機器 ANF 設定 |Microsoft Docs
description: 適用于 SAP Hana 的 Azure NetApp Files 儲存體建議。
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP、Azure、ANF、HANA、Azure NetApp Files、snapshot
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/28/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 511801962d07e5fb99000b2fc19adce2489b46d3
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967477"
---
# <a name="nfs-v41-volumes-on-azure-netapp-files-for-sap-hana"></a>Azure NetApp Files for SAP HANA 上的 NFS v4.1 磁碟區

Azure NetApp Files 提供可用於 **/hana/shared**、 **/hana/data** 和 **/hana/log** 磁片區的原生 NFS 共用。 針對 **/hana/data** 和 **/hana/log** 磁片區使用以 ANF 為基礎的 nfs 共用需要使用 4.1 NFS 通訊協定。 在 ANF 上以共用為基礎時，不支援使用 **/hana/data** 和 **/hana/log** 磁片區的 NFS 通訊協定 v3。 


> [!IMPORTANT]
> Azure NetApp Files 上所執行的 NFS v3 通訊協定 **不** 支援用於 **/hana/data** 和 **/hana/log**。 從功能的觀點來看， **/hana/data** 和 **/hana/log** 磁片區必須使用 NFS 4.1。 但是針對 **/hana/shared** 磁片區，可以從功能的觀點來使用 nfs V3 或 nfs 4.1 通訊協定。

## <a name="important-considerations"></a>重要考量︰

考慮到適用於 SAP Netweaver 和 SAP Hana 的 Azure NetApp Files 時，請注意下列重要考量：

- 容量集區的最小值為 4 TiB  
- 最小磁碟區大小為 100 GiB
- Azure NetApp Files 和所有虛擬機器（其中裝載了 Azure NetApp Files 磁片區）必須位於相同的 Azure 虛擬網路或相同區域中的[對等互連虛擬網路](../../../virtual-network/virtual-network-peering-overview.md)
- 請務必將虛擬機器部署在 Azure NetApp 儲存體附近，以降低延遲。  
- 選取的虛擬網路必須有委派給 Azure NetApp Files 的子網
- 請確定資料庫伺服器到 ANF 磁片區的延遲已測量且低於1毫秒
- Azure NetApp 磁碟區的輸送量是磁碟區配額的功能和服務等級，如 [Azure NetApp Files 的服務等級](../../../azure-netapp-files/azure-netapp-files-service-levels.md)中所述。 調整 HANA Azure NetApp 磁片區的大小時，請確定產生的輸送量符合 HANA 系統需求
- 嘗試「合併」磁片區以在較大的磁片區中達到更高的效能，例如，使用一個磁片區進行/sapmnt、/usr/sap/trans、.。。 可能的話  
- Azure NetApp Files 提供[匯出原則](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)：您可控制允許的用戶端、存取類型 (讀取及寫入、唯讀等)。 
- Azure NetApp Files 功能尚無法感知區域。 Azure NetApp Files 功能目前不會部署在 Azure 區域中的所有可用性區域。 請留意某些 Azure 區域中可能出現的延遲情形。   
- <b>sid</b>adm 的使用者識別碼，以及虛擬機器上 `sapsys` 的群組識別碼，都必須符合 Azure NetApp Files 中的設定。 

> [!IMPORTANT]
> 針對 SAP Hana 工作負載，低延遲很重要。 請與您的 Microsoft 代表合作，以確保虛擬機器和 Azure NetApp Files 磁碟區已部署在附近。  

> [!IMPORTANT]
> 如果 <b>sid</b>Adm 的使用者識別碼與 `sapsys` 虛擬機器和 Azure netapp Configuration 之間的群組識別碼不符，則會將 Azure netapp 磁片區上的檔案許可權顯示為 `nobody` 。 [將新系統上線](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u)至 Azure NetApp Files 時，請務必為 <b>sid</b>adm 指定正確的使用者識別碼，以及為 `sapsys` 指定群組識別碼。


## <a name="sizing-for-hana-database-on-azure-netapp-files"></a>針對 Azure NetApp Files 上的 HANA 資料庫進行大小調整

Azure NetApp 磁碟區的輸送量是磁碟區大小的功能和服務等級，如 [Azure NetApp Files 的服務等級](../../../azure-netapp-files/azure-netapp-files-service-levels.md)中所述。 

重要的是，效能關聯性的大小，以及 SVM (儲存體虛擬機器) 之 LIF (邏輯介面) 的實體限制。

下表示范建立大型「標準」磁片區來儲存備份，以及建立大於 12 TB 的「Ultra」磁片區並不合理，因為會超過單一 LIF 的實體頻寬容量。 

LIF 和單一 Linux 會話的最大輸送量介於1.2 到 1.4 GB/秒之間。 

| 大小  | 輸送量標準 | 輸送量 Premium | Ultra 的輸送量 |
| --- | --- | --- | --- |
| 1 TB | 16 MB/秒 | 64 MB/秒 | 128 MB/秒 |
| 2 TB | 32 MB/秒 | 128 MB/秒 | 256 MB/秒 |
| 4 TB | 64 MB/秒 | 256 MB/秒 | 512 MB/秒 |
| 10 TB | 160 MB/秒 | 640 MB/秒 | 1.280 MB/秒 |
| 15 TB | 240 MB/秒 | 960 MB/秒 | 1.400 MB/秒 |
| 20 TB | 320 MB/秒 | 1.280 MB/秒 | 1.400 MB/秒 |
| 40 TB | 640 MB/秒 | 1.400 MB/秒 | 1.400 MB/秒 |

請務必瞭解，資料會寫入至儲存體後端中的相同 Ssd。 容量集區的效能配額是為了能夠管理環境而建立的。
所有 HANA 資料庫大小的儲存體 Kpi 都相等。 在幾乎所有情況下，這項假設並不反映實際和客戶的期望。 HANA 系統的大小不一定表示小型系統需要較低的儲存體輸送量–而大型系統需要高儲存體輸送量。 但是，我們通常會預期較大的 HANA 資料庫實例需要較高的輸送量。 由於 SAP 的基礎硬體調整大小規則的結果，較大的 HANA 實例也可提供更多的 CPU 資源和更高的平行處理原則，例如在實例重新開機後載入資料。 因此，您應該將磁片區大小採用給客戶期望和需求。 而不只是由純容量需求所驅動。

當您在 Azure 中設計適用于 SAP 的基礎結構時，您應該知道 SAP 所) 的生產系統 (的一些最低儲存體輸送量需求，這會轉譯成下列各項的最小輸送量特性：

| 磁片區類型和 i/o 類型 | SAP 要求的最小 KPI | Premium 服務層級 | Ultra 服務層級 |
| --- | --- | --- | --- |
| 記錄磁片區寫入 | 250 MB/秒 | 4 TB | 2 TB |
| 資料磁片區寫入 | 250 MB/秒 | 4 TB | 2 TB |
| 資料磁片區讀取 | 400 MB/秒 | 6.3 TB | 3.2 TB |

因為這三個 Kpi 都有要求，所以必須將 **/hana/data** 磁片區的大小調整為較大的容量，以滿足最基本的讀取需求。

針對不需要高頻寬的 HANA 系統，ANF 磁片區大小可能較小。 如果 HANA 系統需要更多輸送量，您可以調整線上容量的大小來調整磁片區大小。 未定義備份磁片區的 Kpi。 不過，備份磁片區輸送量對於執行良好的環境而言是不可或缺的。 記錄檔和資料磁片區效能必須設計為客戶期望。

> [!IMPORTANT]
> 與您在單一 NFS 磁碟區上部署的容量不同，取用者在虛擬機器中所用的輸送量峰值預期會出現在 1.2-1.4 GB/秒的頻寬範圍內。 這必須搭配 ANF 供應項目的基礎結構和 NFS 周圍相關的 Linux 工作階段限制。 [適用於Azure NetApp Files 的效能基準測試](../../../azure-netapp-files/performance-benchmarks-linux.md)一文所述的效能和輸送量數目是針對一個具有多個用戶端 VM 的共用 NFS 磁碟區進行，因此有多個工作階段。 這種情況與我們在 SAP 中測量的案例不同。 我們會從單一 VM 中對 NFS 磁碟區測量輸送量。 裝載于 ANF 上。

為了符合資料和記錄的 SAP 最小輸送量需求，以及根據 **/hana/shared** 的指導方針，建議的大小如下所示：

| 磁碟區 | 大小<br /> 進階儲存層 | 大小<br /> Ultra 儲存層 | 支援的 NFS 通訊協定 |
| --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB | v4.1 |
| /hana/data | 6.3 TiB | 3.2 TiB | v4.1 |
| /hana/shared 擴大 | 最小 (1 TB，1 x RAM)   | 最小 (1 TB，1 x RAM)  | v3 或 4.1 版 |
| /hana/shared 向外延展 | 1 x 個背景工作節點的 RAM<br /> 每4個背景工作節點  | 1 x 個背景工作節點的 RAM<br /> 每4個背景工作節點  | v3 或 4.1 版 |
| /hana/logbackup | 3 x RAM  | 3 x RAM | v3 或 4.1 版 |
| /hana/backup | 2 x RAM  | 2 x RAM | v3 或 4.1 版 |

針對所有磁片區，強烈建議使用 NFS 4。1

備份磁片區的大小為估計。 您必須根據工作負載和作業程式來定義確切的需求。 針對備份，您可以將不同 SAP Hana 實例的許多磁片區合併到一個 (或兩個) 較大的磁片區，這可能會有較低的服務層級 ANF。

> [!NOTE]
> 本檔中所述的 Azure NetApp Files、大小調整建議，是以 SAP 向其基礎結構提供者所表達的最低需求為目標。 在實際的客戶部署和工作負載案例中，這可能不夠。 請將這些建議當作起點，並且根據特定工作負載的需求來調整。  

因此，您可以考慮為已針對 Ultra 磁碟儲存體列出的 ANF 磁碟區，部署類似的輸送量。 針對大小，也請考慮 Ultra 磁碟資料表中已針對不同 VM SKU 列出的磁碟區大小。

> [!TIP]
> 您可以動態地重新調整 Azure NetApp Files 磁碟區大小，而不需要 `unmount` 磁碟區、停止虛擬機器或停止 SAP Hana。 這可讓您彈性地滿足應用程式上預期和未預期的輸送量需求。

[使用SUSE Linux Enterprise Server 上的 Azure NetApp Files，在 Azure VM 上使用待命節點來擴展 SAP Hana](./sap-hana-scale-out-standby-netapp-files-suse.md) 中，已發佈如何使用 ANF 中所裝載的 NFS v4.1 磁碟區，搭配待命節點來部署 SAP Hana 延展設定的方法。


## <a name="availability"></a>可用性
系統會套用 ANF 系統更新和升級，而不會影響客戶環境。 定義的 [SLA 是 99.99%](https://azure.microsoft.com/support/legal/sla/netapp/)。


## <a name="volumes-and-ip-addresses-and-capacity-pools"></a>磁片區和 IP 位址和容量集區
使用 ANF 時，請務必瞭解基礎結構的建立方式。 容量集區只是一個結構，可讓您更輕鬆地建立 ANF 的計費模型。 容量集區與基礎結構沒有實體關聯性。 如果您建立容量集區，則只會建立可以收費的 shell，而不是其他。 當您建立磁片區時，會在數個 NetApp 系統的叢集中建立第一個 SVM (存放裝置虛擬機器) 。 為此 SVM 建立單一 IP 以存取磁片區。 如果您建立數個磁片區，所有磁片區都會透過此多控制器 NetApp 叢集散佈在此 SVM 中。 即使您只取得一個 IP，資料仍會分散在數個控制器上。 ANF 具有邏輯，可在設定的儲存體的磁片區或/和容量達到內部預先定義的層級時，自動散發客戶工作負載。 您可能會注意到這種情況，因為系統會指派新的 IP 位址來存取磁片區。

##<a name="log-volume-and-log-backup-volume"></a>記錄磁片區和記錄備份磁片區
「記錄磁片區」 (**/hana/log**) 用來寫入線上重做記錄。 因此，在此磁片區中有開啟的檔案，因此無法將此磁片區建立快照集。 線上重做記錄檔已滿或執行重做記錄備份之後，線上重做記錄檔會封存或備份到記錄備份磁片區。 為了提供合理的備份效能，記錄備份磁片區需要良好的輸送量。 若要將儲存體成本優化，可將多個 HANA 實例的記錄備份磁片區合併。 如此一來，多個 HANA 實例會利用相同的磁片區，並將其備份寫入不同的目錄。 您可以使用這種合併來取得更多的輸送量，因為您需要讓磁片區變得更大。 

這同樣適用于您用來寫入完整 HANA 資料庫備份的磁片區。  
 

## <a name="backup"></a>備份
除了串流備份和 Azure 備份服務 SAP Hana 資料庫（如 [Azure 虛擬機器上的 SAP Hana 的備份指南](./sap-hana-backup-guide.md)中所述），Azure NetApp Files 也開啟了執行以儲存體為基礎的快照備份的可能性。 

SAP Hana 支援：

- SAP Hana 1.0 SPS7 的儲存體型快照集備份
- 以儲存體為基礎的快照集備份支援多資料庫容器 (MDC) HANA 環境，從 SAP Hana 2.0 SPS4


建立以儲存體為基礎的快照集備份是一個簡單的四步驟程式， 
1. 建立 HANA (內部) 資料庫快照集-您或工具需要執行的活動 
1. SAP Hana 將資料寫入資料檔案以在儲存體上建立一致的狀態，則在建立 HANA 快照集時，會執行此步驟。
1. 在儲存體上的 **/hana/data** 磁片區上建立快照集-您或工具需要執行的步驟。 不需要在 **/hana/log** 磁片區上執行快照集
1. 刪除 HANA (內部) 資料庫快照集，並繼續正常操作-您或工具需要執行的步驟

> [!WARNING]
> 遺漏最後一個步驟或無法執行最後一個步驟會對 SAP Hana 的記憶體需求造成嚴重的影響，並可能導致 SAP Hana 停止

```
BACKUP DATA FOR FULL SYSTEM CREATE SNAPSHOT COMMENT 'SNAPSHOT-2019-03-18:11:00';
```

![SAP Hana 的 ANF 快照集備份](media/hana-vm-operations-netapp/storage-snapshot-scenario.png)

```
az netappfiles snapshot create -g mygroup --account-name myaccname --pool-name mypoolname --volume-name myvolname --name mysnapname 
```

![SAP Hana 第2部分的 ANF 快照集備份](media/hana-vm-operations-netapp/storage-snapshot.png)

```
BACKUP DATA FOR FULL SYSTEM CLOSE SNAPSHOT BACKUP_ID 47110815 SUCCESSFUL SNAPSHOT-2020-08-18:11:00';
```

您可以使用各種工具，以各種不同的方式來管理此快照集備份程式。 其中一個範例是 GitHub 上提供的 python 腳本 "ntaphana_azure .py" [https://github.com/netapp/ntaphana](https://github.com/netapp/ntaphana) ，這是範例程式碼，在沒有任何維護或支援的情況下提供「依原樣」。



> [!CAUTION]
> 快照本身並不是受保護的備份，因為它與您剛才拍攝快照集的磁片區位於相同的實體儲存體上。 您必須「每天保護」至少一個快照集到不同的位置。 這可以在相同環境中，在遠端 Azure 區域或 Azure Blob 儲存體中完成。


針對 Commvault 備份產品的使用者，第二個選項是 Commvault IntelliSnap V. 11.21 和更新版本。 此版或更新版本的 Commvault 提供 Azure NetApp Files 支援。 [Commvault IntelliSnap 11.21](https://documentation.commvault.com/11.21/essential/116350_getting_started_with_backup_and_restore_operations_for_azure_netapp_file_services_smb_shares_and_nfs_shares.html)一文提供詳細資訊。


### <a name="back-up-the-snapshot-using-azure-blob-storage"></a>使用 Azure blob 儲存體備份快照集
備份至 Azure blob 儲存體是一種符合成本效益且快速的方法，可儲存以 ANF 為基礎的 HANA 資料庫儲存體快照集備份。 若要將快照集儲存至 Azure Blob 儲存體，azcopy 工具是慣用的。 下載此工具的最新版本，並加以安裝，例如，在安裝 GitHub 的 python 腳本的 bin 目錄中。
下載最新的 azcopy 工具：

```
root # wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1
Saving to: ‘azcopy_v10.tar.gz’
```

最先進的功能是同步處理選項。 如果您使用 SYNC 選項，azcopy 會讓來源和目的地目錄保持同步。 使用參數 **--刪除-目的地** 是很重要的。 如果沒有這個參數，azcopy 就不會刪除目的地網站上的檔案，而且目的地端的空間使用率會增加。 在您的 Azure 儲存體帳戶中建立區塊 Blob 容器。 然後建立 blob 容器的 SAS 金鑰，並將快照集資料夾同步處理至 Azure Blob 容器。

例如，如果每日快照集應該同步處理至 Azure blob 容器以保護資料。 而且，您應該只保留一個快照集，可以使用下列命令。

```
root # > azcopy sync '/hana/data/SID/mnt00001/.snapshot' 'https://azacsnaptmytestblob01.blob.core.windows.net/abc?sv=2021-02-02&ss=bfqt&srt=sco&sp=rwdlacup&se=2021-02-04T08:25:26Z&st=2021-02-04T00:25:26Z&spr=https&sig=abcdefghijklmnopqrstuvwxyz' --recursive=true --delete-destination=true
```

## <a name="next-steps"></a>後續步驟
閱讀文章：

- [Azure 虛擬機器的 SAP Hana 高可用性](./sap-hana-availability-overview.md)