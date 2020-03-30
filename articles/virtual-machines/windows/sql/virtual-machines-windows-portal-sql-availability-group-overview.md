---
title: 可用性組概述
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74037489"
---
# <a name="introducing-sql-server-availability-groups-on-azure-virtual-machines"></a>在 Azure 虛擬機器上引入 SQL Server 可用性組

本文介紹 Azure 虛擬機器上的 SQL Server 可用性群組。 

「Azure 虛擬機器」上的 Always On 可用性群組與內部部署的 Always On 可用性群組類似。 如需詳細資訊，請參閱 [Always On 可用性群組 (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx)。 

下圖顯示「Azure 虛擬機器」中完整「SQL Server 可用性群組」的各個部分。

![可用性群組](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

「Azure 虛擬機器」中「可用性群組」的主要差異在於 Azure 虛擬機器需要[負載平衡器](../../../load-balancer/load-balancer-overview.md)。 負載平衡器會保有可用性群組接聽程式的 IP 位址。 如果您有多個可用性群組，則每個群組都需要一個接聽程式。 一個負載平衡器可以支援多個接聽程式。

此外，在 Azure IaaS VM 來賓容錯移轉叢集上，我們建議每個伺服器（叢集節點）和單個子網使用單個 NIC。 Azure 網路有實體備援，因此 Azure IaaS VM 客體叢集上不需要額外的 NIC 和子網路。 雖然叢集驗證報告會發出節點只能在單一網路上連線的警告，但您可以放心地在 Azure IaaS VM 客體容錯移轉叢集上忽略此警告。 

為了提高冗余性和高可用性，SQL Server VM 應位於相同的[可用性集中](virtual-machines-windows-portal-sql-availability-group-prereq.md#create-availability-sets)，或者位於不同的[可用性區域](/azure/availability-zones/az-overview)。 

|  | Windows 伺服器版本 | SQL Server 版本 | SQL Server 版本 | WSFC 仲裁設定 | 帶多區域的 DR | 多子網支援 | 支援現有 AD | 多區域相同區域的 DR | 無 AD 域的 Dist-AG 支援 | 無群集的 Dist-AG 支援 |  
| :------ | :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----|
| [SQL VM CLI](virtual-machines-windows-sql-availability-group-cli.md) | 2016 | 2017 </br>2016   | 恩特 | 雲端見證 | 否 | 是 | 是 | 是 | 否 | 否 |
| [快速入門範本](virtual-machines-windows-sql-availability-group-quickstart-template.md) | 2016 | 2017</br>2016  | 恩特 | 雲端見證 | 否 | 是 | 是 | 是 | 否 | 否 |
| [門戶範本](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) | 2016 </br>2012 R2 | 2016</br>2014 | 恩特 | 檔案共用 | 否 | 否 | 否 | 否 | 否 | 否 |
| [手動](virtual-machines-windows-portal-sql-availability-group-prereq.md) | 全部 | 全部 | 全部 | 全部 | 是 | 是 | 是 | 是 | 是 | 是 |
| &nbsp; | &nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |

當您已準備好在「Azure 虛擬機器」上建置 SQL Server 可用性群組時，請參考這些教學課程。

## <a name="manually-with-azure-cli"></a>手動使用 Azure CLI
建議使用 Azure CLI 配置和部署可用性組，因為它是在簡單性和部署速度方面最好的選項。 使用 Azure CLI，創建 Windows 容錯移轉叢集、將 SQL Server VM 加入群集以及創建攔截器和內部負載等化器都可以在 30 分鐘內實現。 此選項仍然需要手動創建可用性組，但自動執行所有其他必要的配置步驟。 

有關詳細資訊，請參閱使用[Azure SQL VM CLI 在 Azure VM 上為 SQL Server 配置始終打開可用性組](virtual-machines-windows-sql-availability-group-cli.md)。 

## <a name="automatically-with-azure-quickstart-templates"></a>使用 Azure 快速入門範本自動
Azure 快速啟動範本利用 SQL VM 資來源提供者部署 Windows 容錯移轉叢集、將 SQL Server VM 加入到該群集、創建攔截器以及配置內部負載等化器。 此選項仍然需要手動創建可用性組和內部負載等化器 （ILB），但自動和簡化所有其他必要的配置步驟（包括 ILB 的配置）。 

有關詳細資訊，請參閱使用[Azure 快速入門範本在 Azure VM 上為 SQL Server 配置始終打開可用性組](virtual-machines-windows-sql-availability-group-quickstart-template.md)。


## <a name="automatically-with-an-azure-portal-template"></a>使用 Azure 門戶範本自動

[在 Azure VM 中自動設定 Always On 可用性群組 - Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)


## <a name="manually-in-azure-portal"></a>在 Azure 門戶中手動

您也可以自行建立虛擬機器，而不使用範本。 首先，請完成必要條件，然後再建立可用性群組。 請參閱下列主題： 

- [設定 Azure 虛擬機器上 SQL Server Always On 可用性群組的必要條件](virtual-machines-windows-portal-sql-availability-group-prereq.md)

- [建立 Always On 可用性群組以改善可用性和災害復原](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## <a name="next-steps"></a>後續步驟

[在不同區域的 Azure 虛擬機器上配置 SQL 伺服器始終處於可用性組](virtual-machines-windows-portal-sql-availability-group-dr.md)
