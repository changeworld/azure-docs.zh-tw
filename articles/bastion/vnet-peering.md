---
title: VNet 對等互連和 Azure 防禦架構
description: 在本文中，您將瞭解如何搭配使用 VNet 對等互連和 Azure 防禦來連線至 Vm。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 12/09/2020
ms.author: cherylmc
ms.openlocfilehash: 472261666c86b666efd09c7217d12e5a795a50d9
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2020
ms.locfileid: "97094877"
---
# <a name="vnet-peering-and-azure-bastion-preview"></a>VNet 對等互連和 Azure 防禦 (預覽) 

您可以同時使用 Azure 防禦和 VNet 對等互連。 設定 VNet 對等互連時，您不需要在每個對等互連 VNet 中部署 Azure 防禦。 這表示，如果您在一個虛擬網路中設定 Azure 堡壘主機 (VNet) ，它可以用來連線至對等互連 VNet 中部署的 Vm，而不需要部署額外的防禦主機。 如需 VNet 對等互連的詳細資訊，請參閱 [關於虛擬網路對等互連](../virtual-network/virtual-network-peering-overview.md)。

Azure 防禦適用于下列類型的對等互連：

* **虛擬網路對等互連：** 連接相同 Azure 區域內的虛擬網路。
* **全域虛擬網路對等互連：** 跨 Azure 區域連接虛擬網路。

## <a name="architecture"></a>架構

設定 VNet 對等互連時，可在中樞和輪輻或全網狀拓撲中部署 Azure 防禦。 Azure Bastion 部署依虛擬網路來進行，而非以訂用帳戶/帳戶或虛擬機器為依據。

當您在虛擬網路中布建 Azure 防禦服務之後，您可以在相同 VNet 中的所有 Vm 和對等互連 Vnet 中使用 RDP/SSH 體驗。 這表示您可以將防禦部署合併至單一 VNet，並仍能觸達部署在對等互連 VNet 中的 Vm，並將整體部署集中。

:::image type="content" source="./media/vnet-peering/design.png" alt-text="設計與架構圖表":::

下圖顯示中樞和輪輻模型中的 Azure 防禦部署架構。 在此圖表中，您可以看到下列設定：

* 堡壘主機會部署在集中式中樞虛擬網路中。
* 已部署 (NSG) 的集中式網路安全性群組。
* Azure VM 上不需要公用 IP。

**步驟：**

1. 使用任何 HTML5 瀏覽器連接到 Azure 入口網站。
1. 選取要連接的虛擬機器。
1. 跨對等互連 VNet 順暢地偵測到 Azure 防禦。
1. 只要按一下，RDP/SSH 工作階段就會在瀏覽器中開啟。 如需 RDP 和 SSH 並行會話限制，請參閱 [rdp 和 ssh 會話](bastion-faq.md#limits)。

  :::image type="content" source="../../includes/media/bastion-vm-rdp/connect-vm.png" alt-text="[連接]":::

   如需透過 Azure 防禦連接至 VM 的詳細資訊，請參閱：

   * [連接至 VM-RDP](bastion-connect-vm-rdp.md)。
   * [連接至 VM-SSH](bastion-connect-vm-ssh.md)。

## <a name="faq"></a>常見問題集

[!INCLUDE [FAQ for VNet peering](../../includes/bastion-faq-peering-include.md)]

## <a name="next-steps"></a>後續步驟

閱讀 [Bastion 常見問題集](bastion-faq.md)。