---
title: 檔案層級的 SAP HANA Azure 備份 | Microsoft Docs
description: 備份 Azure 虛擬機器上的 SAP HANA 有兩種主要做法，本文涵蓋檔案層級的 SAP HANA Azure 備份
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/01/2020
ms.author: juergent
ms.openlocfilehash: 93b67936166eb73db5e9a15db42c2c6135794108
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78271393"
---
# <a name="sap-hana-azure-backup-on-file-level"></a>檔案層級的 SAP HANA Azure 備份

## <a name="introduction"></a>簡介

本文是有關[Azure 虛擬機器上的 SAP HANA 備份指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)的相關文章，它提供了有關入門的概述和資訊，以及有關 Azure 備份服務和存儲快照的更多詳細資訊。 

Azure 中的不同 VM 類型允許連接不同數目的 VHD。 確切的詳細資料記載於 [Azure 中的 Linux 虛擬機器大小](https://docs.microsoft.com/azure/virtual-machines/linux/sizes)。 對於本文檔中提到的測試，我們使用 GS5 Azure VM，它允許 64 個附加的資料磁片。 在較大型的 SAP HANA 系統中，資料和記錄檔可能已佔據大量磁碟，可能還加上用於最佳磁碟 IO 輸送量的軟體等量。 有關 Azure VM 上 SAP HANA 部署建議磁片配置的更多詳細資訊，請閱讀文章[SAP HANA Azure 虛擬機器存儲配置](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)。 所提供的建議也包括本機備份的磁碟空間建議。

在檔案層級管理備份/還原的標準方式，是透過 SAP HANA Studio 或 SAP HANA SQL 陳述式使用以檔案為基礎的備份。 有關詳細資訊，請閱讀文章 SAP [HANA SQL 和系統檢視參考](https://help.sap.com/hana/SAP_HANA_SQL_and_System_Views_Reference_en.pdf)。

![本圖顯示 SAP HANA Studio 中的備份的功能表對話方塊](media/sap-hana-backup-file-level/backup-menue-dialog.png)

本圖顯示 SAP HANA Studio 中的備份的功能表對話方塊。 當選擇 [檔案]&quot;&quot; 類型，必須指定檔案系統中的路徑讓 SAP HANA 寫入備份檔案。 還原的運作方式亦同。

雖然這項選擇聽起來簡單又直接，但仍有一些事要考量。 Azure VM 具有可附加的資料磁片數的限制。 可能無法在 VM 的檔案系統上儲存 SAP HANA 備份檔案，取決於資料庫的大小和磁碟輸送量需求，其中可能牽涉跨多個資料磁碟的軟體等量。 在本文稍後提供多種選項，可用於在處理數 TB 資料時，移動這些備份檔案以及管理檔案大小限制和效能。

另一個可以不計總容量提供更多自由的選項是 Azure Blob 儲存體。 雖然單一 blob 也有 1 TB 的限制，單一 blob 容器的總容量目前為 500 TB。 此外，它讓客戶可以選擇較具成本效益的「非經常性」&quot;&quot;blob 儲存體。 有關冷 Blob 存儲的詳細資訊，請參閱[Azure Blob 存儲：熱、酷和存檔訪問層](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers?tabs=azure-portal)。

如果想要更安全，使用異地複寫儲存體帳戶來存放 SAP HANA 備份。 有關存儲冗余和存儲複製的詳細資訊，請參閱[Azure 存儲冗余](https://docs.microsoft.com/azure/storage/common/storage-redundancy)。

一個人將 SAP HANA 備份專用的 VHD 放在異地複寫的專用備份儲存體帳戶。 而另一個人可以將存放 SAP HANA 備份的 VHD 複製到異地複寫的儲存體帳戶中，或複製到不同區域中的儲存體帳戶。

## <a name="azure-blobxfer-utility-details"></a>Azure blobxfer 公用程式詳細資料

為了在 Azure 儲存體上儲存目錄和檔案，可以使用 CLI 或 PowerShell，或開發使用其中一種 [Azure SDK](https://azure.microsoft.com/downloads/) 的工具。 還有一個現成的實用程式 AzCopy，用於將資料複製到 Azure 存儲。 （請參閱[使用 AzCopy 命令列實用程式傳輸資料](../../../storage/common/storage-use-azcopy.md)）。

因此，使用 Blobxfer 來複製 SAP HANA 備份檔案。 blobxfer 是開放原始碼，可從 [GitHub](https://github.com/Azure/blobxfer) 取得，許多客戶在生產環境中使用它。 此工具不允許將資料直接複製到 Azure blob 儲存體或 Azure 檔案共用。 它還提供了一系列有用的功能，如 md5 雜湊，或複製具有多個檔的目錄時的自動並行性。

## <a name="sap-hana-backup-performance"></a>SAP HANA 備份的效能
本章將討論性能注意事項。 實現的數位可能並不代表最新狀態，因為有穩定的開發來實現更好的 Azure 存儲輸送量。 因此，應對配置和 Azure 區域執行單獨的測試。

![這個螢幕擷取畫面是 SAP HANA Studio 中的 SAP HANA 備份主控台](media/sap-hana-backup-file-level/backup-console-hana-studio.png)

此螢幕截圖顯示了 SAP HANA 工作室的 SAP HANA 備份主控台。 使用一個磁片上的 XFS 檔案系統，在連接到 HANA VM 的單個 Azure 標準 HDD 存儲磁片上執行 230 GB 的備份大約需要 42 分鐘。

![這個螢幕擷取畫面是 SAP HANA 測試 VM 上的 YaST](media/sap-hana-backup-file-level/one-backup-disk.png)

這個螢幕擷取畫面是 SAP HANA 測試 VM 上的 YaST。 您可以看到用於 SAP HANA 備份的 1 TB 單個磁片。 備份 230 GB 花費約 42 分鐘。 此外，還連結了五個 200 GB 的磁碟並建立了軟體 RAID md0，而且在這五個 Azure 資料磁碟上串接。

![對軟體 RAID 重複相同的備份，並串接五個連結的 Azure 標準儲存體資料磁碟](media/sap-hana-backup-file-level/five-backup-disks.png)

對軟體 RAID 重複相同的備份，並串接五個連結的 Azure 標準儲存體資料磁碟，將備份時間從 42 分鐘降到 10 分鐘。 磁碟已連結，且沒有快取至 VM。 本練習演示磁片寫入輸送量對於實現良好備份時間的重要性。 您可以切換到 Azure 標準 SSD 存儲或 Azure 高級存儲，以進一步加快該過程以實現最佳性能。 通常，不建議使用 Azure 標準 HDD 存儲，並且僅用於演示目的。 建議為生產系統使用最少的 Azure 標準 SSD 存儲或 Azure 高級存儲。

## <a name="copy-sap-hana-backup-files-to-azure-blob-storage"></a>將 SAP HANA 備份檔案複製到 Azure Blob 儲存體
上面提到的性能編號、備份持續時間編號和複製持續時間編號可能並不代表 Azure 技術的最新狀態。 Microsoft 正在穩步改進 Azure 存儲，以提供更多輸送量和更低的延遲。 因此，這些數位僅用於演示目的。 您需要在您選擇的 Azure 區域中測試您的個人需求，以便能夠判斷使用方法最適合您。

快速儲存 SAP HANA 備份檔案的另一個選項是 Azure Blob 儲存體。 單個 Blob 容器的限制約為 500 TB，對於 SAP HANA 系統來說，使用 M32ts、M32ls、M64ls 和 GS5 VM 類型的 Azure，足以保留足夠的 SAP HANA 備份。 &quot;客戶可以選擇&quot;熱 blob 存儲&quot;和&quot;冷 Blob 存儲（請參閱[Azure Blob 存儲：熱、酷和存檔訪問層](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers?tabs=azure-portal)）。

使用 blobxfer 工具，將 SAP HANA 備份檔案直接複製到 Azure Blob 儲存體很容易。

![在這裡可以看到完整的 SAP HANA 檔案備份的檔案](media/sap-hana-backup-file-level/list-of-backups.png)

您可以看到完整的 SAP HANA 檔案備份的檔。 在四個檔中，最大的檔大約有 230 GB 大小。

![將 230 GB 複製至 Azure 的標準儲存體帳戶 blob 容器花了大約 3000 秒](media/sap-hana-backup-file-level/copy-duration-blobxfer.png)

在初始測試中沒有使用 md5 雜湊，將 230 GB 複製至 Azure 的標準儲存體帳戶 blob 容器花了大約 3000 秒。

HANA Studio 備份主控台可讓您限制 HANA 備份檔案的檔案大小上限。 在示例環境中，它擁有多個較小的備份檔案，而不是一個大型的 230 GB 檔，從而提高了性能。

在 HANA 端設置備份檔案大小限制不會&#39;縮短備份時間，因為檔是按順序編寫的。 檔案大小上限設定為 60 GB，因此備份會建立四個大型資料檔案，而不是 230 GB 的單一檔案。 如果您的備份目標對 Blob 大小的檔案大小有限制，則使用多個備份檔案可能成為備份 HANA 資料庫的必要條件。

![為了測試 blobxfer 工具的平行處理原則，而將 HANA 備份的檔案大小上限設為 15 GB](media/sap-hana-backup-file-level/parallel-copy-multiple-backup-files.png)

為了測試 blobxfer 工具的平行處理原則，而將 HANA 備份的檔案大小上限設為 15 GB，得到 19 個備份檔案。 如此設定，將 blobxfer 複製 230 GB 至 Azure Blob 儲存體的時間，從 3000 秒降至 875 秒 。

在探索將針對本地磁片執行的備份複製到其他位置（如 Azure Blob 存儲）時，請記住，最終並行複製過程使用的頻寬是針對單個 VM 類型的網路或存儲配額考慮的。 因此，您需要平衡複製的持續時間與 VM 中運行的正常工作負載所需的網路和存儲頻寬。 


## <a name="copy-sap-hana-backup-files-to-nfs-share"></a>將 SAP HANA 備份檔案複製到 NFS 共用

微軟 Azure 通過 Azure [NetApp 檔](https://azure.microsoft.com/services/netapp/)提供本機 NFS 共用。 您可以創建不同卷的十幾個 TB，以存儲和管理備份。 您還可以根據 NetApp 的技術對這些捲進行快照。 Azure NetApp 檔 （ANF） 提供三個不同的服務等級，提供不同的存儲輸送量。 有關詳細資訊，請閱讀[Azure NetApp 檔的服務等級](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)。 可以創建和裝載從 ANF 的 NFS 卷，如文章["快速入門：設置 Azure NetApp 檔"並創建 NFS 卷](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes?tabs=azure-portal)。

除了通過 ANF 使用 Azure 的本機 NFS 卷外，還有各種可能創建自己的部署，在 Azure 上提供 NFS 共用。 所有這些都有自己部署和管理這些解決方案所需的缺點。 這些文章中記錄了其中一些可能性：

- [SUSE Linux Enterprise Server 上 Azure VM 的 NFS 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)
- [Red Hat Enterprise Linux for SAP NetWeaver 上的 GlusterFS on Azure VM](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)

通過上述方式創建的 NFS 共用可用於直接針對這些 NFS 共用執行 HANA 備份或將針對本地磁片執行的備份複製到這些 NFS 共用。

> [!NOTE]
> SAP HANA 支援 NFS v3 和 NFS v4.x。 不支援任何其他格式（如具有 CIFS 檔案系統的 SMB）編寫 HANA 備份。 另請參閱[SAP 支援說明#1820529](https://launchpad.support.sap.com/#/notes/1820529)

## <a name="copy-sap-hana-backup-files-to-azure-files"></a>將 SAP HANA 備份檔案複製到 Azure 檔案

可以將 Azure 檔案共用掛接在 Azure Linux VM 內部。 文章["如何在 Linux 中使用 Azure 檔存儲](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-linux)"詳細介紹了如何執行配置。 有關 Azure 檔或 Azure 高級檔的限制，請閱讀文章[Azure 檔可伸縮性和性能目標](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets)。

> [!NOTE]
> SAP HANA 不支援使用 CIFS 檔案系統的 SMB 編寫 HANA 備份。 另請參閱[SAP 支援說明#1820529](https://launchpad.support.sap.com/#/notes/1820529)。 因此，您只能使用此解決方案作為直接針對本地連接磁片進行的 HANA 資料庫備份的最終目標
> 

在對 Azure 檔（而不是 Azure 高級檔）進行的測試中，複製 19 個備份檔案（總容量為 230 GB）需要大約 929 秒。 我們期望使用 Azure 高級檔的時間更好。 但是，您需要記住，您需要在快速複製的興趣與工作負載對網路頻寬的要求之間取得平衡。 由於每種 Azure VM 類型都強制實施網路頻寬配額，因此您需要在工作負荷以及備份檔案副本的範圍內保持該配額的範圍。

![本圖顯示複製 19 個 SAP HANA 備份檔案需要約 929 秒](media/sap-hana-backup-file-level/parallel-copy-to-azure-files.png)


在 Azure 檔上存儲 SAP HANA 備份檔案可能是一個有趣的選項。 特別是隨著 Azure 高級檔的延遲和輸送量的提高。

## <a name="next-steps"></a>後續步驟
* [Azure 虛擬機器上 SAP HANA 的備份指南](sap-hana-backup-guide.md)提供快速入門的概觀和詳細資訊。
* 要瞭解如何在 Azure（大型實例）上建立 SAP HANA 的高可用性和災害復原計畫，請參閱[Azure 上的 SAP HANA（大型實例）高可用性和災害復原](hana-overview-high-availability-disaster-recovery.md)。
