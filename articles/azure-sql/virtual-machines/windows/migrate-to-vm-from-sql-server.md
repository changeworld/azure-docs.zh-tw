---
title: 將 SQL Server 資料庫移轉至虛擬機器上的 SQL Server | Microsoft Docs
description: 深入瞭解如何將內部部署使用者資料庫移轉到 Azure 虛擬機器中的 SQL Server。
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 00fd08c6-98fa-4d62-a3b8-ca20aa5246b1
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.tgt_pltfrm: vm-windows-sql-server
ms.topic: how-to
ms.date: 08/18/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 83b29252038f88bf8b81299303442abd0cc36814
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91298655"
---
# <a name="migrate-a-sql-server-database-to-sql-server-on-an-azure-virtual-machine"></a>將 SQL Server 資料庫移轉到 Azure 虛擬機器中的 SQL Server

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

有多個方法可將內部部署 SQL Server 使用者資料庫移轉至 Azure 虛擬機器 (VM) 中的 SQL Server。 本文將簡短討論各種方法，並針對各種案例建議最佳的方法。


[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

  > [!NOTE]
  > SQL Server 2008 和 SQL Server 2008 R2 已接近其內部部署執行個體的[結束支援生命週期](https://www.microsoft.com/sql-server/sql-server-2008)。 若要擴充支援，您可以將 SQL Server 執行個體遷移至 Azure VM，或購買擴充的安全性更新以將其保留在內部部署。 如需詳細資訊，請參閱[使用 Azure 擴充對 SQL Server 2008 和 2008 R2 的支援](sql-server-2008-extend-end-of-support.md)

## <a name="what-are-the-primary-migration-methods"></a>主要的移轉方法有哪些？

主要的移轉方法如下：

* 使用壓縮執行在內部部署備份，然後手動將備份檔案複製到 Azure VM。
* 執行備份至 URL，然後從 URL 還原到 Azure VM。
* 中斷資料和記錄檔的連結，將資料與記錄檔複製到 Azure Blob 儲存體，然後從 URL 將其連結至 Azure VM 內的 SQL Server。
* 將內部部署實體機器轉換為 Hyper-V VHD，接著上傳至 Azure Blob 儲存體，然後使用上傳的 VHD 部署為新 的VM。
* 使用 Windows 匯入/匯出服務寄送硬碟機。
* 如果您有內部部署的 AlwaysOn 可用性群組部署，請使用[加入 Azure 複本精靈](../../../virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-onprem-availability.md)在 Azure 中建立複本，然後進行容錯移轉，將使用者指向 Azure 資料庫執行個體。
* 使用 SQL Server [異動複寫](https://msdn.microsoft.com/library/ms151176.aspx)將 Azure SQL Server 執行個體設定為訂閱者，然後停用複寫，將使用者指向 Azure 資料庫執行個體。

> [!TIP]
> 您也可以使用這些相同的技巧，在 Azure 中的 SQL Server VM 之間移動資料庫。 例如，若要將 SQL Server 資源庫映像 VM 從一個版本升級至另一版本，並沒有支援的方法。 在此情況下，您應該使用新的版本建立新的 SQL Server VM，然後使用本文其中一個移轉技術來移動資料庫。 

## <a name="choose-a-migration-method"></a>選擇移轉方法

為了達到最佳的資料傳輸效能，請使用壓縮的備份檔案將資料庫檔案遷移至 Azure VM。

若要將進行資料庫移轉程序時的停機時間縮到最短，請使用 AlwaysOn 選項或異動複寫選項。

如果無法使用上述方法，則請以手動方式移轉資料庫。 一般來說，您會從資料庫備份開始，將資料庫備份的複本放在 Azure 中，然後還原資料庫。 您也可以將資料庫檔案本身複製到 Azure，然後再連接它們。 若要將資料庫移轉到 Azure VM，有數種方法可以完成此手動程序。

> [!NOTE]
> 當您從舊版 SQL Server 升級至 SQL Server 2014 或 SQL Server 2016 時，您應該考慮是否需要變更。 我們建議您先解決所有新版本 SQL Server 不支援的功能相依性作為移轉專案的一部分。 如需有關支援版本和案例的詳細資訊，請參閱 [升級至 SQL Server](https://msdn.microsoft.com/library/bb677622.aspx)。

下表會列出每個主要的移轉方法，並討論何時使用每個方法是最合適的。

| 方法 | 來源資料庫版本 | 目的地資料庫版本 | 來源資料庫備份的大小條件約束 | 注意 |
| --- | --- | --- | --- | --- |
| [使用壓縮執行內部部署備份，並手動將備份檔案複製到 Azure 虛擬機器](#back-up-and-restore) |SQL Server 2005 或更新版本 |SQL Server 2005 或更新版本 |[Azure VM 儲存體限制](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) | 這項技術很簡單且經過妥善測試，可在電腦之間移動資料庫。 |
| [執行備份至 URL 並從 URL 還原到 Azure 虛擬機器](#backup-to-url-and-restore-from-url) |SQL Server 2012 SP1 CU2 或更新版本 | SQL Server 2012 SP1 CU2 或更新版本 | < 12.8 TB 適用於 SQL Server 2016，否則為 < 1 TB | 此法是使用 Azure 儲存體將備份檔案移至 VM 的另一種方式。 |
| [中斷連結並將資料和記錄檔複製到 Azure Blob 儲存體，然後從 URL 連結至 Azure 虛擬機器中的 SQL Server](#detach-and-attach-from-a-url) | SQL Server 2005 或更新版本 |SQL Server 2014 或更新版本 | [Azure VM 儲存體限制](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) | 當您打算 [使用 Azure Blob 儲存體服務儲存這些檔案](https://msdn.microsoft.com/library/dn385720.aspx) ，並將其連接至在 Azure VM 中執行的 SQL Server，尤其是針對極大資料庫時，請使用這個方法。 |
| [將內部部署機器轉換為 Hyper-V VHD，接著上傳至 Azure Blob 儲存體，然後使用上傳的 VHD 部署新的虛擬機器](#convert-to-a-vm-upload-to-a-url-and-deploy-as-a-new-vm) |SQL Server 2005 或更新版本 |SQL Server 2005 或更新版本 |[Azure VM 儲存體限制](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |當您在遷移您要在舊版 SQL Server 上執行的資料庫時，或在遷移與其他使用者資料庫和/或系統資料庫相依的資料庫時，將系統和使用者資料庫移轉時，請使用此 [SQL Server 授權](../../../azure-sql/azure-sql-iaas-vs-paas-what-is-overview.md)。 |
| [使用 Windows 匯入/匯出服務寄送硬碟機](#ship-a-hard-drive) |SQL Server 2005 或更新版本 |SQL Server 2005 或更新版本 |[Azure VM 儲存體限制](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |當手動複製方法太慢，例如包含極大資料庫時，請使用 [Windows 匯入/匯出服務](../../../storage/common/storage-import-export-service.md) |
| [使用新增 Azure 複本精靈](../../../virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-onprem-availability.md) |SQL Server 2012 或更新版本 |SQL Server 2012 或更新版本 |[Azure VM 儲存體限制](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |可將停機時間縮到最短，請在您有內部部署的 AlwaysOn 部署時使用 |
| [使用 SQL Server 異動複寫](https://msdn.microsoft.com/library/ms151176.aspx) |SQL Server 2005 或更新版本 |SQL Server 2005 或更新版本 |[Azure VM 儲存體限制](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |當您需要將停機時間降到最低，且沒有 Always On 內部部署時，請使用 |

## <a name="back-up-and-restore"></a>備份及還原

備份壓縮的資料庫，並將備份複製到 VM，接著還原資料庫。 由於 VM 磁碟的大小上限為 1 TB，如果您的備份檔案大於 1 TB，您必須建立等量磁碟區。 您可以使用下列一般步驟，使用此手動方法來移轉使用者資料庫：

1. 執行完整資料庫備份至內部部署位置。
2. 建立或上傳具有所需 SQL Server 版本的虛擬機器。
3. 根據您的需求設定連線。 請參閱 [連線到 Azure 上的 SQL Server 虛擬機器 (Resource Manager)](ways-to-connect-to-sql.md)。
4. 使用遠端桌面、Windows 檔案總管或從命令提示字元複製命令，將備份檔案 (s) 複製到您的 VM。

## <a name="backup-to-url-and-restore-from-url"></a>備份至 URL 並從 URL 還原

您可以使用 [ [備份至 url](https://msdn.microsoft.com/library/dn435916.aspx) ]，然後從 url 還原至 VM，而不是備份至本機檔案。 SQL Server 2016 支援等量分割備份組。 建議用來提升效能，並且在超過每個 Blob 的大小限制時為必備條件。 對於大型資料庫，建議使用 [Windows 匯入/匯出服務](../../../storage/common/storage-import-export-service.md) 。

## <a name="detach-and-attach-from-a-url"></a>從 URL 中斷連結和從 URL 連結

中斷資料庫和記錄檔案的連線，並將其傳輸至 [Azure Blob 儲存體](https://msdn.microsoft.com/library/dn385720.aspx)。 然後在您的 Azure VM 上從 URL 連結資料庫。 如果您想要將實體資料庫檔案存放在 Blob 儲存體中，這對非常大型的資料庫可能很有用，請使用這個方法。 您可以使用下列一般步驟，使用此手動方法來移轉使用者資料庫：

1. 從內部部署資料庫執行個體中斷連結資料庫檔案。
2. 使用 [AZCopy 命令列公用程式](../../../storage/common/storage-use-azcopy.md)，將中斷連結的資料庫檔案複製到 Azure Blob 儲存體。
3. 將資料庫檔案從 Azure URL 連結至 Azure VM 中的 SQL Server 執行個體。

## <a name="convert-to-a-vm-upload-to-a-url-and-deploy-as-a-new-vm"></a>轉換為 VM、上傳至 URL 並部署為新的 VM

您可以使用這個方法，將內部部署 SQL Server 執行個體中的所有系統和使用者資料庫移轉至 Azure 虛擬機器。 您可以使用下列一般步驟，使用此手動方法來移轉整個 SQL Server 執行個體：

1. 將實體或虛擬機器轉換為 Hyper-V VHD。
2. 使用 [Add-AzureVHD Cmdlet](https://msdn.microsoft.com/library/windowsazure/dn495173.aspx)，將 VHD 檔案上傳至 Azure 儲存體。
3. 使用上傳的 VHD 來部署新的虛擬機器。

> [!NOTE]
> 若要移轉整個應用程式，請考慮使用 [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md)。

## <a name="ship-a-hard-drive"></a>寄送硬碟

透過網路進行上傳所費不貲或不可行時，請使用 [Windows 匯入/匯出服務方法](../../../storage/common/storage-import-export-service.md) ，將大量檔案資料移轉至 Azure Blob 儲存體。 您可以使用此服務，將包含該資料的一個或多個硬碟送至 Azure 資料中心，而您的資料將會在此上傳至儲存體帳戶。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱 [Azure 虛擬機器上的 SQL Server 概觀](sql-server-on-azure-vm-iaas-what-is-overview.md)。

> [!TIP]
> 如果您有 SQL Server 虛擬機器的相關問題，請參閱[常見問題集](frequently-asked-questions-faq.md)。

如需從擷取的映像建立 Azure 虛擬機器中 SQL Server 的指示，請參閱 CSS SQL Server 工程師部落格上的[從擷取之映像複製 Azure SQL 虛擬機器的秘訣和竅門](https://blogs.msdn.microsoft.com/psssql/2016/07/06/tips-tricks-on-cloning-azure-sql-virtual-machines-from-captured-images/)。

