---
title: 使用 Azure 防禦連接到 Windows 虛擬機器擴展集 |Microsoft Docs
description: 在本文中，您將瞭解如何使用 Azure 防禦來連線到 Azure 虛擬機器擴展集。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 4f513aaf113ef4bd6e75e5c4b31e0f0252d45f10
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988085"
---
# <a name="connect-to-a-virtual-machine-scale-set-using-azure-bastion"></a>使用 Azure 防禦連接到虛擬機器擴展集

本文說明如何在 Azure 虛擬網路中使用 Azure 防禦，安全且順暢地透過 RDP 連線到您的 Windows 虛擬機器擴展集實例。 您可以直接從 Azure 入口網站連接到虛擬機器擴展集實例。 使用 Azure Bastion 時，VM 不需要用戶端、代理程式或其他軟體。 如需 Azure 防禦的詳細資訊，請參閱[總覽](bastion-overview.md)。

## <a name="before-you-begin"></a>開始之前

請確定您已為虛擬機器擴展集所在的虛擬網路設定 Azure 防禦主機。 如需詳細資訊，請參閱[建立 Azure 防禦主機](bastion-create-host-portal.md)。 在虛擬網路中布建並部署防禦服務之後，您就可以使用它來連線到此虛擬網路中的虛擬機器擴展集實例。 防禦會假設您使用 RDP 來連線到 Windows 虛擬機器擴展集，而 SSH 則是用來連線到您的 Linux 虛擬機器擴展集。 如需 Linux VM 連線的相關資訊，請參閱[連接到 VM-linux](bastion-connect-vm-ssh.md)。

## <a name="rdp"></a>使用 RDP 連接

1. 開啟 [Azure 入口網站](https://portal.azure.com)。 流覽至您想要連接的虛擬機器擴展集。

   ![定位](./media/bastion-connect-vm-scale-set/1.png)
2. 流覽至您想要連接的虛擬機器擴展集實例，然後選取 [連線 **]** 。 使用 RDP 連線時，虛擬機器擴展集應為 Windows 虛擬機器擴展集。

   ![虛擬機器擴展集](./media/bastion-connect-vm-scale-set/2.png)
3. 選取 [連線 **]** 之後，會出現具有三個索引標籤（RDP、SSH 和防禦）的側條。 從側邊列**選取 [防禦**] 索引標籤。 如果您未布建虛擬網路的防禦，您可以選取連結來設定防禦。 如需設定指示，請參閱[設定](bastion-create-host-portal.md)防禦。

   ![防禦索引標籤](./media/bastion-connect-vm-scale-set/3.png)
4. 在 [防禦] 索引標籤上，輸入虛擬機器擴展集的使用者名稱和密碼，然後選取 **[連線]** 。

   ![連線](./media/bastion-connect-vm-scale-set/4.png)
5. 透過防禦的此虛擬機器的 RDP 連線，會使用埠443和防禦服務，直接在 Azure 入口網站（透過 HTML5）中開啟。

## <a name="next-steps"></a>後續步驟

閱讀防禦[常見問題](bastion-faq.md)。