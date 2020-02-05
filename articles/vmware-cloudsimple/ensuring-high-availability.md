---
title: 在 Azure 上的 VMware 中執行時，確保應用程式的高可用性
description: 說明 AVS 高可用性功能，以解決在 AVS 私人雲端中執行之應用程式的常見應用程式失敗案例
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b32f7f3f38098f935382cce46d8251340784b940
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025345"
---
# <a name="ensure-application-high-availability-when-running-in-vmware-on-azure"></a>在 Azure 上的 VMware 中執行時，確保應用程式的高可用性

AVS 解決方案會為在 Azure 環境中執行的應用程式提供高可用性。 下表列出失敗案例和相關聯的高可用性功能。

| 失敗案例 | 受保護的應用程式？ | 平臺 HA 功能 | VMware HA 功能 | Azure HA 功能 |
------------ | ------------- | ------------ | ------------ | ------------- |
| 磁片失敗 | YES | 快速取代失敗的節點 | [關於 vSAN 預設儲存體原則](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-C228168F-6807-4C2A-9D74-E584CAF49A2A.html) |
| 風扇故障 | YES | 冗餘風扇，快速取代失敗的節點 |  |  |
| NIC 失敗 | YES | 重複的 NIC，快速取代失敗的節點
| 主機電源失敗 | YES | 多餘的電源供應器 |  |  |
| ESXi 主機失敗 | YES | 快速取代失敗的節點 | [VMware vSphere 高可用性](https://www.vmware.com/products/vsphere/high-availability.html) |  |  |
| VM 失敗 | YES | [負載平衡器](load-balancers.md)  | [VMware vSphere 高可用性](https://www.vmware.com/products/vsphere/high-availability.html) | 無狀態 VMware Vm 的 Azure Load Balancer |
| 分葉切換埠失敗 | YES | 多餘的 NIC |  |  |
| 分葉切換失敗 | YES | 多餘的分葉切換 |  |  |
| 機架故障 | YES | 放置群組 |  |  |
| 內部部署 DC 的網路連線能力 | YES  | 重複的網路服務 |  | 多餘的 ER 線路 |
| Azure 的網路連線能力 | YES | |  | 多餘的 ER 線路 |
| 資料中心失敗 | YES |  |  | 可用性區域 |
| 區域失敗 | YES  |  |  | Azure 地區 |

Azure VMware Solution by AVS 提供下列高可用性功能。

## <a name="fast-replacement-of-failed-node"></a>快速取代失敗的節點

AVS 控制平面軟體會持續監視 VMware 叢集的健全狀況，並偵測 ESXi 節點何時失敗。 然後，它會自動將新的 ESXi 主機新增至受影響的 VMware 叢集，從其立即可用的節點集區，到失敗的節點移出叢集。 這項功能可確保 VMware 叢集中的備用容量會快速還原，以便還原 vSAN 和 VMware HA 所提供的叢集復原功能。

## <a name="placement-groups"></a>放置群組

建立 AVS 私人雲端的使用者可以在選取的區域中選取 Azure 區域和放置群組。 放置群組是散佈于多個機架上，但在相同側面網路區段內的一組節點。 相同放置群組內的節點可以彼此接觸，最多可有兩個額外的切換躍點。 放置群組一律會在單一 Azure 可用性區域內，並跨越多個機架。 AVS 控制平面會根據最佳做法，將 AVS 私人雲端的節點分散到多個機架上。 不同放置群組中的節點保證會放在不同的機架中。

## <a name="availability-zones"></a>可用性區域

可用性區域是高可用性供應專案，可保護您的應用程式和資料不受資料中心失敗的影響。 可用性區域是 Azure 區域內的特殊實體位置。 每個區域皆由一或多個配備獨立電力、冷卻系統及網路的資料中心所組成。 每個區域都有一個可用性區域。 如需詳細資訊，請參閱[什麼是 Azure 中的可用性區域？](../availability-zones/az-overview.md)。

## <a name="redundant-azure-expressroute-circuits"></a>多餘的 Azure ExpressRoute 線路

使用 ExpressRoute 將資料中心連線到 Azure vNet 有重複的線路，以提供高可用性的網路連線連結。

## <a name="redundant-networking-services"></a>重複的網路服務

所有適用于 AVS 私用雲端（包括 VLAN、防火牆、公用 IP 位址、網際網路和 VPN）的 AVS 網路服務，都是設計成高度可用且能夠支援服務 SLA。

## <a name="azure-layer-7-load-balancer-for-stateless-vmware-vms"></a>無狀態 VMware Vm 的 Azure 第7層 Load Balancer

使用者可以將 Azure 第7層 Load Balancer 放在 VMware 環境中執行的無狀態 web 層 Vm 前面，以達到 web 層的高可用性。

## <a name="azure-regions"></a>Azure 地區

Azure 區域是在延遲定義的周邊內部署的一組資料中心，並透過專用的區域低延遲網路連線。 如需詳細資訊，請參閱[Azure 區域](https://azure.microsoft.com/global-infrastructure/regions)。
