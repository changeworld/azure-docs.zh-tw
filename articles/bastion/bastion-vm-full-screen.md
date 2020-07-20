---
title: Azure 防禦：觀看虛擬機器會話：全螢幕
description: 在本文中，您將瞭解如何將此視圖變更為全螢幕。
services: bastion
author: charwen
ms.service: bastion
ms.topic: how-to
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: f4aa2c5aa3f40802db7be64eeb778866819bfe67
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84744216"
---
# <a name="change-to-full-screen-view-for-a-vm-session-azure-bastion"></a>變更 vm 會話的全螢幕視圖： Azure 防禦

本文可協助您將虛擬機器視圖變更為全螢幕，並回到瀏覽器中。 使用 VM 之前，請確定您已遵循[建立防禦主機](bastion-create-host-portal.md)的步驟。 然後，使用[RDP](bastion-connect-vm-rdp.md)或[SSH](bastion-connect-vm-ssh.md)連接到您想要使用的 VM。

## <a name="launch-the-clipboard-tool"></a>啟動剪貼簿工具

在遠端會話期間，選取位於會話左邊中間的兩個箭號，啟動 [防禦剪貼簿存取工具] 選擇區。

![tools](./media/bastion-vm-manage/left.png)

## <a name="select-full-screen"></a>選取全螢幕

選取 [**全**螢幕] 按鈕，將會話切換至全螢幕體驗。 切換之後，會話就會重新初始化為全螢幕。

![全螢幕](./media/bastion-vm-manage/full-screen.png)
 
## <a name="next-steps"></a>後續步驟

閱讀 [Bastion 常見問題集](bastion-faq.md)。
瞭解如何在 Azure VM 之間進行[複製和貼](bastion-vm-copy-paste.md)上。
