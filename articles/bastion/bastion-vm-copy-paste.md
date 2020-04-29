---
title: 複製和貼上虛擬機器： Azure 防禦
description: 在本文中，您將瞭解如何使用防禦，在 Azure VM 之間進行複製和貼上。
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: 0af0fdfa1b0bcbb06d9424390e7ca8c8f406bcb3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80619311"
---
# <a name="copy-and-paste-to-a-virtual-machine-azure-bastion"></a>複製並貼到虛擬機器： Azure 防禦

本文可協助您在使用 Azure 防禦時，從虛擬機器複製和貼上文字。 使用 VM 之前，請確定您已遵循[建立防禦主機](bastion-create-host-portal.md)的步驟。 然後，使用[RDP](bastion-connect-vm-rdp.md)或[SSH](bastion-connect-vm-ssh.md)連接到您想要使用的 VM。

針對支援先進剪貼簿 API 存取的瀏覽器，您可以使用在本機裝置上的應用程式之間複製和貼上的相同方式，在本機裝置和遠端會話之間複製並貼上文字。 若為其他瀏覽器，您可以使用防禦剪貼簿存取工具選擇區。

   ![允許剪貼簿](./media/bastion-vm-manage/allow.png)

僅支援文字複製/貼上。 若要直接複製並貼上，您的瀏覽器可能會在防禦會話初始化時提示您提供剪貼簿存取。 **允許**網頁存取剪貼簿。

## <a name="copy-to-a-remote-session"></a><a name="to"></a>複製到遠端會話

使用[Azure 入口網站](https://portal.azure.com)連接到虛擬機器之後，請完成下列步驟：

1. 將文字/內容從本機裝置複製到本機剪貼簿。
1. 在遠端會話期間，選取兩個箭號來啟動 [防禦剪貼簿存取工具] 選擇區。 箭號位於會話的左邊。

   ![工具調色板](./media/bastion-vm-manage/left.png)

   ![剪貼簿](./media/bastion-vm-manage/clipboard.png)
1. 通常，複製的文字會自動顯示在防禦複製貼上調色板上。 如果您的文字不在該處，請將文字貼入調色板的文字區域中。
1. 文字出現在文字區域中之後，您就可以將它貼到遠端會話。

   ![貼上](./media/bastion-vm-manage/local.png)

## <a name="copy-from-a-remote-session"></a><a name="from"></a>從遠端會話複製

使用[Azure 入口網站](https://portal.azure.com)連接到虛擬機器之後，請完成下列步驟：

1. 將遠端會話的文字/內容複寫到遠端剪貼簿（使用 Ctrl-c）。

   ![工具調色板](./media/bastion-vm-manage/remote.png)
1. 在遠端會話期間，選取兩個箭號來啟動 [防禦剪貼簿存取工具] 選擇區。 箭號位於會話的左邊。

   ![剪貼簿](./media/bastion-vm-manage/clipboard2.png)
1. 通常，複製的文字會自動顯示在防禦複製貼上調色板上。 如果您的文字不在該處，請將文字貼入調色板的文字區域中。
1. 文字出現在文字區域中之後，您就可以將它貼到本機裝置上。

   ![貼上](./media/bastion-vm-manage/local2.png)
 
## <a name="next-steps"></a>後續步驟

閱讀防禦[常見問題](bastion-faq.md)。