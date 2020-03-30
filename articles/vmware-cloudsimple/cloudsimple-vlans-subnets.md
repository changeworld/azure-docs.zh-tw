---
title: Azure VMware 解決方案中的 VLAN 和子網（按雲簡單）
description: 瞭解雲簡單私有雲中的 VLAN 和子網
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2451fbb69636624db354006df2a7925ef9e75459
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024886"
---
# <a name="vlans-and-subnets-overview"></a>VLAN 和子網概述

CloudSimple 提供部署雲簡單服務每個區域的網路。  網路是單個 TCP 第 3 層位址空間，預設情況下啟用路由。  在此區域中創建的所有私有雲和子網都可以相互通信，而無需任何其他配置。  您可以使用 VLan 在 vCenter 上創建分散式埠組。

![雲簡單網路拓撲](media/cloudsimple-network-topology.png)

## <a name="vlans"></a>VLAN

為每個私有雲創建 VLAN（第 2 層網路）。  第 2 層流量保持在私有雲的邊界內，允許您隔離私有雲中的本地流量。  在私有雲上創建的 VLAN 只能用於在專用雲中創建分散式埠組。  在私有雲上創建的 VLAN 會自動設定連接到私有雲主機的所有交換器。

## <a name="subnets"></a>子網路

通過定義子網的位址空間，可以在創建 VLAN 時創建子網。 位址空間中的 IP 位址被指定為子網閘道。 每個客戶和地區分配單個專用第 3 層位址空間。 您可以在網路區域中配置任何 RFC 1918 非重疊位址空間，以及本地網路或 Azure 虛擬網路。

預設情況下，所有子網可以相互通信，從而減少私有雲之間路由的配置開銷。 同一區域中的 PC 上的東西方資料將位於同一第 3 層網路中，並通過區域內的本地網路基礎結構進行傳輸。 區域中的私有雲之間的通信不需要出口。 此方法消除了在不同私有雲中部署不同工作負載時的任何 WAN/出口性能損失。

## <a name="vspherevsan-subnets-cidr-range"></a>vSphere/vSAN 子網 CIDR 範圍

私有雲創建為由 vCenter 伺服器管理的隔離 VMware 堆疊（ESXi 主機、vCenter、vSAN 和 NSX） 環境。  管理元件部署在為 vSphere/vSAN 子網 CIDR 選擇的網路中。  網路 CIDR 範圍在部署期間分為不同的子網。

* 最小 vSphere/vSAN 子網 CIDR 範圍首碼： **/24**
* 最大 vSphere/vSAN 子網 CIDR 範圍首碼： **/21**

> [!CAUTION]
> vSphere/vSAN CIDR 範圍內的 IP 位址保留供私有雲基礎設施使用。  不要在任何虛擬機器上使用此範圍內的 IP 位址。

### <a name="vspherevsan-subnets-cidr-range-limits"></a>vSphere/vSAN 子網 CIDR 範圍限制

選擇 vSphere/vSAN 子網 CIDR 範圍大小會影響私有雲的大小。  下表顯示了基於 vSphere/vSAN 子網 CIDR 的大小可以擁有的最大節點數。

| 指定的 vSphere/vSAN 子網 CIDR 前置長度 | 最大節點數 |
|---------------------------------------------------|-------------------------|
| /24 | 26 |
| /23 | 58 |
| /22 | 118 |
| /21 | 220 |

### <a name="management-subnets-created-on-a-private-cloud"></a>在私有雲上創建的管理子網

創建私有雲時將創建以下管理子網。

* **系統管理**。 用於 ESXi 主機管理網路、DNS 伺服器、vCenter 伺服器的 VLAN 和子網。
* **VMotion**. 用於 ESXi 主機 vMotion 網路的 VLAN 和子網。
* **VSAN**. 用於 ESXi 主機 vSAN 網路的 VLAN 和子網。
* **NsxtEdgeUplink1**. VLAN 和子網，用於 VLAN 上行鏈路到外部網路。
* **NsxtEdgeUplink2**. VLAN 和子網，用於 VLAN 上行鏈路到外部網路。
* **NsxtEdge 傳輸**。 傳輸區域的 VLAN 和子網控制 NSX-T 中第 2 層網路的覆蓋範圍。
* **NsxtHost 傳輸**。 用於主機傳輸區域的 VLAN 和子網。

### <a name="management-network-cidr-range-breakdown"></a>管理網路 CIDR 範圍細分

vSphere/vSAN 子網 CIDR 範圍被劃分為多個子網。  下表顯示了允許首碼的細分示例。  該示例使用 192.168.0.0 作為 CIDR 範圍。

範例：

| 指定的 vSphere/vSAN 子網 CIDR/首碼 | 192.168.0.0/21 | 192.168.0.0/22 | 192.168.0.0/23 | 192.168.0.0/24 |
|---------------------------------|----------------|----------------|----------------|----------------|
| 系統管理 | 192.168.0.0/24 | 192.168.0.0/24 | 192.168.0.0/25 | 192.168.0.0/26 |
| vMotion | 192.168.1.0/24 | 192.168.1.0/25 | 192.168.0.128/26 | 192.168.0.64/27 |
| vSAN | 192.168.2.0/24 | 192.168.1.128/25 | 192.168.0.192/26 | 192.168.0.96/27 |
| NSX-T 主機傳輸 | 192.168.4.0/23 | 192.168.2.0/24 | 192.168.1.0/25 | 192.168.0.128/26 |
| NSX-T 邊緣傳輸 | 192.168.7.208/28 | 192.168.3.208/28 | 192.168.1.208/28 | 192.168.0.208/28 |
| NSX-T 邊緣上行鏈路1 | 192.168.7.224/28 | 192.168.3.224/28 | 192.168.1.224/28 | 192.168.0.224/28 |
| NSX-T 邊緣上行鏈路2 | 192.168.7.240/28 | 192.168.3.240/28 | 192.168.1.240/28 | 192.168.0.240/28 |

## <a name="next-steps"></a>後續步驟

* [建立及管理 VLAN 和子網路](create-vlan-subnet.md)
