---
title: Microsoft Azure 上的 Oracle 解決方案 | Microsoft Docs
description: 瞭解在 Microsoft Azure 上部署 Oracle 應用程式和解決方案的選項，包括完全在 Azure 基礎結構上運行或使用與 Oracle 雲基礎結構 （OCI） 的跨雲連接。
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/04/2019
ms.author: rogirdh
ms.openlocfilehash: 6ab41182d59f8c96edfca4854aad5f9a13f53436
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74806604"
---
# <a name="overview-of-oracle-applications-and-solutions-on-azure"></a>Azure 上的 Oracle 應用程式和解決方案概述

本文介紹了使用 Azure 基礎結構運行 Oracle 解決方案的功能。 另請參閱 Azure 應用商店中可用[Oracle VM 映射](oracle-vm-solutions.md)的詳細介紹，以及將[Azure 與 Oracle 雲基礎結構 （OCI） 互連的](oracle-oci-overview.md)預覽功能。

## <a name="oracle-databases-on-azure-infrastructure"></a>Azure 基礎結構上的 Oracle 資料庫

使用 Azure 應用商店中提供的 Oracle Linux 映射上的 Oracle 資料庫在 Azure 基礎結構上運行 Oracle 資料庫：

* Oracle 資料庫 12.1、12.2 和 18.3 企業版 

* Oracle 資料庫 12.1、12.2 和 18.3 標準版 

您還可以選擇在 Azure 中可用的非 Oracle Linux 映射上設置 Oracle 資料庫，將解決方案基於在 Azure 中從頭開始創建的自訂映射，或者從本地環境上載自訂映射。

可選使用多個連接磁片進行配置，並通過安裝 Oracle 自動存儲管理 （ASM） 提高資料庫性能。

## <a name="applications-on-oracle-linux-and-weblogic-server"></a>甲骨文Linux和WebLogic伺服器上的應用程式

在 Azure 中支援 Oracle 作業系統上運行企業應用程式。 Azure 應用商店中提供了以下映射：

* 甲骨文 WebLogic 伺服器 12.1.2

* 甲骨文 Linux 與牢不可破的企業內核 （UEK） 6.8， 6.9， 6.10， 7.3， 7.4， 7.5 和 7.6 

## <a name="high-availability-and-disaster-recovery-options"></a>高可用性和災害復原選項

* 在 Azure 基礎架構上配置[Oracle 資料防護](https://docs.oracle.com/cd/B19306_01/server.102/b14239/concepts.htm#g1049956)、[使用 FSFO、](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html)[分片](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html)或[金門](https://www.oracle.com/middleware/technologies/goldengate.html)的主動資料防護，並結合[可用性區域](../../../availability-zones/az-overview.md)，在區域內提供高可用性。 您還可以跨多個 Azure 地區設定這些配置，以增加可用性和災害復原。

* 使用[Azure 網站恢復](../../../site-recovery/site-recovery-overview.md)來協調和管理 Azure 中的 Oracle Linux VM 以及本地或物理伺服器的災害復原。 

* 使用[Azure VMWare 解決方案](https://docs.azure.cloudsimple.com/oracle-rac/)或[FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/)在 Azure 中啟用 Oracle 實際應用程式群集 （RAC）。

## <a name="backup-oracle-workloads"></a>備份 Oracle 工作負載

* 使用[Azure 備份](https://docs.microsoft.com/azure/backup/backup-overview)備份 Oracle VM

* 使用 Oracle RMAN 備份 Oracle 資料庫，並選擇性地使用[Azure Blob Fuse](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux)裝載[高度紅的 Azure Blob 存儲帳戶](https://docs.microsoft.com/azure/storage/common/storage-redundancy)，並將 RMAN 備份寫入該帳戶，以增加恢復能力。

## <a name="integration-of-azure-with-oci-preview"></a>將 Azure 與 OCI 集成（預覽版）

在 Azure 基礎結構中運行 Oracle 應用程式，連接到 Oracle 雲基礎結構 （OCI） 中的後端資料庫。 此解決方案使用以下功能： 

* **跨雲網路**- 使用 Azure ExpressRoute 和 Oracle FastConnect 之間的直接互連在應用程式和資料庫層之間建立高頻寬、私有和低延遲連接。
* **集成標識**- 在 Azure AD 和 Oracle IDCS 之間設置聯合標識，為解決方案創建單個標識源。 啟用單一登入以跨 OCI 和 Azure 管理資源。

### <a name="deploy-oracle-applications-on-azure"></a>在 Azure 上部署 Oracle 應用程式

使用 Terraform 範本設置 Azure 基礎結構，並安裝經過驗證和支援的 Oracle 應用程式以在跨雲配置中運行：

* 電子商務套件
* JD Edwards EnterpriseOne
* PeopleSoft
* 甲骨文零售應用程式
* 甲骨文海龍財務管理

還在 Azure 中部署與 OCI 和其他 Azure 服務連接的自訂應用程式。

### <a name="set-up-oracle-databases-in-oci"></a>在 OCI 中設置 Oracle 資料庫

將 Oracle 資料庫雲服務（自治資料庫、RAC、Exadata、DBaaS、單節點）與在 Azure 中運行的 Oracle 應用程式結合使用。 瞭解有關[OCI 資料庫選項](https://docs.cloud.oracle.com/iaas/Content/Database/Concepts/databaseoverview.htm)的詳細資訊。 
 

## <a name="licensing"></a>授權

在 Azure 中部署 Oracle 應用程式基於"自帶許可證"模型。 假定您擁有使用 Oracle 軟體的適當許可，並且您與 Oracle 有最新的支援協定。 Oracle 已保證從內部部署至 Azure 的授權行動性。 請參閱 Oracle-Azure[常見問題解答](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html)。

## <a name="next-steps"></a>後續步驟

* 瞭解有關在 Azure 基礎結構中部署[Oracle VM 映射](oracle-vm-solutions.md)的詳細資訊。

* 詳細瞭解如何將[Azure 與 OCI 互連](oracle-oci-overview.md)。

* 查看 2019 年 Ignite [Azure 概述會話中的 Oracle。](https://myignite.techcommunity.microsoft.com/sessions/82915) 
