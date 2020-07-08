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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "78255240"
---
# <a name="backup-guide-for-sap-hana-on-azure-virtual-machines"></a>Azure 虛擬機器上的 SAP HANA 備份指南

## <a name="getting-started"></a>開始使用

Azure 虛擬機器上執行的 SAP HANA 備份指南只描述 Azure 相關主題。 如需一般 SAP Hana 備份相關專案，請參閱 SAP Hana 檔。 我們希望您熟悉主體資料庫備份策略、原因和動機，以擁有音效和有效的備份策略，並瞭解您的公司對於備份程式的需求、備份和還原程式的保留期間。

各種 Azure VM 類型都正式支援 SAP HANA (例如 Azure M 系列)。 如需 SAP Hana 認證的 Azure Vm 和 HANA 大型實例單位的完整清單，請參閱[尋找認證的 IaaS 平臺](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)。 Microsoft Azure 提供了數個單位，其中 SAP Hana 在實體伺服器上執行非虛擬化。 此服務稱為「 [HANA 大型實例](hana-overview-architecture.md)」。 本指南不會涵蓋適用于 HANA 大型實例的備份程式和工具。 但即將限制為 Azure 虛擬機器。 如需有關使用 HANA 大型實例之備份/還原程式的詳細資訊，請閱讀「在[備份和還原](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore)中」一文。

本文的重點在於 Azure 虛擬機器上 SAP Hana 的三種備份可能性：

