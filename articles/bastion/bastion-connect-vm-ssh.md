---
title: 使用 Azure 防禦連接到 Linux VM |Microsoft Docs
description: 在本文中，您將瞭解如何使用 Azure 防禦來連線到 Linux 虛擬機器。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 7fe1c2f74ca2a7b0fa4aefad934c45edd6f85a73
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990436"
---
# <a name="connect-using-ssh-to-a-linux-virtual-machine-using-azure-bastion"></a>使用 Azure 防禦將 SSH 連線至 Linux 虛擬機器

本文說明如何在 Azure 虛擬網路中，安全且順暢地透過 SSH 連線到您的 Linux Vm。 您可以直接從 Azure 入口網站連線到 VM。 使用 Azure Bastion 時，VM 不需要用戶端、代理程式或其他軟體。 如需 Azure 防禦的詳細資訊，請參閱[總覽](bastion-overview.md)。

您可以使用 Azure 防禦來連線到使用 SSH 的 Linux 虛擬機器。 您可以使用使用者名稱/密碼和 SSH 金鑰進行驗證。 您可以使用下列其中一種方式，透過 SSH 金鑰連接到您的 VM：

* 您手動輸入的私用金鑰
* 包含私密金鑰資訊的檔案

SSH 私密金鑰的格式必須以 `"-----BEGIN RSA PRIVATE KEY-----"` 開頭，並以 `"-----END RSA PRIVATE KEY-----"`結尾。

## <a name="before-you-begin"></a>開始之前

請確定您已為 VM 所在的虛擬網路設定 Azure 防禦主機。 如需詳細資訊，請參閱[建立 Azure 防禦主機](bastion-create-host-portal.md)。 在虛擬網路中布建並部署防禦服務之後，您就可以使用它來連線到此虛擬網路中的任何 VM。 

當您使用防禦來連線時，它會假設您使用 RDP 連線至 Windows VM，而 SSH 則是連線到您的 Linux Vm。 如需連線到 Windows VM 的詳細資訊，請參閱連線[至 vm-Windows](bastion-connect-vm-rdp.md)。

### <a name="required-roles"></a>必要角色

若要建立連線，必須具備下列角色：

* 虛擬機器上的讀取者角色
* 虛擬機器的私人 IP 位址與 NIC 上的讀取者角色
* Azure Bastion 資源上的讀取者角色

### <a name="ports"></a>連接埠

若要透過 SSH 連接到 Linux VM，您必須在 VM 上開啟下列埠：

* 輸入埠： SSH （22）


## <a name="username"></a>連接：使用使用者名稱和密碼

1.   開啟 [Azure 入口網站](https://portal.azure.com)。 流覽至您想要連接的虛擬機器，然後按一下 [連線 **]** 。 使用 SSH 連線時，VM 應該是 Linux 虛擬機器。
1. 按一下 [連線] 之後，會出現具有三個索引標籤的側邊列– RDP、SSH 和防禦。 如果已針對虛擬網路布建防禦，預設會啟用 [防禦] 索引標籤。 如果您未布建虛擬網路的防禦，請參閱[設定](bastion-create-host-portal.md)防禦。

   ![VM 連接](./media/bastion-connect-vm-ssh/bastion.png)
1. 輸入虛擬機器的 SSH 使用者名稱和密碼。
1. 輸入金鑰後，請按一下 **[連線]** 按鈕。

## <a name="privatekey"></a>連接：手動輸入私密金鑰

1. 開啟 [Azure 入口網站](https://portal.azure.com)。 流覽至您想要連接的虛擬機器，然後按一下 [連線 **]** 。 使用 SSH 連線時，VM 應該是 Linux 虛擬機器。
1. 按一下 [連線] 之後，會出現具有三個索引標籤的側邊列– RDP、SSH 和防禦。 如果已針對虛擬網路布建防禦，預設會啟用 [防禦] 索引標籤。 如果您未布建虛擬網路的防禦，請參閱[設定](bastion-create-host-portal.md)防禦。

   ![VM 連接](./media/bastion-connect-vm-ssh/bastion.png)
1. 輸入使用者名稱，然後選取 [ **SSH 私密金鑰**]。
1. 在 [文字區域**SSH 私密金鑰**] 中輸入您的私密金鑰（或直接將其貼上）。
1. 輸入金鑰後，請按一下 **[連線]** 按鈕。

## <a name="ssh"></a>連接：使用私密金鑰檔案

1. 開啟 [Azure 入口網站](https://portal.azure.com)。 流覽至您想要連接的虛擬機器，然後按一下 [連線 **]** 。 使用 SSH 連線時，VM 應該是 Linux 虛擬機器。

   ![VM 連接](./media/bastion-connect-vm-ssh/connect.png)
1. 按一下 [連線] 之後，會出現具有三個索引標籤的側邊列– RDP、SSH 和防禦。 如果已針對虛擬網路布建防禦，預設會啟用 [防禦] 索引標籤。 如果您未布建虛擬網路的防禦，請參閱[設定](bastion-create-host-portal.md)防禦。

   ![VM 連接](./media/bastion-connect-vm-ssh/bastion.png)
1. 輸入使用者名稱，然後**從本機**檔案選取 [SSH 私密金鑰]。
1. 按一下 [**流覽]** 按鈕（本機檔案中的資料夾圖示）。
1. 流覽檔案，然後按一下 [**開啟**]。
1. 按一下 **[** 連線] 以連線至 VM。 一旦您按一下 [連線]，此虛擬機器的 SSH 就會直接在 Azure 入口網站中開啟。 此連線透過 HTML5 使用防禦服務上的埠443，而不是虛擬機器的私人 IP。

## <a name="next-steps"></a>後續步驟

閱讀防禦[常見問題](bastion-faq.md)
