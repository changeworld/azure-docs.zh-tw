---
title: 在 Azure 上的 VMware 中執行時，確保應用程式的高可用性
description: 描述 CloudSimple 高可用性功能，以解決在 CloudSimple 私用雲端中執行之應用程式的常見應用程式失敗案例
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8bb1f8bb2aaeab88e5a9ea19534c8983af8c1626
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97895745"
---
# <a name="ensure-application-high-availability-when-running-in-vmware-on-azure"></a>在 Azure 上的 VMware 中執行時，確保應用程式的高可用性

CloudSimple 解決方案可為在 Azure 環境中的 VMware 上執行的應用程式提供高可用性。 下表列出失敗案例和相關聯的高可用性功能。

| 失敗案例 | 應用程式受到保護嗎？ | 平臺 HA 功能 | VMware HA 功能 | Azure HA 功能 |
------------ | ------------- | ------------ | ------------ | ------------- |
| 磁碟失敗 | YES | 快速更換失敗的節點 | [關於 vSAN 預設儲存體原則](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-C228168F-6807-4C2A-9D74-E584CAF49A2A.html) |
| 風扇故障 | YES | 冗餘風扇，快速更換失敗的節點 |  |  |
| NIC 失敗 | YES | 多餘的 NIC，快速更換失敗的節點
| 主機電源故障 | YES | 冗余電源供應器 |  |  |
| ESXi 主機失敗 | YES | 快速更換失敗的節點 | [VMware vSphere 高可用性](https://www.vmware.com/products/vsphere/high-availability.html) |  |  |
| VM 失敗 | YES | [負載平衡器](load-balancers.md)  | [VMware vSphere 高可用性](https://www.vmware.com/products/vsphere/high-availability.html) | 無狀態 VMware Vm 的 Azure Load Balancer |
| 分葉交換器埠失敗 | YES | 多餘的 NIC |  |  |
| 分葉切換失敗 | YES | 多餘的分葉參數 |  |  |
| 機架故障 | YES | 放置群組 |  |  |
| 內部部署 DC 的網路連線能力 | YES  | 重複的網路服務 |  | 多餘的 ER 電路 |
| Azure 的網路連線能力 | YES | |  | 多餘的 ER 電路 |
| 資料中心失敗 | YES |  |  | 可用性區域 |
| 區域失敗 | YES  |  |  | Azure 區域 |

Azure VMware Solution by CloudSimple 提供下列高可用性功能。

## <a name="fast-replacement-of-failed-node"></a>快速更換失敗的節點

CloudSimple 控制平面軟體會持續監視 VMware 叢集的健康情況，並偵測 ESXi 節點何時失敗。 然後，它會自動將新的 ESXi 主機從現成可用節點的集區新增至受影響的 VMware 叢集，並將失敗的節點移出叢集。 這項功能可確保 VMware 叢集中的備用容量可以快速還原，以便還原 vSAN 和 VMware HA 提供的叢集復原。

## <a name="placement-groups"></a>放置群組

建立私人雲端的使用者可以選取 Azure 區域和所選區域內的放置群組。 放置群組是分散在多個機架上，但位於相同書脊網路區段內的一組節點。 相同放置群組內的節點可以彼此聯繫，最多可達兩個額外的切換躍點。 放置群組一律會在單一 Azure 可用性區域內，並跨越多個機架。 CloudSimple 控制平面會根據最多的工作，將私人雲端的節點分散到多個機架。 不同放置群組中的節點保證會放置在不同的機架中。

## <a name="availability-zones"></a>可用性區域

可用性區域是高可用性供應項目，可保護您的應用程式和資料不受資料中心故障影響。 可用性區域是 Azure 區域內的特殊實體位置。 每個區域皆由一或多個配備獨立電力、冷卻系統及網路的資料中心所組成。 每個區域都有一個可用性區域。 如需詳細資訊，請參閱 [Azure 中有哪些可用性區域？](../availability-zones/az-overview.md)。

## <a name="redundant-azure-expressroute-circuits"></a>多餘的 Azure ExpressRoute 線路

使用 ExpressRoute 連線至 Azure vNet 的資料中心有重複的線路，以提供高可用性的網路連線連結。

## <a name="redundant-networking-services"></a>重複的網路服務

私人雲端的所有 CloudSimple 網路服務 (包括 VLAN、防火牆、公用 IP 位址、網際網路和 VPN) 設計為高度可用，且能夠支援服務 SLA。

## <a name="azure-layer-7-load-balancer-for-stateless-vmware-vms"></a>適用于無狀態 VMware Vm 的 Azure 第7層 Load Balancer

使用者可以在 VMware 環境中執行的無狀態 web 層 Vm 前面放置 Azure 第7層 Load Balancer，以達到 web 層的高可用性。

## <a name="azure-regions"></a>Azure 區域

Azure 區域是在延遲定義的周邊內部署的一組資料中心，並透過專用的區域低延遲網路進行連線。 如需詳細資訊，請參閱 [Azure 區域](https://azure.microsoft.com/global-infrastructure/regions)。
