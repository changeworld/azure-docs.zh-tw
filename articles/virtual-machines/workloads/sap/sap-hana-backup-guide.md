---
title: Azure 虛擬機器上的 SAP HANA 備份指南 | Microsoft Docs
description: SAP HANA 備份指南說明備份 Azure 虛擬機器上 SAP HANA 的兩種主要做法
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/01/2020
ms.author: juergent
ms.openlocfilehash: bb32350597059209e5baf01d53b0c59fdc2344f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255240"
---
# <a name="backup-guide-for-sap-hana-on-azure-virtual-machines"></a>Azure 虛擬機器上的 SAP HANA 備份指南

## <a name="getting-started"></a>開始使用

Azure 虛擬機器上執行的 SAP HANA 備份指南只描述 Azure 相關主題。 有關常規 SAP HANA 備份相關專案，請查看 SAP HANA 文檔。 我們希望您熟悉具有健全有效的備份策略的原則資料庫備份策略、原因和動機，並瞭解貴公司對備份過程、備份保留期和恢復的要求程式。

各種 Azure VM 類型都正式支援 SAP HANA (例如 Azure M 系列)。 有關 SAP HANA 認證的 Azure VM 和 HANA 大型實例單元的完整清單，請查看[查找認證 IaaS 平臺](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)。 Microsoft Azure 提供了許多單元，SAP HANA 在物理伺服器上運行非虛擬化。 此服務稱為[HANA 大型實例](hana-overview-architecture.md)。 本指南將不介紹 HANA 大型實例的備份過程和工具。 但將僅限於 Azure 虛擬機器。 有關使用 HANA 大型實例進行備份/還原進程的詳細資訊，請閱讀文章[HLI 備份和還原](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore)。

本文的重點是 SAP HANA 在 Azure 虛擬機器上的三種備份可能性：