- 透過[Azure 備份服務](https://docs.microsoft.com/azure/backup/backup-overview)的 HANA 備份 
- HANA 備份至 Azure Linux 虛擬機器中的檔案系統 (請參閱[檔案層級的 SAP HANA Azure 備份](sap-hana-backup-file-level.md))
- 使用 Azure 儲存體 blob 快照集功能手動或 Azure 備份服務，以儲存體快照集為基礎的 HANA 備份


SAP HANA 提供備份 API，可讓第三方備份工具直接與 SAP HANA 整合。 Azure 備份 service 或[Commvault](https://azure.microsoft.com/resources/protecting-sap-hana-in-azure/)之類的產品會使用此專屬介面來觸發 SAP Hana 資料庫或重做記錄備份。 


如需瞭解 Azure 支援哪些 SAP 軟體的相關資訊，請參閱[azure 部署支援哪些 sap 軟體](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)。

## <a name="azure-backup-service"></a>Azure 備份服務

第一個顯示的案例是 Azure 備份服務使用 SAP Hana `backint` 介面，從 SAP Hana 資料庫執行串流備份的案例。 或者，您也可以使用 Azure 備份服務的一般功能，建立應用程式一致的磁片快照集，並將其傳送至 Azure 備份服務。

Azure 備份整合並通過認證，可使用稱為[backint](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5)的專屬 SAP Hana 介面，做為 SAP Hana 的備份解決方案。 如需解決方案的詳細資訊、其功能和可用的 Azure 區域，請參閱[Azure vm 上 SAP Hana 資料庫備份的支援對照表](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support)一文。 如需有關 Azure 備份 service for HANA 的詳細資訊和原則，請參閱[關於 Azure vm 中 SAP Hana 資料庫備份](https://docs.microsoft.com/azure/backup/sap-hana-db-about)的文章。 

利用 Azure 備份服務的第二種可能性是使用 Azure 進階儲存體的磁片快照集來建立應用程式一致備份。 其他 HANA 認證的 Azure 儲存體（例如[Azure Ultra 磁片](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-ultra-ssd)和[azure NetApp Files](https://azure.microsoft.com/services/netapp/) ）不會透過 Azure 備份服務來支援這類的快照集。 閱讀下列文章：

- [在 Azure 中規劃 VM 備份基礎結構](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction)
- [Azure Linux VM 的應用程式一致備份](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent) 

這一系列的活動會如下所示：

- Azure 備份需要執行預先快照集腳本，將應用程式（在此案例中 SAP Hana）置於一致的狀態
- 當此一致狀態已確認時，Azure 備份將會執行磁片快照集
- 完成快照集後，Azure 備份將會復原其在預先快照集腳本中執行的活動。
- 成功執行之後，Azure 備份會將資料串流至備份保存庫

如果 SAP Hana，大部分的客戶都會針對包含 SAP Hana 重做記錄檔的磁片區使用 Azure 寫入加速器。 Azure 備份服務會自動將這些磁片區從快照集排除。 此排除不會傷害 HANA 還原的能力。 雖然它會封鎖幾乎所有其他 SAP 支援 DBMS 的還原功能。

這種可能性的缺點是您必須開發自己的前置和後快照集腳本。 預先快照集腳本必須建立 HANA 快照集，並處理最終的例外狀況案例。 而快照集後腳本必須再次刪除 HANA 快照集。 如需必要邏輯的詳細資訊，請從[SAP 支援附注 #2039883](https://launchpad.support.sap.com/#/notes/2039883)開始。 本文中「取得儲存體快照集時 SAP Hana 資料一致性」一節的考慮，完全適用于這類備份。

> [!NOTE]
> 以磁片快照集為基礎的備份，用於在使用多個資料庫容器的部署中 SAP Hana，需要 HANA 2.0 SP04 的最低版本
> 

請參閱本檔稍後的儲存體快照集詳細資料。

![此圖顯示儲存目前 VM 狀態的兩種做法](media/sap-hana-backup-guide/azure-backup-service-for-hana.png)

## <a name="other-hana-backup-methods"></a>其他 HANA 備份方法
有三種其他的備份方法或路徑可以考慮：

- 針對以 Azure NetApp Files （及）為基礎的 NFS 共用進行備份。 再次及能夠建立您儲存備份所在磁片區的快照集。 由於您最終需要寫入備份的輸送量，此解決方案可能會成為昂貴的方法。 雖然可輕鬆建立，但因為 HANA 可以直接將備份寫入 Azure native NFS 共用
- 針對標準 SSD 或 Azure 進階儲存體的 VM 連接磁片執行 HANA 備份。 在下一個步驟中，您可以將這些備份檔案複製到 Azure Blob 儲存體。 此策略的價格可能非常吸引人
- 針對標準 SSD 或 Azure 進階儲存體的 VM 連接磁片執行 HANA 備份。 在下一個步驟中，磁片會定期取得快照。 第一個快照集之後，可以使用增量快照集來降低成本

![此圖顯示在 VM 中建立 SAP HANA 檔案備份的選項](media/sap-hana-backup-guide/other-hana-backup-paths.png)

此圖顯示在 VM 中建立 SAP HANA 檔案備份的選項，然後再使用不同的工具將 HANA 備份檔案儲存在其他地方。 不過，所有不涉及協力廠商備份服務或 Azure 備份服務的解決方案，都有一些常見的障礙。 其中一些可以列出，例如保留管理、自動還原程式，並提供自動時間點恢復，因為 Azure 備份服務或其他特定的協力廠商備份套件和服務提供。 許多協力廠商服務都可以在 Azure 上執行。 


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
與您的備份方法無關，針對不同的系統執行測試還原是絕對必要的。 這個方法能確保備份正確，以及備份和還原的內部程序如預期般運作。 雖然還原備份可能會因為其基礎結構需求而在內部部署上變得很困難，但在雲端中更容易完成，因為這是為了此目的而暫時提供必要的資源。 正確的是，HANA 提供的工具可檢查備份檔案的還原功能。 不過，經常還原練習的目的是要測試資料庫還原的程式，並向作業人員訓練該進程。

請記住，光是簡單執行還原，然後確認 HANA 有在執行是不夠的。 您應該執行資料表一致性檢查，以確定還原的資料庫是否正常。 SAP HANA 提供數種一致性檢查，如 [SAP Note 1977584](https://launchpad.support.sap.com/#/notes/1977584) 中所述。

有關資料表一致性檢查的相關資訊，也可以在 SAP 網站上的[資料表和目錄一致性檢查](https://help.sap.com/saphelp_hanaplatform/helpdata/en/25/84ec2e324d44529edc8221956359ea/content.htm#loio9357bf52c7324bee9567dca417ad9f8b)找到。

### <a name="pros-and-cons-of-hana-backup-versus-storage-snapshot"></a>比較 HANA 備份與儲存體快照集的優缺點

SAP 不特別偏好 HANA 備份或儲存體快照集之中的任何一個。 它會列出其各別的優缺點，讓您視情況和可用的儲存技術決定要使用哪一個 (請參閱[規劃備份和復原策略](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm))。

在 Azure 上，請注意 Azure blob 快照集功能不&#39;t 提供跨多個磁片的檔案系統一致性（請參閱搭配[PowerShell 使用 blob 快照](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)集）。 

此外，您必須了解經常使用 blob 快照集對費用的影響，如這篇文章中所述︰[了解快照如何產生費用](/rest/api/storageservices/understanding-how-snapshots-accrue-charges)— 它不像使用 Azure 虛擬磁碟那麼明顯。

### <a name="sap-hana-data-consistency-when-taking-storage-snapshots"></a>建立儲存體快照集時，SAP HANA 資料的一致性

如先前所述，在取得儲存體快照集時，描述 Azure 備份的快照集備份功能、檔案系統和應用程式一致性是強制的。 若要避免問題，最簡單的方法是關閉 SAP HANA，或甚至是整台虛擬機器。 生產實例不可行的專案。

> [!NOTE]
> 以磁片快照集為基礎的備份，用於在使用多個資料庫容器的部署中 SAP Hana，需要 HANA 2.0 SP04 的最低版本
> 

Azure 儲存體不會提供在快照集進程期間連接至 VM 的多個磁片或磁片區的檔案系統一致性。 這表示在快照集期間的應用程式一致性必須由應用程式傳遞，在此案例中 SAP Hana 本身。 [SAP 附注 2039883](https://launchpad.support.sap.com/#/notes/2039883)具有儲存體快照集 SAP Hana 備份的重要資訊。 例如，使用 XFS 檔案系統時，必須先執行**XFS \_ 凍結**，才能啟動儲存體快照集以提供應用程式一致性（如需**XFS \_ 凍結**的詳細資料，請參閱[XFS \_ 凍結（8）-Linux 手冊頁面](https://linux.die.net/man/8/xfs_freeze)）。

假設有個橫跨四個 Azure 虛擬磁碟的 XFS 檔案系統，下列步驟提供可表示 HANA 資料區域的一致快照集︰

1. 建立 HANA 資料快照集準備
1. 凍結所有磁片/磁片區的檔案系統（例如，使用**xfs \_ 凍結**）
1. 在 Azure 上建立所有必要的 blob 快照集
1. 取消凍結檔案系統
1. 確認 HANA 資料快照集（將刪除快照集）

當您使用 Azure 備份的功能來執行應用程式一致快照集備份時，您必須在中將步驟 #1 編碼/編寫成，以便進行預先快照集腳本。 Azure 備份服務會執行 #2 和 #3 的步驟。 後續快照集腳本中的程式碼必須再次提供步驟 #4 和 #5。 如果您不是使用 Azure 備份服務，您也需要自行撰寫程式碼/腳本步驟 #2 並 #3。
如需有關建立 HANA 資料快照集的詳細資訊，請參閱下列文章：

- [HANA 資料快照集](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/ac114d4b34d542b99bc390b34f8ef375.html
- 如需執行步驟 #1 的詳細資訊，請參閱[建立資料快照集（NATIVE SQL）](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/9fd1c8bb3b60455caa93b7491ae6d830.html)一文。 
- 如需在步驟 #5 中確認/刪除 HANA 資料快照集的詳細資訊，請參閱[建立資料快照集（NATIVE SQL）](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/9fd1c8bb3b60455caa93b7491ae6d830.html)一文。 

確認 HANA 快照集很重要。 由於「複製時寫入」的特性&quot;&quot;，在此快照集準備模式中，SAP HANA 可能不需要額外的磁碟空間。 而且除非確認 SAP HANA 快照集，否則不能開始新的備份。


### <a name="sap-hana-backup-scheduling-strategy"></a>SAP HANA 備份排程策略

[規劃備份和復原策略](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)的 SAP Hana 一文說明了執行備份的基本計畫。 在定義 SAP Hana 的備份/還原策略和程式時，依賴 HANA 的 SAP 檔和您對其他 DBMS 的使用體驗。 不同備份類型的順序和保留期限，會高度相依于您需要提供的 Sla。


### <a name="sap-hana-backup-encryption"></a>SAP HANA 備份的加密

SAP HANA 提供加密資料和記錄的功能。 如果 SAP Hana 資料和記錄檔未加密，則預設不會加密備份。 不過，SAP Hana 提供個別的備份加密，如[SAP Hana 備份加密](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.03/en-US/5f837a57ce5e468d9db21c8683bc84da.html)中所述。 如果您執行的是較舊的 SAP Hana 版本，您可能需要檢查備份加密是否為已提供功能的一部分。  


## <a name="next-steps"></a>後續步驟
* [檔案層級的 SAP HANA Azure 備份](sap-hana-backup-file-level.md)說明以檔案為基礎的備份選項。
* 若要瞭解如何建立高可用性，並規劃 Azure （大型實例）上 SAP Hana 的嚴重損壞修復，請參閱[azure 上的 SAP Hana （大型實例）高可用性和嚴重損壞修復](hana-overview-high-availability-disaster-recovery.md)。
