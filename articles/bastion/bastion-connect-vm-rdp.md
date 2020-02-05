---
title: 使用 Azure 防禦連接到 Windows VM |Microsoft Docs
description: 在本文中，您將瞭解如何使用 Azure 防禦連線至執行 Windows 的 Azure 虛擬機器。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 7dad6a517341f83f693e1e7e1f7d27e899e00f7e
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990482"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>使用 Azure 防禦連接到 Windows 虛擬機器

本文說明如何在 Azure 虛擬網路中使用 Azure 防禦，安全且順暢地透過 RDP 連線到您的 Windows Vm。 您可以直接從 Azure 入口網站連線到 VM。 使用 Azure Bastion 時，VM 不需要用戶端、代理程式或其他軟體。 如需 Azure 防禦的詳細資訊，請參閱[總覽](bastion-overview.md)。

## <a name="before-you-begin"></a>開始之前

請確定您已為 VM 所在的虛擬網路設定 Azure 防禦主機。 如需詳細資訊，請參閱[建立 Azure 防禦主機](bastion-create-host-portal.md)。 在虛擬網路中布建並部署防禦服務之後，您就可以使用它來連線到此虛擬網路中的任何 VM。

防禦會假設您使用 RDP 連線至 Windows VM，而 SSH 則是用來連線到您的 Linux Vm。 如需 Linux VM 連線的相關資訊，請參閱[連接到 VM-linux](bastion-connect-vm-ssh.md)。

### <a name="required-roles"></a>必要角色
若要建立連線，必須具備下列角色：

* 虛擬機器上的讀取者角色
* 虛擬機器的私人 IP 位址與 NIC 上的讀取者角色
* Azure Bastion 資源上的讀取者角色

### <a name="ports"></a>連接埠

若要透過 RDP 連接到 Windows VM，您必須在 Windows VM 上開啟下列埠：

* 輸入埠： RDP （3389）

## <a name="rdp"></a>使用 RDP 連接

1. 開啟 [Azure 入口網站](https://portal.azure.com)。 流覽至您想要連接的虛擬機器，然後按一下 [連線 **]** 。 使用 RDP 連線時，VM 應該是 Windows 虛擬機器。

   ![VM 連接](./media/bastion-connect-vm-rdp/connect.png)
1. 按一下 [連線] 之後，會出現具有三個索引標籤的側邊列– RDP、SSH 和防禦。 如果已針對虛擬網路布建防禦，預設會啟用 [防禦] 索引標籤。 如果您未布建虛擬網路的防禦，您可以按一下連結來設定防禦。 如需設定指示，請參閱[設定](bastion-create-host-portal.md)防禦。

   ![VM 連接](./media/bastion-connect-vm-rdp/bastion.png)
1. 在 [防禦] 索引標籤上，您虛擬機器的使用者名稱和密碼，然後按一下 **[連線]** 。 透過防禦的此虛擬機器的 RDP 連線，會使用埠443和防禦服務，直接在 Azure 入口網站（透過 HTML5）中開啟。

   ![VM 連接](./media/bastion-connect-vm-rdp/443rdp.png)
 
## <a name="next-steps"></a>後續步驟

閱讀防禦[常見問題](bastion-faq.md)