- 通過 Azure[備份服務](https://docs.microsoft.com/azure/backup/backup-overview)進行 HANA 備份 
- HANA 備份至 Azure Linux 虛擬機器中的檔案系統 (請參閱[檔案層級的 SAP HANA Azure 備份](sap-hana-backup-file-level.md))
- 使用手動 Azure 存儲 Blob 快照功能或 Azure 備份服務基於存儲快照的 HANA 備份


SAP HANA 提供備份 API，可讓第三方備份工具直接與 SAP HANA 整合。 Azure 備份服務或[Commvault](https://azure.microsoft.com/resources/protecting-sap-hana-in-azure/)等產品正在使用此專有介面觸發 SAP HANA 資料庫或重做記錄備份。 


有關如何查找 Azure 上支援的 SAP 軟體的資訊，請參閱文章["Azure 部署支援哪些 SAP 軟體](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)"。

## <a name="azure-backup-service"></a>Azure 備份服務

顯示的第一個方案是 Azure 備份服務使用 SAP HANA`backint`介面從 SAP HANA 資料庫執行流式備份。 或者，使用 Azure 備份服務更通用的功能來創建應用程式一致的磁片快照，並將該快照傳輸到 Azure 備份服務。

Azure 備份組成並認證為 SAP HANA 的備份解決方案，使用稱為[backint](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5)的專有 SAP HANA 介面。 有關解決方案、其功能及其可用的 Azure 區域的更多詳細資訊，請閱讀有關[在 Azure VM 上備份 SAP HANA 資料庫](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support)的文章。 有關 HANA Azure 備份服務的詳細資訊和原則，請閱讀[有關 Azure VM 中的 SAP HANA 資料庫備份](https://docs.microsoft.com/azure/backup/sap-hana-db-about)的文章。 

利用 Azure 備份服務的第二種可能性是使用 Azure 高級存儲的磁片快照創建應用程式一致的備份。 其他 HANA 認證的 Azure 存儲（如[Azure 超磁片](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-ultra-ssd)和[Azure NetApp 檔](https://azure.microsoft.com/services/netapp/)）不支援通過 Azure 備份服務進行此類快照。 閱讀以下文章：

- [在 Azure 中規劃 VM 備份基礎結構](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction)
- [Azure Linux VM 的應用程式一致備份](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent) 

出現此序列的活動：

- Azure 備份需要執行預快照腳本，使應用程式（本例中為 SAP HANA）處於一致狀態
- 確認此一致狀態後，Azure 備份將執行磁片快照
- 完成快照後，Azure 備份將撤銷在快照前腳本中執行的活動
- 成功執行後，Azure 備份會將資料流程式傳輸到備份保存庫

對於 SAP HANA，大多數客戶對包含 SAP HANA 重做日誌的卷使用 Azure 寫入加速器。 Azure 備份服務將自動從快照中排除這些卷。 此排除不會損害 HANA 的恢復能力。 儘管它會阻止使用幾乎所有其他 SAP 支援的 DBMS 進行還原的能力。

這種可能性的缺點是，您需要開發自己的快照前和快照後腳本。 預快照腳本需要創建 HANA 快照並處理最終異常情況。 而快照後腳本需要再次刪除 HANA 快照。 有關所需邏輯的更多詳細資訊，請從[SAP 支援說明#2039883](https://launchpad.support.sap.com/#/notes/2039883)開始。 本文中"在拍攝存儲快照時 SAP HANA 資料一致性"一節的注意事項完全適用于此類備份。

> [!NOTE]
> 在使用多個資料庫容器的部署中，SAP HANA 的基於磁片快照的備份需要至少發佈 HANA 2.0 SP04
> 

請參閱本文檔後面的有關存儲快照的詳細資訊。

![此圖顯示儲存目前 VM 狀態的兩種做法](media/sap-hana-backup-guide/azure-backup-service-for-hana.png)

## <a name="other-hana-backup-methods"></a>其他 HANA 備份方法
還有三種其他備份方法或路徑可以考慮：

- 備份基於 Azure NetApp 檔 （ANF） 的 NFS 共用。 ANF 再次能夠創建存儲備份的卷的快照。 給定您最終編寫備份所需的輸送量，此解決方案可能會成為一種昂貴的方法。 雖然易於建立，因為 HANA 可以直接將備份寫入 Azure 本機 NFS 共用
- 針對標準 SSD 或 Azure 高級存儲的 VM 連接磁片執行 HANA 備份。 作為下一步，您可以根據 Azure Blob 存儲複製這些備份檔案。 這種策略可能是價格明智的吸引力
- 針對標準 SSD 或 Azure 高級存儲的 VM 連接磁片執行 HANA 備份。 作為下一步，磁片會定期進行快照。 在第一個快照之後，增量快照可用於降低成本

![此圖顯示在 VM 中建立 SAP HANA 檔案備份的選項](media/sap-hana-backup-guide/other-hana-backup-paths.png)

此圖顯示在 VM 中建立 SAP HANA 檔案備份的選項，然後再使用不同的工具將 HANA 備份檔案儲存在其他地方。 但是，所有不涉及協力廠商備份服務或 Azure 備份服務的解決方案都有幾個共同點。 其中一些可以列出，例如保留管理、自動還原過程以及提供 Azure 備份服務或其他專用協力廠商備份套件和服務的自動點恢復。 其中許多協力廠商服務能夠在 Azure 上運行。 


## <a name="sap-resources-for-hana-backup"></a>HANA 備份的 SAP 資源

### <a name="sap-hana-backup-documentation"></a>SAP HANA 備份文件

- [SAP HANA 管理簡介](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US)
- [規劃備份和復原策略](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)
- [使用 ABAP DBACOCKPIT 排程 HANA 備份](https://www.hanatutorials.com/p/schedule-hana-backup-using-abap.html)
- [排程資料備份 (SAP HANA Cockpit)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/6d/385fa14ef64a6bab2c97a3d3e40292/frameset.htm)
- [SAP Note 1642148](https://launchpad.support.sap.com/#/notes/1642148) 中的 SAP HANA 備份常見問題集
- [SAP Note 2039883](https://launchpad.support.sap.com/#/notes/2039883) 中的 SAP HANA 資料庫和儲存體快照集的常見問題集
- [SAP Note 1820529](https://launchpad.support.sap.com/#/notes/1820529) 中的不適用於備份和復原的網路檔案系統

### <a name="how-to-verify-correctness-of-sap-hana-backup"></a>如何確認 SAP HANA 備份的正確性
與備份方法無關，針對不同的系統運行測試還原是絕對必要的。 這個方法能確保備份正確，以及備份和還原的內部程序如預期般運作。 雖然還原備份可能是本地的障礙，因為它需要基礎結構，但通過暫時為此提供必要的資源，在雲中完成起來要容易得多。 正確程度是，HANA 提供了一些工具，可以檢查備份檔案的還原能力。 但是，頻繁恢復練習的目的是測試資料庫還原過程，並與操作人員一起訓練該進程。

請記住，光是簡單執行還原，然後確認 HANA 有在執行是不夠的。 應運行表一致性檢查，以確保還原的資料庫正常。 SAP HANA 提供數種一致性檢查，如 [SAP Note 1977584](https://launchpad.support.sap.com/#/notes/1977584) 中所述。

有關資料表一致性檢查的相關資訊，也可以在 SAP 網站上的[資料表和目錄一致性檢查](https://help.sap.com/saphelp_hanaplatform/helpdata/en/25/84ec2e324d44529edc8221956359ea/content.htm#loio9357bf52c7324bee9567dca417ad9f8b)找到。

### <a name="pros-and-cons-of-hana-backup-versus-storage-snapshot"></a>比較 HANA 備份與儲存體快照集的優缺點

SAP 不特別偏好 HANA 備份或儲存體快照集之中的任何一個。 它會列出其各別的優缺點，讓您視情況和可用的儲存技術決定要使用哪一個 (請參閱[規劃備份和復原策略](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm))。

在 Azure 上，請注意 Azure blob 快照功能&#39;未跨多個磁片提供檔案系統一致性（請參閱[使用帶 PowerShell 的 blob 快照](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)）。 

此外，您必須了解經常使用 blob 快照集對費用的影響，如這篇文章中所述︰[了解快照如何產生費用](/rest/api/storageservices/understanding-how-snapshots-accrue-charges)— 它不像使用 Azure 虛擬磁碟那麼明顯。

### <a name="sap-hana-data-consistency-when-taking-storage-snapshots"></a>建立儲存體快照集時，SAP HANA 資料的一致性

如前所述，在拍攝存儲快照時，必須描述 Azure 備份、檔案系統和應用程式一致性的快照備份功能。 若要避免問題，最簡單的方法是關閉 SAP HANA，或甚至是整台虛擬機器。 對於生產實例來說，是不可行的。

> [!NOTE]
> 在使用多個資料庫容器的部署中，SAP HANA 的基於磁片快照的備份需要至少發佈 HANA 2.0 SP04
> 

Azure 存儲不會在快照過程中連接到 VM 的多個磁片或卷中提供檔案系統一致性。 這意味著快照期間的應用程式一致性需要由應用程式傳遞，在這種情況下，SAP HANA 本身。 [SAP Note 2039883](https://launchpad.support.sap.com/#/notes/2039883)包含有關 SAP HANA 備份的重要資訊（按存儲快照進行）。 例如，對於 XFS 檔案系統，在啟動存儲快照之前必須運行**xfs\_凍結**以提供應用程式一致性（有關**xfs\_凍結**的詳細資訊，請參閱[xfs\_freeze（8） - Linux 人頁](https://linux.die.net/man/8/xfs_freeze)。

假設有個橫跨四個 Azure 虛擬磁碟的 XFS 檔案系統，下列步驟提供可表示 HANA 資料區域的一致快照集︰

1. 創建 HANA 資料快照準備
1. 凍結所有磁片/卷的檔案系統（例如，使用**xfs\_凍結**）
1. 在 Azure 上建立所有必要的 blob 快照集
1. 取消凍結檔案系統
1. 確認 HANA 資料快照（將刪除快照）

使用 Azure 備份的功能執行應用程式一致的快照備份時，需要#1步驟進行編碼/編寫腳本，以便進行快照前腳本。 Azure 備份服務將執行#2和#3步驟。 #4和#5步驟需要由快照後腳本中的代碼再次提供。 如果不使用 Azure 備份服務，還需要自行編寫代碼/腳本步驟#2並#3。
有關創建 HANA 資料快照的詳細資訊，請參閱以下文章：

- [HANA 資料快照](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/ac114d4b34d542b99bc390b34f8ef375.html
- 有關執行步驟#1的詳細資訊，請參閱[創建資料快照（本機 SQL）](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/9fd1c8bb3b60455caa93b7491ae6d830.html)的文章 
- 有關在步驟#5中根據需要確認/刪除 HANA 資料快照的詳細資訊，請參閱[文章"創建資料快照"（本機 SQL） 中](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/9fd1c8bb3b60455caa93b7491ae6d830.html) 

確認 HANA 快照集很重要。 由於「複製時寫入」的特性&quot;&quot;，在此快照集準備模式中，SAP HANA 可能不需要額外的磁碟空間。 而且除非確認 SAP HANA 快照集，否則不能開始新的備份。


### <a name="sap-hana-backup-scheduling-strategy"></a>SAP HANA 備份排程策略

SAP HANA 文章["規劃備份和修復原則](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)"中，提出了執行備份的基本計畫。 在定義 SAP HANA 的備份/修復原則和流程時，依靠有關 HANA 的 SAP 文檔以及您與其他 DBMS 的經驗。 不同類型的備份順序和保留期高度依賴于您需要提供的 SL。


### <a name="sap-hana-backup-encryption"></a>SAP HANA 備份的加密

SAP HANA 提供加密資料和記錄的功能。 如果 SAP HANA 資料和日誌未加密，則預設情況下不會加密備份。 但是，SAP HANA 提供了單獨的備份加密，如[SAP HANA 備份加密](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.03/en-US/5f837a57ce5e468d9db21c8683bc84da.html)中所述。 如果您正在運行較舊版本的 SAP HANA，則可能需要檢查備份加密是否是已提供的功能的一部分。  


## <a name="next-steps"></a>後續步驟
* [檔案層級的 SAP HANA Azure 備份](sap-hana-backup-file-level.md)說明以檔案為基礎的備份選項。
* 要瞭解如何在 Azure（大型實例）上建立 SAP HANA 的高可用性和災害復原計畫，請參閱[Azure 上的 SAP HANA（大型實例）高可用性和災害復原](hana-overview-high-availability-disaster-recovery.md)。
