---
title: 使用 Azure 堡壘連接到 Linux VM
description: 在本文中，瞭解如何使用 Azure 堡壘連接到 Linux 虛擬機器。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: cherylmc
ms.openlocfilehash: 381f45a51002188c72174cff83c26b829912a0b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596822"
---
# <a name="connect-using-ssh-to-a-linux-virtual-machine-using-azure-bastion"></a>使用 SSH 連接到使用 Azure 堡壘的 Linux 虛擬機器

本文介紹如何在 Azure 虛擬網路中安全地將 SSH 無縫地連接到 Linux VM。 可以直接從 Azure 門戶連接到 VM。 使用 Azure 堡壘時，VM 不需要用戶端、代理或其他軟體。 有關 Azure 堡壘的詳細資訊，請參閱[概述](bastion-overview.md)。

您可以使用 Azure 堡壘使用 SSH 連接到 Linux 虛擬機器。 您可以使用使用者名/密碼和 SSH 金鑰進行身份驗證。 您可以使用以下任一操作使用 SSH 金鑰連接到 VM：

* 手動輸入的私密金鑰
* 包含私密金鑰資訊的檔

SSH 私密金鑰必須採用以`"-----BEGIN RSA PRIVATE KEY-----"`開頭和以`"-----END RSA PRIVATE KEY-----"`結尾的格式。

## <a name="before-you-begin"></a>開始之前

請確保為 VM 所在的虛擬網路設置了 Azure 堡壘主機。 有關詳細資訊，請參閱創建[Azure 堡壘主機](bastion-create-host-portal.md)。 在虛擬網路中預配和部署 Bastion 服務後，可以使用它連接到此虛擬網路中的任何 VM。 

當您使用 Bastion 進行連接時，它假定您正在使用 RDP 連接到 Windows VM，SSH 連接到 Linux VM。 有關連接到 Windows VM 的資訊，請參閱[連接到 VM - Windows](bastion-connect-vm-rdp.md)。

### <a name="required-roles"></a>所需角色

若要建立連線，必須具備下列角色：

* 虛擬機器上的讀取者角色
* 虛擬機器的私人 IP 位址與 NIC 上的讀取者角色
* Azure Bastion 資源上的讀取者角色

### <a name="ports"></a>連接埠

要通過 SSH 連接到 Linux VM，必須在 VM 上打開以下埠：

* 入站埠： SSH （22）

## <a name="connect-using-username-and-password"></a><a name="username"></a>連接：使用使用者名和密碼

1. 打開[Azure 門戶](https://portal.azure.com)。 導航到要連接到的虛擬機器，然後按一下 **"連接**"並從下拉清單中選擇 **"堡壘**"。

   ![連線](./media/bastion-connect-vm-ssh/connect.png)
1. 按一下"堡壘"後，將顯示一個側欄，該邊欄具有三個選項卡 - RDP、SSH 和堡壘。 如果為虛擬網路預配了堡壘，則預設情況下堡壘選項卡處於活動狀態。 如果未為虛擬網路預配堡壘，請參閱[配置堡壘](bastion-create-host-portal.md)。

   ![Bastion](./media/bastion-connect-vm-ssh/bastion.png)
1. 向虛擬機器輸入 SSH 的使用者名和密碼。
1. 輸入鍵後按一下 **"連接**"按鈕。

## <a name="connect-manually-enter-a-private-key"></a><a name="privatekey"></a>連接：手動輸入私密金鑰

1. 打開[Azure 門戶](https://portal.azure.com)。 導航到要連接到的虛擬機器，然後按一下 **"連接**"並從下拉清單中選擇 **"堡壘**"。

   ![連線](./media/bastion-connect-vm-ssh/connect.png)
1. 按一下"堡壘"後，將顯示一個側欄，該邊欄具有三個選項卡 - RDP、SSH 和堡壘。 如果為虛擬網路預配了堡壘，則預設情況下堡壘選項卡處於活動狀態。 如果未為虛擬網路預配堡壘，請參閱[配置堡壘](bastion-create-host-portal.md)。

   ![Bastion](./media/bastion-connect-vm-ssh/bastion.png)
1. 輸入使用者名並選擇**SSH 私密金鑰**。
1. 將私密金鑰輸入文本區域**SSH 私密金鑰**（或直接粘貼）。
1. 輸入鍵後按一下 **"連接**"按鈕。

## <a name="connect-using-a-private-key-file"></a><a name="ssh"></a>連接：使用私密金鑰檔

1. 打開[Azure 門戶](https://portal.azure.com)。 導航到要連接到的虛擬機器，然後按一下 **"連接**"並從下拉清單中選擇 **"堡壘**"。

   ![連線](./media/bastion-connect-vm-ssh/connect.png)
1. 按一下"堡壘"後，將顯示一個側欄，該邊欄具有三個選項卡 - RDP、SSH 和堡壘。 如果為虛擬網路預配了堡壘，則預設情況下堡壘選項卡處於活動狀態。 如果未為虛擬網路預配堡壘，請參閱[配置堡壘](bastion-create-host-portal.md)。

   ![Bastion](./media/bastion-connect-vm-ssh/bastion.png)
1. 輸入使用者名並從**本地檔中選擇 SSH 私密金鑰**。
1. 按一下 **"流覽"** 按鈕（本地檔中的資料夾圖示）。
1. 流覽該檔，然後按一下"**打開**"。
1. 按一下"**連接**"以連接到 VM。 按一下"連接"後，SSH 將直接在 Azure 門戶中打開。 此連接通過 HTML5 使用堡壘服務上的埠 443 覆蓋虛擬機器的專用 IP。

## <a name="next-steps"></a>後續步驟

閱讀[堡壘常見問題解答](bastion-faq.md)
