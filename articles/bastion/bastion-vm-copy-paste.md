---
title: 在虛擬機器之間複製和貼上： Azure 防禦
description: 在本文中，瞭解如何使用防禦來複製和貼上 Azure VM。
services: bastion
author: charwen
ms.service: bastion
ms.topic: how-to
ms.date: 05/04/2020
ms.author: charwen
ms.openlocfilehash: 6835c5e1ecbf0fc99a14754d17bb6e6380eda6c8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91445342"
---
# <a name="copy-and-paste-to-a-virtual-machine-azure-bastion"></a>複製並貼上至虛擬機器： Azure 防禦

本文可協助您在使用 Azure 防禦時，在虛擬機器之間複製及貼上文字。 在使用 VM 之前，請確定您已遵循 [建立堡壘主機](bastion-create-host-portal.md)的步驟。 然後，使用 [RDP](bastion-connect-vm-rdp.md) 或 [SSH](bastion-connect-vm-ssh.md)連接到您想要使用的 VM。

針對支援「advanced 剪貼簿 API」存取的瀏覽器，您可以在本機裝置和遠端會話之間複製和貼上文字，就像您在本機裝置上的應用程式之間複製並貼上的方式一樣。 若為其他瀏覽器，您可以使用 [防禦剪貼簿存取工具] 選項區。

>[!NOTE]
>目前只支援文字複製/貼上。
>

   ![允許剪貼簿](./media/bastion-vm-manage/allow.png)

僅支援文字複製/貼上。 針對直接複製和貼上，您的瀏覽器可能會在防禦會話初始化時提示您提供剪貼簿存取。 **允許** 網頁存取剪貼簿。

## <a name="copy-to-a-remote-session"></a><a name="to"></a>複製到遠端會話

使用 [Azure 入口網站 ](https://portal.azure.com)連接到虛擬機器之後，請完成下列步驟：

1. 將文字/內容從本機裝置複製到本機剪貼簿。
1. 在遠端會話期間，選取兩個箭號來啟動 [防禦剪貼簿存取工具] 選項區。 箭號位於會話的左邊。

   ![螢幕擷取畫面，顯示視窗左側醒目提示之工具選擇區的啟動箭號。](./media/bastion-vm-manage/left.png)

   ![螢幕擷取畫面顯示在防禦中複製的文字剪貼簿。](./media/bastion-vm-manage/clipboard.png)
1. 通常複製的文字會自動顯示在 [防禦複製貼上] 調色板上。 如果您的文字不存在，請將文字貼到 [調色板] 上的文字區域中。
1. 文字出現在文字區域之後，您就可以將它貼到遠端會話。

   ![顯示反白顯示覆制/貼上按鈕的螢幕擷取畫面，以及複製到遠端會話的範例文字字串。](./media/bastion-vm-manage/local.png)

## <a name="copy-from-a-remote-session"></a><a name="from"></a>從遠端會話複製

使用 [Azure 入口網站 ](https://portal.azure.com)連接到虛擬機器之後，請完成下列步驟：

1. 使用 Ctrl-c) 將遠端會話中的文字/內容複寫到遠端剪貼簿 (。

   ![工具元件](./media/bastion-vm-manage/remote.png)
1. 在遠端會話期間，選取兩個箭號來啟動 [防禦剪貼簿存取工具] 選項區。 箭號位於會話的左邊。

   ![剪貼簿](./media/bastion-vm-manage/clipboard2.png)
1. 通常複製的文字會自動顯示在 [防禦複製貼上] 調色板上。 如果您的文字不存在，請將文字貼到 [調色板] 上的文字區域中。
1. 文字出現在文字區域之後，您就可以將它貼到本機裝置上。

   ![貼上](./media/bastion-vm-manage/local2.png)
 
## <a name="next-steps"></a>後續步驟

閱讀 [Bastion 常見問題集](bastion-faq.md)。
