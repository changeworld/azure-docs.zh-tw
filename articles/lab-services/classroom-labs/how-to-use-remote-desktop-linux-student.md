---
title: 在 Azure 實驗室服務中使用遠端桌面進行 Linux |微軟文檔
description: 瞭解如何在 Azure 實驗室服務中的實驗室中使用 Linux 虛擬機器的遠端桌面。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/20/2019
ms.author: spelluru
ms.openlocfilehash: 0f45af2730b05998fc82212c63778c89bb16b6ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73585089"
---
# <a name="use-remote-desktop-for-linux-virtual-machines-in-a-classroom-lab-of-azure-lab-services"></a>在 Azure 實驗室服務的教室實驗室中為 Linux 虛擬機器使用遠端桌面
本文介紹學生如何使用 RDP/SSH 連接到實驗室中的 Linux 虛擬機器 （VM）。 

教師需要啟用遠端桌面連線功能，然後學生才能連接到教室實驗室的 VM。 有關講師如何啟用遠端桌面連線功能的說明，請參閱[為 Linux 虛擬機器啟用遠端桌面](how-to-enable-remote-desktop-linux.md)。

> [!IMPORTANT] 
> 啟用**遠端桌面連線**僅在 Linux 電腦上打開**RDP**埠。 教師首次可以使用 SSH 連接到 Linux 電腦，並安裝 RDP 和 GUI 包，以便以後可以使用 RDP 連接到 Linux 電腦。 

## <a name="connect-to-the-student-vm"></a>連接到學生 VM
在實驗室擁有者（教師/教授）**發佈**範本 VM 並在電腦上安裝了 RDP 和 GUI 包後，學生可以將 RDP 連接到其 Linux VM。 以下為其步驟： 

1. 當學生直接登錄到實驗室門戶 （）`https://labs.azure.com`或使用註冊連結 （`https://labs.azure.com/register/<registrationCode>`）， 將顯示學生有權訪問的每個實驗室的磁貼。 
2. 在磁貼上，如果按鈕處於停止狀態，則切換按鈕以啟動 VM。 
3. 選取 [連接]****。 您將看到兩個連接到 VM 的選項 **：SSH**和**遠端桌面**。

    ![學生 VM - 連接選項](../media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)

## <a name="connect-using-ssh-or-rdp"></a>使用 SSH 或 RDP 連接
如果選擇**SSH**選項，您將看到以下 **"連接到虛擬機器**"對話方塊：  

![SSH 連接字串](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

選擇文字方塊旁邊的 **"複製**"按鈕以將其複製到剪貼簿。 保存 SSH 連接字串。 使用此連接字串，從 SSH 終端機 (例如 [Putty](https://www.putty.org/)) 連線至虛擬機器。

如果選擇**RDP**選項，RDP 檔將下載到您的電腦上。 保存並打開它以連接到電腦。 

## <a name="next-steps"></a>後續步驟
要瞭解如何在教室實驗室中為 Linux VM 啟用遠端桌面連線功能，請參閱[為 Linux 虛擬機器啟用遠端桌面](how-to-enable-remote-desktop-linux.md)。 

