---
title: 包含檔案
description: 包含檔案
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 11/05/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 57b0bb9ab8ceb34021a38db0d0abf2c9c919e808
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2020
ms.locfileid: "94356637"
---
### <a name="can-i-still-deploy-multiple-bastion-hosts-across-peered-virtual-networks"></a>我是否仍然可以跨對等互連虛擬網路部署多個防禦主機？

是。 根據預設，使用者會看到部署在 VM 所在的相同虛擬網路中的堡壘主機。 不過，在 [連線 **]** 功能表中，使用者可以看到跨對等互連網路偵測到多個防禦主機。 他們可以選取想要用來連線至虛擬網路中所部署 VM 的防禦主機。

### <a name="if-my-peered-vnets-are-deployed-in-different-subscriptions-will-connectivity-via-bastion-work"></a>如果我的對等互連 Vnet 部署在不同的訂用帳戶中，是否可以透過防禦工作進行連線？

是的，透過防禦的連線會繼續在單一租使用者的不同訂用帳戶中對等互連 Vnet。 不支援跨兩個不同租使用者的訂用帳戶。 若要在 [ **連接]** 下拉式功能表中看到 [防禦]，使用者必須選取他們在訂用帳戶 **> 全域訂** 用帳戶中具有存取權的許可權。

:::image type="content" source="./media/bastion-faq-peering-include/global-subscriptions.png" alt-text="全域訂閱篩選" lightbox="./media/bastion-faq-peering-include/global-subscriptions.png":::

### <a name="i-have-access-to-the-peered-vnet-but-i-cant-see-the-vm-deployed-there"></a>我可以存取對等互連 VNet，但看不到部署的 VM。

請確定使用者具有 VM 和對等互連 VNet 的 **讀取** 許可權。 此外，請檢查 IAM 底下的使用者具有下列資源的 **讀取** 許可權：

* 虛擬機器上的讀取者角色。
* 虛擬機器的私人 IP 位址與 NIC 上的讀取者角色。
* Azure Bastion 資源上的讀者角色。
* 如果沒有對等互連的虛擬網路) ，就不需要虛擬 (網路的讀取者角色。

|權限|說明|權限類型|
|---|---| ---|
|Microsoft.Network/bastionHosts/read |取得堡壘主機|動作|
|Microsoft.Network/virtualNetworks/BastionHosts/action |取得虛擬網路中的堡壘主機參考。|動作|
|Microsoft. Network/virtualNetworks/bastionHosts/default/action|取得虛擬網路中的堡壘主機參考。|動作|
|Microsoft.Network/networkInterfaces/read|取得網路介面定義。|動作|
|Microsoft.Network/networkInterfaces/ipconfigurations/read|取得網路介面 IP 設定定義。|動作|
|Microsoft.Network/virtualNetworks/read|取得虛擬網路定義|動作|
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read|取得虛擬網路子網路中所有虛擬機器的參考|動作|
|Microsoft.Network/virtualNetworks/virtualMachines/read|取得虛擬網路中所有虛擬機器的參考|動作|