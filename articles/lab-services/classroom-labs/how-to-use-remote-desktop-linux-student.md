---
title: 在 Azure 實驗室服務中使用適用于 Linux 的遠端桌面 |Microsoft Docs
description: 瞭解如何在 Azure 實驗室服務的實驗室中，使用適用于 Linux 虛擬機器的遠端桌面。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "73585089"
---
# <a name="use-remote-desktop-for-linux-virtual-machines-in-a-classroom-lab-of-azure-lab-services"></a>在 Azure 實驗室服務的教室實驗室中使用適用于 Linux 虛擬機器的遠端桌面
本文將說明學生如何使用 RDP/SSH 連線到實驗室中的 Linux 虛擬機器（VM）。 

講師必須啟用 [遠端桌面連線] 功能，學生才能連線到教室實驗室的 VM。 如需講師如何啟用「遠端桌面連線」功能的指示，請參閱[啟用 Linux 虛擬機器的遠端桌面](how-to-enable-remote-desktop-linux.md)。

> [!IMPORTANT] 
> 啟用「**遠端桌面**連線」只會開啟 Linux 電腦上的**RDP**埠。 講師可以在第一次使用 SSH 連線到 Linux 機器，並安裝 RDP 和 GUI 套件，讓您稍後可以使用 RDP 連接到 Linux 機器。 

## <a name="connect-to-the-student-vm"></a>連接到學生 VM
當實驗室擁有者（老師/教授）使用電腦上安裝的 RDP 和 GUI 封裝來**發佈**範本 VM 之後，學生可以透過 rdp 連線到其 Linux vm。 以下為其步驟： 

1. 當學生直接登入 Labs 入口網站時（`https://labs.azure.com`），或使用註冊連結（`https://labs.azure.com/register/<registrationCode>`）時，就會顯示學生擁有存取權的每個實驗室的磚。 
2. 在圖格上，切換按鈕以啟動 VM （如果它處於已停止狀態）。 
3. 選取 [連接]  。 您會看到兩個連接到 VM 的選項： **SSH**和**遠端桌面**。

    ![學生 VM-連線選項](../media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)

## <a name="connect-using-ssh-or-rdp"></a>使用 SSH 或 RDP 連接
如果您選取 [ **SSH** ] 選項，您會看到下列 [連線**到您的虛擬機器**] 對話方塊：  

![SSH 連接字串](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

選取文字方塊旁邊的 [**複製**] 按鈕，將它複製到剪貼簿。 儲存 SSH 連接字串。 使用此連接字串，從 SSH 終端機 (例如 [Putty](https://www.putty.org/)) 連線至虛擬機器。

如果您選取**rdp**選項，系統會將 rdp 檔案下載到您的電腦上。 儲存並開啟它以連接到電腦。 

## <a name="next-steps"></a>後續步驟
若要瞭解如何在教室實驗室中啟用 Linux Vm 的遠端桌面連線功能，請參閱[啟用 linux 虛擬機器的遠端桌面](how-to-enable-remote-desktop-linux.md)。 

