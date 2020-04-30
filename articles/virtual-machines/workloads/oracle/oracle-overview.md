---
title: Microsoft Azure 上的 Oracle 解決方案 | Microsoft Docs
description: 瞭解在 Microsoft Azure 上部署 Oracle 應用程式和解決方案的選項，包括完全在 Azure 基礎結構上執行，或使用與 Oracle 雲端基礎結構（OCI）的跨雲端連線能力。
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/16/2020
ms.author: borisb
ms.openlocfilehash: c737189650d571fb62a770707e84ed15c5a37a57
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81870510"
---
# <a name="overview-of-oracle-applications-and-solutions-on-azure"></a>Azure 上的 Oracle 應用程式和解決方案總覽

本文介紹使用 Azure 基礎結構執行 Oracle 解決方案的功能。 另請參閱 Azure Marketplace 中可用[ORACLE VM 映射](oracle-vm-solutions.md)的詳細介紹，以及與[Oracle 雲端基礎結構（OCI）互連 Azure](oracle-oci-overview.md)的功能。

## <a name="oracle-databases-on-azure-infrastructure"></a>Azure 基礎結構上的 Oracle 資料庫

使用 Azure Marketplace 中提供的 Oracle Linux 映射上的 Oracle Database，在 Azure 基礎結構上執行 Oracle 資料庫：

* Oracle Database 12.1、12.2 和 18.3 Enterprise Edition 

* Oracle Database 12.1、12.2 和 18.3 Standard Edition 

您也可以選擇在 Azure 中提供的非 Oracle Linux 映射上設定 Oracle Database，以您在 Azure 中從頭建立的自訂映射作為解決方案，或從您的內部部署環境上傳自訂映射。

您可以選擇性地設定多個連接的磁片，並藉由安裝 Oracle 自動儲存管理（ASM）來改善資料庫效能。

## <a name="applications-on-oracle-linux-and-weblogic-server"></a>Oracle Linux 和 WebLogic 伺服器上的應用程式

在支援的 Oracle 作業系統上，于 Azure 中執行企業應用程式。 Azure Marketplace 提供下列影像：

* Oracle WebLogic Server 12.1。2

* Unbreakable Enterprise Kernel （UEK）6.8、6.9、6.10、7.3、7.4、7.5 和7.6 的 Oracle Linux 

## <a name="high-availability-and-disaster-recovery-options"></a>高可用性和嚴重損壞修復選項

* 使用 Azure 基礎結構上的 FSFO、[分區化](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html)或[黃金閘道](https://www.oracle.com/middleware/technologies/goldengate.html)，搭配[可用性區域](../../../availability-zones/az-overview.md)來設定[Oracle Data guard](https://docs.oracle.com/cd/B19306_01/server.102/b14239/concepts.htm#g1049956)、 [Active Data guard](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html)和區域中的高可用性。 您也可以跨多個 Azure 區域設定這些設定，以增加可用性和嚴重損壞修復。

* 使用[Azure Site Recovery](../../../site-recovery/site-recovery-overview.md)來協調和管理 Azure 中 Oracle Linux vm 和內部部署或實體伺服器的嚴重損壞修復。 

* 使用[Azure VMWare Solution](https://docs.microsoft.com/azure/vmware-cloudsimple/oracle-real-application-clusters/)或[FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/)，在 Azure 中啟用 Oracle REAL Application 叢集（RAC）。

## <a name="backup-oracle-workloads"></a>備份 Oracle 工作負載

* 使用[Azure 備份](https://docs.microsoft.com/azure/backup/backup-overview)備份 Oracle vm

* 使用 Oracle RMAN 來備份您的 Oracle Database，並選擇性地使用[Azure Blob 保險絲](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux)來掛接[高度備援的 Azure Blob 儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-redundancy)，並將您的 RMAN 備份寫入其中以增加復原能力。

## <a name="integration-of-azure-with-oci"></a>Azure 與 OCI 的整合

在 Azure 基礎結構中執行 Oracle 應用程式，並連接到 Oracle 雲端基礎結構（OCI）中的後端資料庫。 此解決方案使用下列功能： 

* **跨雲端網路**-使用 Azure ExpressRoute 和 Oracle FastConnect 之間提供的直接互連，在應用程式與資料庫層之間建立高頻寬、私人和低延遲的連線。
* **整合**式身分識別-在 Azure AD 和 Oracle IDCS 之間設定同盟身分識別，以建立解決方案的單一身分識別來源。 啟用單一登入來管理跨 OCI 和 Azure 的資源。

### <a name="deploy-oracle-applications-on-azure"></a>在 Azure 上部署 Oracle 應用程式

使用 Terraform 範本來設定 Azure 基礎結構和安裝 Oracle 應用程式。 

> [!IMPORTANT]
> 使用 Azure/Oracle 雲端互連解決方案時，Oracle 將會認證這些應用程式在 Azure 中執行（5月2020）。

* 電子商務套件
* JD Edwards EnterpriseOne
* PeopleSoft
* Oracle 零售應用程式
* Oracle Hyperion 財務管理

此外，在 Azure 中部署自訂應用程式，以使用 OCI 和其他 Azure 服務進行連接。

### <a name="set-up-oracle-databases-in-oci"></a>在 OCI 中設定 Oracle 資料庫

使用 Oracle Database 雲端服務（自發資料庫、RAC、Exadata、DBaaS、單一節點）搭配在 Azure 中執行的 Oracle 應用程式。 深入瞭解[OCI 資料庫選項](https://docs.cloud.oracle.com/iaas/Content/Database/Concepts/databaseoverview.htm)。 
 

## <a name="licensing"></a>授權

在 Azure 中部署 Oracle 應用程式是以「自備授權」模型為基礎。 假設您已適當授權使用 Oracle 軟體，而且目前已有與 Oracle 搭配的支援合約。 Oracle 已保證從內部部署至 Azure 的授權行動性。 請參閱 Oracle-Azure[常見問題](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html)。

## <a name="next-steps"></a>後續步驟

* 深入瞭解如何在 Azure 基礎結構中部署[ORACLE VM 映射](oracle-vm-solutions.md)。

* 深入瞭解如何[使用 OCI 與 Azure 互連](oracle-oci-overview.md)。

* 查看來自 Ignite 2019 的[Oracle On Azure 總覽課程](https://myignite.techcommunity.microsoft.com/sessions/82915)。 
