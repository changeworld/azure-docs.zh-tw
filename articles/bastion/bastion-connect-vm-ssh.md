---
title: 使用 Azure 防禦連接到 Linux VM
description: 在本文中，您將瞭解如何使用 Azure 防禦來連線至 Linux 虛擬機器。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 02/24/2020
ms.author: cherylmc
ms.openlocfilehash: f9bc06e58fcbd93fba0c92e91b51503239b5a951
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90980772"
---
# <a name="connect-using-ssh-to-a-linux-virtual-machine-using-azure-bastion"></a>使用 Azure 防禦將 SSH 連接到 Linux 虛擬機器

本文說明如何在 Azure 虛擬網路中安全且順暢地 SSH 連線到您的 Linux Vm。 您可以直接從 Azure 入口網站連接到 VM。 使用 Azure 防禦時，Vm 不需要用戶端、代理程式或其他軟體。 如需 Azure 防禦的詳細資訊，請參閱 [總覽](bastion-overview.md)。

您可以使用 Azure 防禦，透過 SSH 連接到 Linux 虛擬機器。 您可以同時使用使用者名稱/密碼和 SSH 金鑰進行驗證。 您可以使用下列其中一種方式，使用 SSH 金鑰連接到您的 VM：

* 您手動輸入的私密金鑰
* 包含私密金鑰資訊的檔案

SSH 私密金鑰必須採用開頭為  `"-----BEGIN RSA PRIVATE KEY-----"` 且結尾為的格式 `"-----END RSA PRIVATE KEY-----"` 。

## <a name="before-you-begin"></a>開始之前

請確定您已為 VM 所在的虛擬網路設定 Azure 堡壘主機。 如需詳細資訊，請參閱 [建立 Azure 堡壘主機](bastion-create-host-portal.md)。 在您的虛擬網路中布建並部署防禦服務之後，您就可以使用它來連線到此虛擬網路中的任何 VM。 

當您使用防禦連線時，會假設您使用 RDP 連線至 Windows VM，並使用 SSH 連線到您的 Linux Vm。 如需有關連接到 Windows VM 的詳細資訊，請參閱連線 [到 VM-windows](bastion-connect-vm-rdp.md)。

### <a name="required-roles"></a>必要的角色

若要建立連線，必須具備下列角色：

* 虛擬機器上的讀取者角色
* 虛擬機器的私人 IP 位址與 NIC 上的讀取者角色
* Azure Bastion 資源上的讀取者角色

### <a name="ports"></a>連接埠

若要透過 SSH 連接到 Linux VM，您必須在 VM 上開啟下列埠：

* 輸入埠： SSH (22) 

## <a name="connect-using-username-and-password"></a><a name="username"></a>連接：使用使用者名稱和密碼

1. 開啟 [Azure 入口網站](https://portal.azure.com)。 流覽至您要連接的虛擬機器，然後按一下 [連線] **，然後從** 下拉式清單 **中選取 [** 防禦]。

   ![螢幕擷取畫面顯示已選取 [連線] Azure 入口網站中的虛擬機器總覽。](./media/bastion-connect-vm-ssh/connect.png)
1. 按一下 [防禦] 之後，會出現一個具有三個索引標籤的側邊列– RDP、SSH 和防禦。 如果已針對虛擬網路布建防禦功能，則預設會使用 [防禦] 索引標籤。 如果您未布建虛擬網路的防禦，請參閱 [設定](bastion-create-host-portal.md)防禦。

   ![螢幕擷取畫面顯示已選取防禦的 [連線至虛擬機器] 對話方塊。](./media/bastion-connect-vm-ssh/bastion.png)
1. 輸入虛擬機器的 SSH 使用者名稱和密碼。
1. 輸入金鑰之後，請按一下 **[連線]** 按鈕。

## <a name="connect-manually-enter-a-private-key"></a><a name="privatekey"></a>連接：手動輸入私密金鑰

1. 開啟 [Azure 入口網站](https://portal.azure.com)。 流覽至您要連接的虛擬機器，然後按一下 [連線] **，然後從** 下拉式清單 **中選取 [** 防禦]。

   ![螢幕擷取畫面顯示已選取 [連線] Azure 入口網站中的虛擬機器總覽。](./media/bastion-connect-vm-ssh/connect.png)
1. 按一下 [防禦] 之後，會出現一個具有三個索引標籤的側邊列– RDP、SSH 和防禦。 如果已針對虛擬網路布建防禦功能，則預設會使用 [防禦] 索引標籤。 如果您未布建虛擬網路的防禦，請參閱 [設定](bastion-create-host-portal.md)防禦。

   ![螢幕擷取畫面顯示已選取防禦的 [連線至虛擬機器] 對話方塊。](./media/bastion-connect-vm-ssh/bastion.png)
1. 輸入使用者名稱，然後選取 **SSH 私密金鑰**。
1. 在 [文字區域 **SSH 私密金鑰** ] (輸入您的私密金鑰，或直接將它貼) 。
1. 輸入金鑰之後，請按一下 **[連線]** 按鈕。

## <a name="connect-using-a-private-key-file"></a><a name="ssh"></a>連接：使用私密金鑰檔案

1. 開啟 [Azure 入口網站](https://portal.azure.com)。 流覽至您要連接的虛擬機器，然後按一下 [連線] **，然後從** 下拉式清單 **中選取 [** 防禦]。

   ![螢幕擷取畫面顯示已選取 [連線] Azure 入口網站中的虛擬機器總覽。](./media/bastion-connect-vm-ssh/connect.png)
1. 按一下 [防禦] 之後，會出現一個具有三個索引標籤的側邊列– RDP、SSH 和防禦。 如果已針對虛擬網路布建防禦功能，則預設會使用 [防禦] 索引標籤。 如果您未布建虛擬網路的防禦，請參閱 [設定](bastion-create-host-portal.md)防禦。

   ![螢幕擷取畫面顯示已選取防禦的 [連線至虛擬機器] 對話方塊。](./media/bastion-connect-vm-ssh/bastion.png)
1. 輸入使用者名稱，然後 **從本機檔案選取 SSH 私密金鑰**。
1. 按一下 [ **流覽]** 按鈕 (本機檔案) 中的資料夾圖示。
1. 流覽檔案，然後按一下 [ **開啟**]。
1. 按一下 **[連線]** 以連線至 VM。 按一下 [連線] 之後，就會在 Azure 入口網站中直接開啟此虛擬機器的 SSH。 此連線會透過您虛擬機器私人 IP 上的防禦服務上的埠443，透過 HTML5 來進行。

## <a name="next-steps"></a>下一步

閱讀防禦 [常見問題](bastion-faq.md)
