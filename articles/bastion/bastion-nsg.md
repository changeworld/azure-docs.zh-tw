---
title: 使用 Azure 防禦中的 Vm 和 Nsg
description: 您可以使用網路安全性群組搭配 Azure 防禦。 深入瞭解此設定所需的子網。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 11/12/2020
ms.author: cherylmc
ms.openlocfilehash: 822a90aa2bd3a19cf3459924e6b577cc416aa3a5
ms.sourcegitcommit: 9706bee6962f673f14c2dc9366fde59012549649
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2020
ms.locfileid: "94616819"
---
# <a name="working-with-nsg-access-and-azure-bastion"></a>使用 NSG 存取和 Azure 防禦

使用「Azure 防禦」時，您可以使用 (Nsg) 的網路安全性群組。 如需詳細資訊，請參閱 [安全性群組](../virtual-network/network-security-groups-overview.md)。

:::image type="content" source="./media/bastion-nsg/figure-1.png" alt-text="NSG":::

在此圖表中：

* 堡壘主機會部署至虛擬網路。
* 使用者使用任何 HTML5 瀏覽器連線到 Azure 入口網站。
* 使用者流覽至 Azure 虛擬機器以進行 RDP/SSH。
* 連線整合-在瀏覽器中按一下單鍵 RDP/SSH 會話
* Azure VM 上不需要公用 IP。

## <a name="network-security-groups"></a><a name="nsg"></a>網路安全性群組

本節說明使用者與 Azure 防禦之間的網路流量，以及如何將虛擬網路中的 Vm 設為目標：

### <a name="azurebastionsubnet"></a><a name="apply"></a>AzureBastionSubnet

Azure 防禦專門部署至 * **AzureBastionSubnet** _。

_ 輸入 **流量：**

   * 輸入 **來自公用網際網路的流量：** Azure 防禦將會建立一個公用 IP，其需要在公用 IP 上啟用埠443來輸入流量。 不需要在 AzureBastionSubnet 上開啟埠3389/22。
   * **從 Azure 防禦控制平面輸入流量：** 針對控制平面連線能力，請從 **GatewayManager** 服務標記啟用埠443輸入。 這可讓控制平面（也就是閘道管理員）能夠與 Azure 防禦對話。
   * **Azure Load Balancer 的輸入流量：** 針對健康情況探查，請從 **AzureLoadBalancer** 服務標記啟用埠443輸入。 這可讓 Azure Load Balancer 偵測連線能力 


   :::image type="content" source="./media/bastion-nsg/inbound.png" alt-text="螢幕擷取畫面顯示 Azure 防禦連接的輸入安全性規則。":::

* **輸出流量：**

   * 流向 **目標 vm 的輸出流量：** Azure 防禦會透過私人 IP 達到目標 Vm。 Nsg 需要允許埠3389和22的其他目標 VM 子網的輸出流量。
   * **Azure 中其他公用端點的輸出流量：** Azure 防禦必須能夠連線到 Azure (中的各種公用端點，例如，用來儲存診斷記錄和計量記錄) 。 基於這個理由，Azure 防禦需要輸出至443至 **AzureCloud** 服務標記。


   :::image type="content" source="./media/bastion-nsg/outbound.png" alt-text="螢幕擷取畫面顯示 Azure 防禦連線的輸出安全性規則。":::

### <a name="target-vm-subnet"></a>目標 VM 子網
這是包含您想要 RDP/SSH 連線之目標虛擬機器的子網。

   * **從 Azure 防禦輸入流量：** Azure 防禦會透過私人 IP 到達目標 VM。 RDP/SSH 埠分別 (埠 3389/22) 必須在私人 IP 的目標 VM 端上開啟。 最佳做法是，您可以在此規則中新增 Azure 防禦子網 IP 位址範圍，以只允許防禦在目標 VM 子網中的目標 Vm 上開啟這些埠。


## <a name="next-steps"></a>後續步驟

如需 Azure 防禦的詳細資訊，請參閱 [常見問題](bastion-faq.md)。
