---
title: Azure 中的私人 IP 位址
titlesuffix: Azure Virtual Network
description: 瞭解 Azure 中的私人 IP 位址。
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/28/2020
ms.author: allensu
ms.openlocfilehash: 36db885cab734c037b0032c714de28b905595ef0
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98223256"
---
# <a name="private-ip-addresses"></a>私人 IP 位址
私人 Ip 允許在 Azure 中的資源之間進行通訊。 

資源可以是：
* Azure 服務，例如：
    * 虛擬機器網路介面
    * 內部負載平衡器 (ILB)
    * 應用程式閘道
* 在 [虛擬網路](virtual-networks-overview.md)中。
* 透過 VPN 閘道或 ExpressRoute 線路的內部部署網路。

私人 ip 允許通訊這些資源，而不需使用公用 IP 位址。

## <a name="allocation-method"></a>配置方法

Azure 會從資源所在之虛擬網路子網的位址範圍，將私人 IP 位址指派給資源。

Azure 會保留每個子網位址範圍內的前四個位址。 無法將位址指派給資源。 例如，如果子網的位址範圍是 10.0.0.0/16，則位址 10.0.0.0-10.0.0.3 和10.0.255.255 無法使用。 子網路位址範圍內的 IP 位址一次只能指派一個資源。 

有兩種方法可提供私人 IP 位址：

- **動態**：Azure 會在子網路的位址範圍中，指派下一個可用的取消指派或取消保留的 IP 位址。 例如，如果位址 10.0.0.4-10.0.0.9 已指派給其他資源，Azure 會將 10.0.0.10 指派給新的資源。 

    動態是預設配置方法。 一旦指派之後，如果網路介面為，就會釋放動態 IP 位址：
    
    * 已刪除
    * 重新指派給相同虛擬網路內的不同子網。
    * 配置方法會變更為靜態，並指定不同的 IP 位址。 
    
    根據預設，當您的配置方法從動態變更為靜態時，Azure 會將先前動態指派的位址指派為靜態位址。

- **靜態**：您在子網路的位址範圍中選取並指派任何取消指派或取消保留的 IP 位址。 

    例如，子網的位址範圍是 10.0.0.0/16，且位址 10.0.0.4-10.0.0.9 會指派給其他資源。 您可以在 10.0.0.10-10.0.255.254 之間指派任何位址。 只有在刪除網路介面後，才會釋出靜態位址。 
    
    當配置方法變更時，Azure 會將靜態 IP 指派為動態 IP。 即使位址不是子網中下一個可用的位址，也會發生重新指派。 當網路介面指派給不同的子網時，位址就會變更。
    
    若要將網路介面指派給不同的子網，請將配置方法從靜態變更為動態。 將網路介面指派給不同的子網，然後將配置方法變更回靜態。 從新的子網位址範圍中指派 IP 位址。
    
## <a name="virtual-machines"></a>虛擬機器

一或多個私人 IP 位址會指派給一或多個 **網路介面**。 系統會將網路介面指派給 [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虛擬機器。 您可以將每個私人 IP 位址的配置方法指定為動態或靜態。

### <a name="internal-dns-hostname-resolution-for-virtual-machines"></a>內部 DNS 主機名稱解析 (適用於虛擬機器)

根據預設，azure 虛擬機器會使用 [azure 受控 DNS 伺服器](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) 來設定。 您可以明確設定自訂 DNS 伺服器。 這些 DNS 伺服器會針對位於相同虛擬網路內的虛擬機器，提供內部名稱解析。

將主機名稱對應至虛擬機器的私人 IP 位址，並新增至 Azure 管理的 DNS 伺服器。 

當 VM 具有下列各項時，主機名稱會對應至主要網路介面的主要 IP：

* 多個網路介面
* 多個 IP 位址
* 兩者

使用 Azure 受控 DNS 設定的 Vm 會解析相同虛擬網路內的主機名稱。 使用自訂 DNS 伺服器來解析已連線之虛擬網路中 Vm 的主機名稱。

## <a name="internal-load-balancers-ilb--application-gateways"></a>內部負載平衡器 (ILB) 與應用程式閘道

您可以將私人 IP 位址指派給的 **前端** 設定：

* [Azure 內部負載平衡器](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (ILB) 
* [Azure 應用程式閘道](../application-gateway/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 

此私人 IP 位址會作為內部端點。 內部端點只能存取其虛擬網路內的資源，以及連接到它的遠端網路。 您可以指派動態或靜態 IP。

## <a name="at-a-glance"></a>快速總覽
下表顯示可讓私人 IP 與資源相關聯的屬性。 

也會顯示可以使用的可能配置方法：

* 動態
* 靜態

| 最上層資源 | IP 位址關聯 | 動態 | 靜態 |
| --- | --- | --- | --- |
| 虛擬機器 |Linux |是 |是 |
| 負載平衡器 |前端組態 |是 |是 |
| 應用程式閘道 |前端組態 |是 |是 |

## <a name="limits"></a>限制
IP 位址限制可在 Azure 中的一組完整的 [網路限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) 中找到。 這些限制是針對每一區域和每一訂用帳戶。 [請聯絡支援](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 人員，以根據您的業務需求增加最大限制的預設限制。

## <a name="next-steps"></a>後續步驟
瞭解 [Azure 中的公用 IP 位址](public-ip-addresses.md)
* [部署使用靜態私人 IP 位址的 VM](virtual-networks-static-private-ip-arm-pportal.md)