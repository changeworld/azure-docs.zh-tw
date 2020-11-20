---
title: Microsoft Azure 上的 Oracle 解決方案 | Microsoft Docs
description: 深入了解可在 Microsoft Azure 上部署 Oracle 應用程式和解決方案的選項，包括完全在 Azure 基礎結構上執行，或者搭配 Oracle 雲端基礎結構 (OCI) 使用跨雲端的連線能力。
services: virtual-machines-linux
documentationcenter: ''
author: dbakevlar
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/05/2020
ms.author: kegorman
ms.openlocfilehash: d9b032bbd89f8b6d300650ac54567b07ae3307b8
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94948386"
---
# <a name="overview-of-oracle-applications-and-solutions-on-azure"></a>Azure 上的 Oracle 應用程式和解決方案概觀

本文會介紹多種使用 Azure 基礎結構執行 Oracle 解決方案的功能。 另請參閱 Azure Marketplace 提供的 [WebLogic Server Azure 應用程式](oracle-weblogic.md)、[Oracle VM 映像](oracle-vm-solutions.md)，以及 [Azure 與 Oracle 雲端基礎結構 (OCI) 互連](oracle-oci-overview.md)功能的詳細介紹。

## <a name="oracle-databases-on-azure-infrastructure"></a>Azure 基礎結構上的 Oracle 資料庫

使用 Azure Marketplace 所提供 Oracle Linux 映像上的 Oracle Database，在 Azure 基礎結構上執行 Oracle 資料庫：

* Oracle Database 12.1、12.2 和 18.3 Enterprise Edition 

* Oracle Database 12.1、12.2 和 18.3 Standard Edition

* Oracle 資料庫 19.3

您也可以選擇在 Azure 提供的非 Oracle Linux 映像上設定 Oracle Database，在 Azure 內從頭建立的自訂映像做為解決方案的基礎，或從您的內部部署環境上傳自訂映像。

也可以自由選擇是否要設定多個附加磁碟，以及安裝 Oracle Automated Storage Management (ASM) 來改善資料庫效能。

## <a name="weblogic-server-with-azure-service-integrations"></a>具有 Azure 服務整合功能的 WebLogic 伺服器

您可以選擇各式各樣的 WebLogic Server Azure 應用程式來加速您的雲端旅程。  有數個預先設定好的 Azure 服務整合功能可供使用，包括資料庫、Azure 應用程式閘道，以及 Azure Active Directory。

## <a name="applications-on-oracle-linux-and-weblogic-server"></a>Oracle Linux 和 WebLogic 伺服器上的應用程式

在 Azure 支援的 Oracle Linux 映射上執行企業應用程式。 Azure Marketplace 提供以下虛擬機器映像：

* Oracle WebLogic Server 12.1.2

* Oracle Linux (含 Unbreakable Enterprise Kernel (UEK) 6.8、6.9、6.10、7.3 至 7.7、8.0 及 8.1 版)。 

## <a name="high-availability-and-disaster-recovery-options"></a>高可用性和災害復原選項

* 在 Azure 基礎結構上設定 [Oracle Data Guard](https://docs.oracle.com/cd/B19306_01/server.102/b14239/concepts.htm#g1049956)[Active Data Guard with FSFO](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html)、[Sharding](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html) 或 [Golden Gate](https://www.oracle.com/middleware/technologies/goldengate.html)，並搭配使用[可用性區域](../../../availability-zones/az-overview.md)，能讓區域內達到高可用性。 您也可以跨多個 Azure 區域設定上述組態，提升可用性和災害復原能力。

* 使用 [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) 來協調和管理 Azure 和實體伺服器中 Oracle Linux vm 的嚴重損壞修復。 

* 使用 [Azure VMware Solution](../../../vmware-cloudsimple/oracle-real-application-clusters.md) 或 [FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/)在 Azure 中啟用 Oracle REAL Application 叢集 (RAC) 。

## <a name="backup-oracle-workloads"></a>備份 Oracle 工作負載

* 使用 [Azure 備份](../../../backup/backup-overview.md)來備份 Oracle VM

* 使用 Oracle RMAN 備份 Oracle Database，也可自由選擇使用 [Azure Blob Fuse](../../../storage/blobs/storage-how-to-mount-container-linux.md) 裝載[高度冗餘的 Azure Blob 儲存體帳戶](../../../storage/common/storage-redundancy.md)，並寫入您的 RMAN 備份以提升復原能力。

## <a name="integration-of-azure-with-oci"></a>Azure 與 OCI 的整合

可在 Azure 基礎結構中執行 Oracle 應用程式，並連接到 Oracle 雲端基礎結構 (OCI) 中的後端資料庫。 此解決方案會使用下列功能： 

* **跨雲端網路** - 使用 Azure ExpressRoute 和 Oracle FastConnect 之間可用的直接互連，在應用程式與資料庫層之間建立高頻寬、低延遲的私人連線。
* **整合式身分識別** - 設定 Azure AD 和 Oracle IDCS 之間的同盟身分識別，建立單一身分識別來源供解決方案使用。 啟用單一登入跨 OCI 和 Azure 管理資源。

### <a name="deploy-oracle-applications-on-azure"></a>在 Azure 上部署 Oracle 應用程式

使用 Terraform 範本設定 Azure 基礎結構並安裝 Oracle 應用程式。 

當透過 Azure/Oracle 雲端互連解決方案連線至 Oracle 資料庫時，Oracle 已認證這些應用程式在 Azure 中執行：

* E-Business Suite
* JD Edwards EnterpriseOne
* PeopleSoft
* Oracle Retail 應用程式
* Oracle Hyperion Financial Management

也可在 Azure 中部署自訂應用程式，與 OCI 和其他 Azure 服務連線。

### <a name="set-up-oracle-databases-in-oci"></a>在 OCI 中設定 Oracle 資料庫

使用 Oracle Database 的雲端服務 (自主資料庫、RAC、Exadata、DBaaS、單一節點) 與在 Azure 中執行的 Oracle 軟體搭配運作。 深入了解 [OCI 資料庫選項](https://docs.cloud.oracle.com/iaas/Content/Database/Concepts/databaseoverview.htm)。 
 

## <a name="licensing"></a>授權

在 Azure 中部署 Oracle 應用程式是以「自備授權」的模式為基礎。 會假設您已取得使用 Oracle 軟體的適當授權，且與 Oracle 之間已擁有支援合約。 Oracle 已保證從內部部署至 Azure 的授權行動性。 請參閱 Oracle-Azure [常見問題](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html)。

## <a name="next-steps"></a>後續步驟

* 深入了解 [WebLogic Server Azure 應用程式](oracle-weblogic.md) (英文) 和支援的 Azure 服務整合功能。

* 深入了解如何在 Azure 基礎結構中部署 [Oracle VM 映像](oracle-vm-solutions.md)。

* 深入了解如何[整合 Azure 與 OCI](oracle-oci-overview.md)。

* 查看 Ignite 2019 [Azure 上的 Oracle 概觀研討會](https://myignite.techcommunity.microsoft.com/sessions/82915) (英文)。 
