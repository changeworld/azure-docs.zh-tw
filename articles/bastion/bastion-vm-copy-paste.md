---
title: 複製和貼上到虛擬機器和從虛擬機器:Azure 堡壘
description: 在本文中,瞭解如何使用 Bastion 複製和貼上到 Azure VM。
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: 0af0fdfa1b0bcbb06d9424390e7ca8c8f406bcb3
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619311"
---
# <a name="copy-and-paste-to-a-virtual-machine-azure-bastion"></a>複製並貼上到虛擬機器:Azure 堡壘

本文可説明您在使用 Azure 堡壘時向虛擬機複製和粘貼文本。 在使用 VM 之前,請確保已遵循[創建堡壘主機](bastion-create-host-portal.md)的步驟。 然後,使用[RDP](bastion-connect-vm-rdp.md)或[SSH](bastion-connect-vm-ssh.md)連接到要使用的 VM。

對於支援進階剪貼簿 API 存取的瀏覽器,可以在本地裝置和遠端作業階段之間複製和貼貼文本,就像在本地裝置上的應用程式之間複製和貼上文字一樣。 對於其他瀏覽器,您可以使用堡壘剪貼簿訪問工具調色板。

   ![允許剪貼簿](./media/bastion-vm-manage/allow.png)

僅支援文本複製/粘貼。 對於直接複製和粘貼,您的瀏覽器可能會提示您在初始化堡壘會話時訪問剪貼簿。 **允許**網頁訪問剪貼簿。

## <a name="copy-to-a-remote-session"></a><a name="to"></a>複製到遠端作業階段

使用[Azure 門戶](https://portal.azure.com)連接到虛擬機器後,請完成以下步驟:

1. 將本地裝置的文本/內容複製到本地剪貼簿中。
1. 在遠端作業階段期間,通過選擇兩個箭頭啟動堡壘剪貼簿訪問工具調色板。 箭頭位於會話的左中心。

   ![工具調色盤](./media/bastion-vm-manage/left.png)

   ![剪貼簿](./media/bastion-vm-manage/clipboard.png)
1. 通常,複製的文本會自動顯示在堡壘複製粘貼調色板上。 如果文本不存在,則將文本貼上到調色板上的文本區域中。
1. 文本進入文本區域后,可以將其粘貼到遠端會話。

   ![貼上](./media/bastion-vm-manage/local.png)

## <a name="copy-from-a-remote-session"></a><a name="from"></a>從遠端作業階段複製

使用[Azure 門戶](https://portal.azure.com)連接到虛擬機器後,請完成以下步驟:

1. 將遠端作業階段中的文字/內容複製到遠端剪貼簿(使用 Ctrl-C)。

   ![工具調色盤](./media/bastion-vm-manage/remote.png)
1. 在遠端作業階段期間,通過選擇兩個箭頭啟動堡壘剪貼簿訪問工具調色板。 箭頭位於會話的左中心。

   ![剪貼簿](./media/bastion-vm-manage/clipboard2.png)
1. 通常,複製的文本會自動顯示在堡壘複製粘貼調色板上。 如果文本不存在,則將文本貼上到調色板上的文本區域中。
1. 文本進入文本區域后,可以將其粘貼到本地設備。

   ![貼上](./media/bastion-vm-manage/local2.png)
 
## <a name="next-steps"></a>後續步驟

閱讀[的移除的裝置的錯誤 。](bastion-faq.md)