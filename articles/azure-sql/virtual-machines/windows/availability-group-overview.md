---
title: SQL Server AlwaysOn 可用性群組概觀
description: 本文介紹 Azure 虛擬機器上的 SQL Server AlwaysOn 可用性群組。
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: overview
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/07/2020
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: 213b973bfc93cb2237473b6bc4c7f1e138457409
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98131894"
---
# <a name="always-on-availability-group-on-sql-server-on-azure-vms"></a>Azure VM 上 SQL Server 的 Always On 可用性群組
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文介紹 Azure 虛擬機器 (VM) 上 SQL Server 的 AlwaysOn 可用性群組。 

## <a name="overview"></a>概觀

Azure 虛擬機器上的 Always On 可用性群組與[內部部署的 Always On 可用性群組](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)類似。 不過，由於虛擬機器裝載於 Azure 中，因此也有一些額外的考量，例如 VM 備援，以及在 Azure 網路上路由傳送流量。 

下圖說明 Azure VM 上 SQL Server 的可用性群組：

![可用性群組](./media/availability-group-overview/00-EndstateSampleNoELB.png)


## <a name="vm-redundancy"></a>VM 備援 

若要提高備援能力與高可用性，SQL Server VM 應位於相同的[可用性設定組](../../../virtual-machines/windows/tutorial-availability-sets.md#availability-set-overview)，或不同的[可用性區域](../../../availability-zones/az-overview.md)。

將一組 VM 放在相同的可用性設定組中，可防止因設備故障 (可用性設定組中的 VM 不會共用資源) 或更新 (可用性設定組中的 VM 不會同時更新) 而造成資料中心內的中斷。 可用性區域可保護整個資料中心避免故障，每個區域都代表一個區域內的資料中心集合。  藉由確保將資源放在不同的可用性區域，資料中心層級的中斷不會讓所有的 VM 離線。

建立 Azure VM 時，必須在設定可用性設定組和可用性區域之間進行選擇。  Azure VM 無法同時參與兩者。


## <a name="connectivity"></a>連線能力 

在傳統的內部部署中，用戶端會使用虛擬網路名稱 (VNN) 來連線到可用性群組接聽程式，而接聽程式會將流量路由傳送到可用性群組中適當的 SQL Server 複本。 不過，在 Azure 網路上路由傳送流量有額外的需求。 

使用 Azure VM 上的 SQL Server，將[負載平衡器](availability-group-vnn-azure-load-balancer-configure.md)設定為將流量路由傳送至您的可用性群組接聽程式，或者，如果您在 SQL Server 2019 CU8 和更新版本上，則可設定[分散式網路名稱 (DNN) 接聽程式](availability-group-distributed-network-name-dnn-listener-configure.md)以取代傳統的 VNN 可用性群組接聽程式。 


### <a name="vnn-listener"></a>VNN 接聽程式 

使用 [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md)，將來自用戶端的流量路由傳送至 Azure 網路上的傳統可用性群組虛擬網路名稱 (VNN) 接聽程式。 

負載平衡器會保有 VNN 接聽程式的 IP 位址。 如果您有多個可用性群組，則每個群組都需要一個 VNN 接聽程式。 一個負載平衡器可以支援多個接聽程式。

若要開始使用，請參閱[設定負載平衡器](availability-group-vnn-azure-load-balancer-configure.md)。 

### <a name="dnn-listener"></a>DNN 接聽程式

SQL Server 2019 CU8 引進分散式網路名稱 (DNN) 接聽程式的支援。 DNN 接聽程式會取代傳統的可用性群組接聽程式，並免除 Azure Load Balancer 在 Azure 網路上路由傳送流量的需求。 

DNN 接聽程式是 Azure 中建議的 HADR 連線解決方案，因為其可簡化部署、減少維護和成本，並在發生失敗時縮短容錯移轉時間。 

使用 DNN 接聽程式來取代現有的 VNN 接聽程式，或者將其與現有的 VNN 接聽程式搭配使用，讓您的可用性群組有兩個不同的連接點：一個使用 VNN 接聽程式名稱 (和連接埠 (若非預設))，另一個使用 DNN 接聽程式名稱和連接埠。 這對於想要避免負載平衡器容錯移轉延遲，但仍要利用相依於 VNN 接聽程式之 SQL Server 功能 (例如分散式可用性群組、Service Broker 或 Filestream) 的客戶非常有用。 若要深入了解，請參閱 [DNN 接聽程式和 SQL Server 功能互通性](availability-group-dnn-interoperability.md)

若要開始使用，請參閱[設定 DNN 接聽程式](availability-group-distributed-network-name-dnn-listener-configure.md)。


## <a name="deployment"></a>部署 

有多個選項可用於將可用性群組部署至 Azure VM 上的 SQL Server，有些選項比其他選項更加自動化。 

下表提供可用選項的比較：

| | Azure 入口網站 | Azure CLI/PowerShell | 快速入門範本 | 手動 |
|---------|---------|---------|---------|---------|
|**SQL Server 版本** |2016 + |2016 +|2016 +|2012 +|
|**SQL Server 版本** |Enterprise |Enterprise |Enterprise |Enterprise、Standard|
|**Windows Server 版本**| 2016 + | 2016 + | 2016 + | 全部|
|**為您建立叢集**|是|是 | 是 |否|
|**為您建立可用性群組** |是 |否|否|否|
|**獨立建立接聽程式和負載平衡器** |否|否|否|是|
|**可以使用這個方法建立 DNN 接聽程式嗎？**|否|否|否|是|
|**WSFC 仲裁組態**|雲端見證|雲端見證|雲端見證|全部|
|**多個區域進行災害復原** |否|否|否|是|
|**多重子網路支援** |是|是|是|是|
|**對現有 AD 的支援**|是|是|是|是|
|**相同區域中的多重地區進行災害復原**|是|是|是|是|
|**沒有 AD 的分散式 AG**|否|否|否|是|
|**沒有叢集的分散式 AG** |否|否|否|是|

如需詳細資訊，請參閱 [Azure 入口網站](availability-group-azure-portal-configure.md)、[Azure CLI / PowerShell](./availability-group-az-commandline-configure.md)、[快速入門範本](availability-group-quickstart-template-configure.md)和[手動設定](availability-group-manually-configure-prerequisites-tutorial.md)。

## <a name="considerations"></a>考量 

在 Azure IaaS VM 客體容錯移轉叢集上，建議每個伺服器 (叢集節點) 使用單一 NIC，並且使用單一子網路。 Azure 網路有實體備援，因此 Azure IaaS VM 客體叢集上不需要額外的 NIC 和子網路。 雖然叢集驗證報告會發出節點只能在單一網路上連線的警告，但您可以放心地在 Azure IaaS VM 客體容錯移轉叢集上忽略此警告。 

## <a name="next-steps"></a>後續步驟

請檢閱 [HADR 最佳做法](hadr-cluster-best-practices.md)，然後開始使用 [Azure 入口網站](availability-group-azure-portal-configure.md)、[Azure CLI / PowerShell](./availability-group-az-commandline-configure.md)、[快速入門範本](availability-group-quickstart-template-configure.md)或[手動方式](availability-group-manually-configure-prerequisites-tutorial.md)來部署您的可用性群組。

或者，您也可以在[多個區域](availability-group-manually-configure-multiple-regions.md)中部署 [無叢集的可用性群組](availability-group-clusterless-workgroup-configure.md)或可用性群組。
