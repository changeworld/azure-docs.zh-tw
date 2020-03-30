---
title: 使用 Azure 堡壘連接到 Windows VM
description: 在本文中，瞭解如何使用 Azure 堡壘連接到運行 Windows 的 Azure 虛擬機器。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: cherylmc
ms.openlocfilehash: 6ec60dc313c8a4374637adf38ea0e5a7d4ed964b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597334"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>使用 Azure 堡壘連接到 Windows 虛擬機器

使用 Azure 堡壘，可以直接在 Azure 門戶中通過 SSL 安全無縫地連接到虛擬機器。 使用 Azure 堡壘時，VM 不需要用戶端、代理或其他軟體。 本文介紹如何連接到 Windows VM。 有關連接到 Linux VM 的資訊，請參閱[使用 Azure 堡壘 - Linux 連接到 VM。](bastion-connect-vm-ssh.md)

Azure Bastion 向預配它的虛擬網路中的所有 VM 提供安全連線。 使用 Azure Bastion 來保護您的虛擬機器免於向外公開 RDP/SSH 連接埠，同時提供使用 RDP/SSH 的安全存取。 有關詳細資訊，請參閱[概述](bastion-overview.md)。

## <a name="before-you-begin"></a>開始之前

請確保為 VM 所在的虛擬網路設置了 Azure 堡壘主機。 在虛擬網路中預配和部署 Bastion 服務後，可以使用它連接到虛擬網路中的任何 VM。 要設置 Azure 堡壘主機，請參閱[創建 Azure 堡壘主機](bastion-create-host-portal.md)。

### <a name="required-roles"></a>所需角色

要建立連接，需要以下角色：

* 虛擬機器上的讀取者角色
* 虛擬機器的私人 IP 位址與 NIC 上的讀取者角色
* Azure Bastion 資源上的讀取者角色

### <a name="ports"></a>連接埠

要連接到 Windows VM，必須在 Windows VM 上打開以下埠：

* 入站埠： RDP （3389）

## <a name="connect"></a><a name="rdp"></a>連接

1. 打開[Azure 門戶](https://portal.azure.com)。 導航到要連接到的虛擬機器，然後按一下 **"連接**"並從下拉清單中選擇 **"堡壘**"。

   ![VM 連接](./media/bastion-connect-vm-rdp/connect.png)
1. 按一下"堡壘"後，將顯示一個側欄，該邊欄具有三個選項卡 - RDP、SSH 和堡壘。 如果為虛擬網路預配了堡壘，則預設情況下堡壘選項卡處於活動狀態。 如果未為虛擬網路預配堡壘，則可以按一下該連結來配置堡壘。 有關配置說明，請參閱[配置堡壘](bastion-create-host-portal.md)。

   ![堡壘選項卡](./media/bastion-connect-vm-rdp/bastion.png)
1. 在"堡壘"選項卡上，輸入虛擬機器的使用者名和密碼，然後按一下"**連接**"。 通過 Bastion 連接到此虛擬機器的 RDP 連接將直接在 Azure 門戶（通過 HTML5）中使用埠 443 和堡壘服務打開。

   ![RDP 連接](./media/bastion-connect-vm-rdp/443rdp.png)
 
## <a name="next-steps"></a>後續步驟

閱讀[堡壘常見問題解答](bastion-faq.md)
