---
title: 使用 Azure 防禦連接到 Windows VM
description: 在本文中，瞭解如何使用 Azure 防禦連接到執行 Windows 的 Azure 虛擬機器。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 10/21/2020
ms.author: cherylmc
ms.openlocfilehash: 708bd1f61da2f3973333f8e68cabdceee0717bee
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521524"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>使用 Azure 防禦連接到 Windows 虛擬機器

您可以使用 Azure 防禦，在 Azure 入口網站中直接透過 SSL 安全地連接到您的虛擬機器。 當您使用 Azure 防禦時，您的 Vm 不需要用戶端、代理程式或其他軟體。 本文說明如何連接到您的 Windows Vm。 如需連線至 Linux VM 的詳細資訊，請參閱 [連接到 LINUX vm](bastion-connect-vm-ssh.md)。

Azure 防禦可為布建所在的虛擬網路中的所有 Vm 提供安全的連線能力。 使用 Azure Bastion 來保護您的虛擬機器免於向外公開 RDP/SSH 連接埠，同時提供使用 RDP/SSH 的安全存取。 如需詳細資訊，請參閱 [什麼是 Azure 防禦？](bastion-overview.md)。

## <a name="prerequisites"></a>必要條件

開始之前，請確認您符合下列準則：

* 已安裝防禦主機的 VNet。

   請確定您已為 VM 所在的虛擬網路設定 Azure 堡壘主機。 在您的虛擬網路中布建並部署防禦服務之後，您就可以使用它來連線到虛擬網路中的任何 VM。 若要設定 Azure 堡壘主機，請參閱 [建立防禦主機](tutorial-create-host-portal.md#createhost)。
* 虛擬網路中的 Windows 虛擬機器。
* 下列是必要角色：
  * 虛擬機器上的讀取者角色。
  * 虛擬機器的私人 IP 位址與 NIC 上的讀取者角色。
  * Azure Bastion 資源上的讀者角色。
* 連接埠：若要連線至 Windows VM，您必須在 Windows VM 上開啟下列連接埠：
  * 輸入連接埠：RDP (3389)

## <a name="connect"></a><a name="rdp"></a>連線

[!INCLUDE [Connect to a Windows VM](../../includes/bastion-vm-rdp.md)]
 
## <a name="next-steps"></a>後續步驟

閱讀 [Bastion 常見問題集](bastion-faq.md)。