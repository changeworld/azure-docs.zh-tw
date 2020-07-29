---
title: SQL Server Always On 可用性群組總覽
description: 本文介紹 Azure 虛擬機器上的 SQL Server Always On 可用性群組。
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/13/2017
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: 0e676e1c97c0039490137aa12b06fc88e457646f
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87286821"
---
# <a name="introducing-sql-server-always-on-availability-groups-on-azure-virtual-machines"></a>Azure 虛擬機器上的 SQL Server Always On 可用性群組簡介

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文介紹 Azure 虛擬機器上的 SQL Server 可用性群組。 

「Azure 虛擬機器」上的 Always On 可用性群組與內部部署的 Always On 可用性群組類似。 如需詳細資訊，請參閱 [Always On 可用性群組 (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx)。 

下圖說明 Azure 虛擬機器中完整 SQL Server 可用性群組的各個部分。

![可用性群組](./media/availability-group-overview/00-EndstateSampleNoELB.png)

Azure 虛擬機器上可用性群組的主要差異在於，這些虛擬機器（Vm）需要[負載平衡器](../../../load-balancer/load-balancer-overview.md)。 負載平衡器會保有可用性群組接聽程式的 IP 位址。 如果您有一個以上的可用性群組，每個群組都需要接聽程式。 一個負載平衡器可以支援多個接聽程式。

此外，在 Azure IaaS VM 客體容錯移轉叢集上，我們建議使用每部伺服器 (叢集節點) 的單一 NIC 和單一子網路。 Azure 網路有實體備援，因此 Azure IaaS VM 客體叢集上不需要額外的 NIC 和子網路。 雖然叢集驗證報告會發出節點只能在單一網路上連線的警告，但您可以放心地在 Azure IaaS VM 客體容錯移轉叢集上忽略此警告。 

若要提高備援能力與高可用性，SQL Server VM 應位於相同的[可用性設定組](availability-group-manually-configure-prerequisites-tutorial.md#create-availability-sets)，或不同的[可用性區域](/azure/availability-zones/az-overview)。 

|  | Windows Server 版本 | SQL Server 版本 | SQL Server 版本 | WSFC 仲裁設定 | 多重地區災害復原 | 多重子網路支援 | 對現有 AD 的支援 | 相同地區多重區域災害復原 | 不包含 AD 網域的 Dist-AG 支援 | 不包含叢集的 Dist-AG 支援 |  
| :------ | :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----|
| **[SQL VM CLI](availability-group-az-cli-configure.md)** | 2016 | 2017 </br>2016   | Ent | 雲端見證 | 否 | 是 | 是 | 是 | 否 | 否 |
| **[快速入門範本](availability-group-quickstart-template-configure.md)** | 2016 | 2017</br>2016  | Ent | 雲端見證 | 否 | 是 | 是 | 是 | 否 | 否 |
| **[手動](availability-group-manually-configure-prerequisites-tutorial.md)** | 全部 | 全部 | 全部 | 全部 | 是 | 是 | 是 | 是 | 是 | 是 |

已從 Azure Marketplace 移除**SQL Server AlwaysOn 叢集（預覽）** 範本，因此無法再使用。 

當您已準備好在「Azure 虛擬機器」上建置 SQL Server 可用性群組時，請參考這些教學課程。

## <a name="manually-with-azure-cli"></a>透過 Azure CLI 手動進行

建議使用 Azure CLI 來設定和部署可用性群組，因為它是最簡單且最快速的部署。 使用 Azure CLI 時，建立 Windows 容錯移轉叢集、將 SQL Server VM 加入至叢集，以及建立接聽程式與內部負載平衡器等作業，都可以在 30 分鐘內完成。 此選項仍然需要手動建立可用性群組，但它會將所有其他必要的設定步驟自動化。 

如需詳細資訊，請參閱[使用 Azure SQL VM CLI 為 Azure VM 上的 SQL Server 設定 Always On 可用性群組](availability-group-az-cli-configure.md)。 

## <a name="automatically-with-azure-quickstart-templates"></a>使用 Azure 快速入門範本自動進行

Azure 快速入門範本會利用 SQL VM 資源提供者來部署 Windows 容錯移轉叢集、將 SQL Server VM 加入到叢集、建立接聽程式，以及設定內部負載平衡器。 此選項仍然需要手動建立可用性群組和內部 Load Balancer （ILB）。 不過，它會自動化並簡化所有其他必要的設定步驟，包括 ILB 的設定。 

如需詳細資訊，請參閱[使用 Azure 快速入門範本為 Azure VM 上的 SQL Server 設定 Always On 可用性群組](availability-group-quickstart-template-configure.md)。


## <a name="automatically-with-an-azure-portal-template"></a>使用 Azure 入口網站範本自動進行

[在 Azure VM 中自動設定 Always On 可用性群組 - Resource Manager](availability-group-azure-marketplace-template-configure.md)


## <a name="manually-in-the-azure-portal"></a>在 Azure 入口網站中手動

您也可以自行建立虛擬機器，而不使用範本。 首先，請完成必要條件，然後再建立可用性群組。 請參閱下列主題： 

- [設定 Azure 虛擬機器上 SQL Server Always On 可用性群組的必要條件](availability-group-manually-configure-prerequisites-tutorial.md)

- [建立 Always On 可用性群組以改善可用性和災害復原](availability-group-manually-configure-tutorial.md)

## <a name="next-steps"></a>後續步驟

[在不同區域中的 Azure 虛擬機器上設定 SQL Server Always On 可用性群組](availability-group-manually-configure-multiple-regions.md)
