---
title: 使用 Azure 防禦連接到 Windows VM
description: 在本文中，您將瞭解如何使用 Azure 防禦連線至執行 Windows 的 Azure 虛擬機器。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 02/24/2020
ms.author: cherylmc
ms.openlocfilehash: 79eb09a005f62846fc2f7e3e7b493d5e366edabc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84744318"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>使用 Azure 防禦連接到 Windows 虛擬機器

使用 Azure 防禦，您可以在 Azure 入口網站中直接透過 SSL 安全且順暢地連線到您的虛擬機器。 當您使用 Azure 防禦時，您的 Vm 不需要用戶端、代理程式或其他軟體。 本文將說明如何連線到您的 Windows Vm。 如需連線到 Linux VM 的詳細資訊，請參閱[使用 Azure 防禦 linux 連線到 VM](bastion-connect-vm-ssh.md)。

Azure 防禦提供虛擬網路中布建它的所有 Vm 的安全連線。 使用 Azure Bastion 來保護您的虛擬機器免於向外公開 RDP/SSH 連接埠，同時提供使用 RDP/SSH 的安全存取。 如需詳細資訊，請參閱[總覽](bastion-overview.md)。

## <a name="before-you-begin"></a>開始之前

請確定您已為 VM 所在的虛擬網路設定 Azure 防禦主機。 在虛擬網路中布建並部署防禦服務之後，您就可以使用它來連線到虛擬網路中的任何 VM。 若要設定 Azure 防禦主機，請參閱[建立 azure 防禦主機](bastion-create-host-portal.md)。

### <a name="required-roles"></a>必要角色

若要建立連接，需要下列角色：

* 虛擬機器上的讀取者角色
* 虛擬機器的私人 IP 位址與 NIC 上的讀取者角色
* Azure Bastion 資源上的讀取者角色

### <a name="ports"></a>連接埠

若要連接到 Windows VM，您必須在 Windows VM 上開啟下列埠：

* 輸入埠： RDP （3389）

## <a name="connect"></a><a name="rdp"></a>連線

1. 開啟 [Azure 入口網站](https://portal.azure.com)。 流覽至您想要連線的虛擬機器，然後按一下 [連線 **]** ，並**從下拉式**清單中選取 [防禦]。

   ![VM 連接](./media/bastion-connect-vm-rdp/connect.png)
1. 按一下 [防禦] 之後，會出現具有三個索引標籤（RDP、SSH 和防禦）的側條。 如果已針對虛擬網路布建防禦，預設會啟用 [防禦] 索引標籤。 如果您未布建虛擬網路的防禦，您可以按一下連結來設定防禦。 如需設定指示，請參閱[設定](bastion-create-host-portal.md)防禦。

   ![防禦索引標籤](./media/bastion-connect-vm-rdp/bastion.png)
1. 在 [防禦] 索引標籤上，輸入虛擬機器的使用者名稱和密碼，然後按一下 **[連線]**。 透過 Bastion 的這個虛擬機器 RDP 連線，會使用連接埠 443 和 Bastion 服務直接在 Azure 入口網站中開啟 (透過 HTML5)。

   ![RDP 連線](./media/bastion-connect-vm-rdp/443rdp.png)
 
## <a name="next-steps"></a>後續步驟

閱讀防禦[常見問題](bastion-faq.md)
