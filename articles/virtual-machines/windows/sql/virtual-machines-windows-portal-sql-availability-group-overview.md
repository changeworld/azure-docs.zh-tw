---
title: 可用性群組總覽
description: 本文介紹 Azure 虛擬機器上的「SQL Server 可用性群組」。
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/13/2017
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: 8119990ab4ab4a918e325976092100086a547aa4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74037489"
---
# <a name="introducing-sql-server-availability-groups-on-azure-virtual-machines"></a>Azure 虛擬機器上的 SQL Server 可用性群組簡介

本文介紹 Azure 虛擬機器上的 SQL Server 可用性群組。 

「Azure 虛擬機器」上的 Always On 可用性群組與內部部署的 Always On 可用性群組類似。 如需詳細資訊，請參閱 [Always On 可用性群組 (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx)。 

下圖顯示「Azure 虛擬機器」中完整「SQL Server 可用性群組」的各個部分。

![可用性群組](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

「Azure 虛擬機器」中「可用性群組」的主要差異在於 Azure 虛擬機器需要[負載平衡器](../../../load-balancer/load-balancer-overview.md)。 負載平衡器會保有可用性群組接聽程式的 IP 位址。 如果您有多個可用性群組，則每個群組都需要一個接聽程式。 一個負載平衡器可以支援多個接聽程式。

此外，在 Azure IaaS VM 來賓容錯移轉叢集上，我們建議每個伺服器（叢集節點）和單一子網都有一個 NIC。 Azure 網路有實體備援，因此 Azure IaaS VM 客體叢集上不需要額外的 NIC 和子網路。 雖然叢集驗證報告會發出節點只能在單一網路上連線的警告，但您可以放心地在 Azure IaaS VM 客體容錯移轉叢集上忽略此警告。 

若要提高冗余和高可用性，SQL Server Vm 應位於相同的[可用性設定組](virtual-machines-windows-portal-sql-availability-group-prereq.md#create-availability-sets)或不同的[可用性區域](/azure/availability-zones/az-overview)中。 

|  | Windows Server 版本 | SQL Server 版本 | SQL Server 版本 | WSFC 仲裁設定 | 具有多重區域的 DR | 多重子網支援 | 對現有 AD 的支援 | 具有多重區域相同區域的 DR | Dist-AG 不含 AD 網域的支援 | Dist-AG 不含叢集的支援 |  
| :------ | :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----|
| [SQL VM CLI](virtual-machines-windows-sql-availability-group-cli.md) | 2016 | 2017 </br>2016   | Ent | 雲端見證 | 否 | 是 | 是 | 是 | 否 | 否 |
| [快速入門範本](virtual-machines-windows-sql-availability-group-quickstart-template.md) | 2016 | 2017</br>2016  | Ent | 雲端見證 | 否 | 是 | 是 | 是 | 否 | 否 |
| [入口網站範本](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) | 2016 </br>2012 R2 | 2016</br>2014 | Ent | 檔案共用 | 否 | 否 | 否 | 否 | 否 | 否 |
| [手動](virtual-machines-windows-portal-sql-availability-group-prereq.md) | 全部 | 全部 | 全部 | 全部 | 是 | 是 | 是 | 是 | 是 | 是 |
| &nbsp; | &nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |

當您已準備好在「Azure 虛擬機器」上建置 SQL Server 可用性群組時，請參考這些教學課程。

## <a name="manually-with-azure-cli"></a>使用 Azure CLI 手動進行
建議使用 Azure CLI 來設定和部署可用性群組，因為這是最簡單且部署速度的最佳選擇。 在 Azure CLI 中，建立 Windows 容錯移轉叢集、將 SQL Server Vm 加入叢集，以及建立接聽程式和內部 Load Balancer 都可以在30分鐘內完成。 此選項仍然需要手動建立可用性群組，但會將所有其他必要的設定步驟自動化。 

如需詳細資訊，請參閱[使用 AZURE SQL VM CLI 在 AZURE VM 上設定 SQL Server 的 Always On 可用性群組](virtual-machines-windows-sql-availability-group-cli.md)。 

## <a name="automatically-with-azure-quickstart-templates"></a>使用 Azure 快速入門範本自動進行
Azure 快速入門範本會利用 SQL VM 資源提供者來部署 Windows 容錯移轉叢集、將 SQL Server 的 Vm 加入該叢集、建立接聽程式，以及設定內部 Load Balancer。 此選項仍然需要手動建立可用性群組和內部 Load Balancer （ILB），但會自動化並簡化所有其他必要的設定步驟（包括 ILB 的設定）。 

如需詳細資訊，請參閱[使用 Azure 快速入門範本在 AZURE VM 上設定 SQL Server 的 Always On 可用性群組](virtual-machines-windows-sql-availability-group-quickstart-template.md)。


## <a name="automatically-with-an-azure-portal-template"></a>使用 Azure 入口網站範本自動進行

[在 Azure VM 中自動設定 Always On 可用性群組 - Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)


## <a name="manually-in-azure-portal"></a>在 Azure 入口網站中手動

您也可以自行建立虛擬機器，而不使用範本。 首先，請完成必要條件，然後再建立可用性群組。 請參閱下列主題： 

- [設定 Azure 虛擬機器上 SQL Server Always On 可用性群組的必要條件](virtual-machines-windows-portal-sql-availability-group-prereq.md)

- [建立 Always On 可用性群組以改善可用性和災害復原](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## <a name="next-steps"></a>後續步驟

[在不同區域中的 Azure 虛擬機器上設定 SQL Server Always On 可用性群組](virtual-machines-windows-portal-sql-availability-group-dr.md)
