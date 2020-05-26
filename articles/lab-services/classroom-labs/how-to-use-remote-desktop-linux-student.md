---
title: 在 Azure 實驗室服務中使用適用於 Linux 的遠端桌面 | Microsoft Docs
description: 了解如何在 Azure 實驗室服務的實驗室中使用適用於 Linux 虛擬機器的遠端桌面。
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
ms.openlocfilehash: 8ccad0698ea6560dd183cacc71f5f3a644e8220c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588100"
---
# <a name="use-remote-desktop-for-linux-virtual-machines-in-a-classroom-lab-of-azure-lab-services"></a>在 Azure 實驗室服務的教室實驗室中使用適用於 Linux 虛擬機器的遠端桌面
本文說明學生該如何使用 RDP/SSH 連線到實驗室中的 Linux 虛擬機器 (VM)。 

講師必須先啟用遠端桌面連線功能，學生才能連線到教室實驗室的 VM。 如需講師該如何啟用遠端桌面連線功能的指示，請參閱[為 Linux 虛擬機器啟用遠端桌面](how-to-enable-remote-desktop-linux.md)。

> [!IMPORTANT] 
> 啟用**遠端桌面連線**只會在 Linux 機器上開啟 **RDP** 連接埠。 講師可以在第一次時使用 SSH 連線到 Linux 機器，並安裝 RDP 和 GUI 套件，以便稍後可以使用 RDP 來連線到 Linux 機器。 

## <a name="connect-to-the-student-vm"></a>連線到學生 VM
在實驗室擁有者 (授課者) **發佈**範本 VM 且機器上安裝了 RDP 和 GUI 套件之後，學生就可以透過 RDP 連線到其 Linux VM。 以下為其步驟： 

1. 當學生直接登入實驗室入口網站 (`https://labs.azure.com`) 或藉由使用註冊連結 (`https://labs.azure.com/register/<registrationCode>`) 來登入時，系統就會針對學生可存取的每個實驗室顯示各自的圖格。 
2. 在圖格上，切換按鈕以啟動 VM (如果 VM 處於已停止狀態的話)。 
3. 選取 [連接]。 您會看到兩個可連線至 VM 的選項：**SSH** 和**遠端桌面**。

    ![學生 VM - 連線選項](../media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)

## <a name="connect-using-ssh-or-rdp"></a>使用 SSH 或 RDP 來連線
如果您選取 [SSH] 選項，就會看到下列 [連線到虛擬機器] 對話方塊：  

![SSH 連接字串](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

選取文字方塊旁邊的 [複製] 按鈕，將其複製到剪貼簿。 儲存 SSH 連接字串。 使用此連接字串，從 SSH 終端機 (例如 [Putty](https://www.putty.org/)) 連線至虛擬機器。

如果您選取 [RDP] 選項，系統就會將 RDP 檔案下載到您的機器上。 請儲存該檔案，並將其開啟以連線到機器。 

## <a name="next-steps"></a>後續步驟
若要了解如何為教室實驗室中的 Linux VM 啟用遠端桌面連線功能，請參閱[為 Linux 虛擬機器啟用遠端桌面](how-to-enable-remote-desktop-linux.md)。 

