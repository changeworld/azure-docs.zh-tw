---
title: 在 Azure 上的 VMware 中運行時，確保應用程式高可用性
description: 描述雲簡單高可用性功能，以解決在雲簡單私有雲中運行的應用程式的常見應用程式失敗方案
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a3eed033ba6a1a6f9237116a53ec7751ae906fe4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025345"
---
# <a name="ensure-application-high-availability-when-running-in-vmware-on-azure"></a>在 Azure 上的 VMware 中運行時，確保應用程式高可用性

CloudSimple 解決方案為在 Azure 環境中在 VMware 上運行的應用程式提供高可用性。 下表列出了故障方案和關聯的高可用性功能。

| 失敗方案 | 應用程式受到保護？ | 平臺 HA 功能 | VMware HA 功能 | Azure HA 功能 |
------------ | ------------- | ------------ | ------------ | ------------- |
| 磁碟失敗 | YES | 快速更換故障節點 | [關於 vSAN 預設存儲策略](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-C228168F-6807-4C2A-9D74-E584CAF49A2A.html) |
| 風扇故障 | YES | 冗餘風扇，快速更換故障節點 |  |  |
| NIC 故障 | YES | 冗余 NIC，快速更換故障節點
| 主機電源故障 | YES | 冗余電源 |  |  |
| ESXi 主機故障 | YES | 快速更換故障節點 | [VMware vSphere 高可用性](https://www.vmware.com/products/vsphere/high-availability.html) |  |  |
| VM 失敗 | YES | [負載平衡器](load-balancers.md)  | [VMware vSphere 高可用性](https://www.vmware.com/products/vsphere/high-availability.html) | 用於無狀態 VM 的 Azure 負載等化器 |
| 葉開關埠故障 | YES | 冗余 NIC |  |  |
| 葉開關故障 | YES | 冗余葉開關 |  |  |
| 機架故障 | YES | 放置群組 |  |  |
| 連接到本地 DC 的網路連接 | YES  | 冗余網路服務 |  | 冗余 ER 電路 |
| 與 Azure 的網路連接 | YES | |  | 冗余 ER 電路 |
| 資料中心故障 | YES |  |  | 可用性區域 |
| 區域故障 | YES  |  |  | Azure 區域 |

Azure VMware 雲簡單解決方案提供了以下高可用性功能。

## <a name="fast-replacement-of-failed-node"></a>快速更換故障節點

CloudSimple 控制平面軟體持續監控 VMware 群集的運行狀況，並檢測 ESXi 節點何時發生故障。 然後，它會自動從其現成的節點池中向受影響的 VMware 群集添加新的 ESXi 主機，並將故障節點從群集中拉出。 此功能可確保快速恢復 VMware 群集中的備用容量，以便恢復 vSAN 和 VMware HA 提供的群集的恢復能力。

## <a name="placement-groups"></a>放置群組

創建私有雲的使用者可以選擇所選區域內的 Azure 區域和展示位置組。 放置組是分佈在多個機架上但在同一骨幹段內的一組節點。 同一放置組中的節點最多可以通過兩個額外的開關躍點到達對方。 放置組始終位於單個 Azure 可用性區域內，並跨越多個機架。 CloudSimple 控制平面基於最大努力，將私有雲的節點分佈在多個機架上。 不同的放置組中的節點保證放置在不同的機架中。

## <a name="availability-zones"></a>可用性區域

可用性區域是一種高可用性產品，可保護您的應用程式和資料免受資料中心故障的影響。 可用性區域是 Azure 區域中的特殊物理位置。 每個區域皆由一或多個配備獨立電力、冷卻系統及網路的資料中心所組成。 每個區域都有一個可用區域。 有關詳細資訊，請參閱[Azure 中有哪些可用性區域？](../availability-zones/az-overview.md)

## <a name="redundant-azure-expressroute-circuits"></a>冗余 Azure 快速路由電路

使用 ExpressRoute 與 Azure vNet 的資料中心連接具有冗余電路，可提供高可用性的網路連接鏈路。

## <a name="redundant-networking-services"></a>冗余網路服務

專用雲的所有 CloudSimple 網路服務（包括 VLAN、防火牆、公共 IP 位址、互聯網和 VPN）都旨在高度可用並支援 SLA 服務。

## <a name="azure-layer-7-load-balancer-for-stateless-vmware-vms"></a>Azure 第 7 層負載等化器，用於無狀態 VM

使用者可以將 Azure 層 7 負載等化器放在 VMware 環境中運行的無狀態 Web 層 VM 前面，以實現 Web 層的高可用性。

## <a name="azure-regions"></a>Azure 區域

Azure 區域是部署在延遲定義週邊線內並通過私人區域低延遲網路連接的一組資料中心。 有關詳細資訊，請參閱[Azure 區域](https://azure.microsoft.com/global-infrastructure/regions)。
