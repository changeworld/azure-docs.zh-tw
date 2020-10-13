---
title: 使用 Azure 防禦連接到 Windows 虛擬機器擴展集 |Microsoft Docs
description: 在本文中，您將瞭解如何使用 Azure 防禦來連線至 Azure 虛擬機器擴展集。
services: bastion
author: charwen
ms.service: bastion
ms.topic: how-to
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: e3dc7ce36e773b5a615b1abf4f50406fcb07826b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84744301"
---
# <a name="connect-to-a-virtual-machine-scale-set-using-azure-bastion"></a>使用 Azure 防禦連接到虛擬機器擴展集

本文說明如何使用 Azure 防禦，在 Azure 虛擬網路中安全且順暢地使用 RDP 連線到您的 Windows 虛擬機器擴展集實例。 您可以直接從 Azure 入口網站連接到虛擬機器擴展集實例。 使用 Azure 防禦時，Vm 不需要用戶端、代理程式或其他軟體。 如需 Azure 防禦的詳細資訊，請參閱 [總覽](bastion-overview.md)。

## <a name="before-you-begin"></a>開始之前

請確定您已為虛擬機器擴展集所在的虛擬網路設定 Azure 堡壘主機。 如需詳細資訊，請參閱 [建立 Azure 堡壘主機](bastion-create-host-portal.md)。 在您的虛擬網路中布建並部署防禦服務之後，您就可以使用它來連線到此虛擬網路中的虛擬機器擴展集實例。 防禦假設您使用 RDP 來連線到 Windows 虛擬機器擴展集，以及使用 SSH 連接到您的 Linux 虛擬機器擴展集。 如需連接至 Linux VM 的詳細資訊，請參閱連線 [到 VM-linux](bastion-connect-vm-ssh.md)。

## <a name="connect-using-rdp"></a><a name="rdp"></a>使用 RDP 連接

1. 開啟 [Azure 入口網站](https://portal.azure.com)。 流覽至您想要連接的虛擬機器擴展集。

   ![瀏覽](./media/bastion-connect-vm-scale-set/1.png)
2. 流覽至您想要連接的虛擬機器擴展集實例，然後選取 [連線 **]**。 使用 RDP 連線時，虛擬機器擴展集應該是 Windows 虛擬機器擴展集。

   ![虛擬機器擴展集](./media/bastion-connect-vm-scale-set/2.png)
3. 選取 [連線 **]** 之後，會出現具有三個索引標籤的側邊列– RDP、SSH 和防禦。 從側邊列選取 [ **防禦] 索引** 標籤。 如果您未布建虛擬網路的防禦，您可以選取連結來設定防禦。 如需設定指示，請參閱 [設定](bastion-create-host-portal.md)防禦。

   ![防禦索引標籤](./media/bastion-connect-vm-scale-set/3.png)
4. 在 [防禦] 索引標籤上，輸入虛擬機器擴展集的使用者名稱和密碼，然後選取 **[連線]**。

   ![連線](./media/bastion-connect-vm-scale-set/4.png)
5. 透過 Bastion 的這個虛擬機器 RDP 連線，會使用連接埠 443 和 Bastion 服務直接在 Azure 入口網站中開啟 (透過 HTML5)。

## <a name="next-steps"></a>後續步驟

閱讀 [Bastion 常見問題集](bastion-faq.md)。