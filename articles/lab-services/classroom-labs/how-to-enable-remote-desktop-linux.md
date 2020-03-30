---
title: 在 Azure 實驗室服務中為 Linux 啟用遠端桌面 |微軟文檔
description: 瞭解如何在 Azure 實驗室服務中的實驗室中為 Linux 虛擬機器啟用遠端桌面。
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
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: cb9a3e2b9ddcd0f74bfa4978f0bc3f4eb0688257
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270858"
---
# <a name="enable-remote-desktop-for-linux-virtual-machines-in-a-lab-in-azure-lab-services"></a>在 Azure 實驗室服務中的實驗室中為 Linux 虛擬機器啟用遠端桌面
本文介紹如何執行以下任務：

- 為 Linux VM 啟用遠端桌面
- 教師如何通過遠端桌面連線 （RDP） 連接到範本 VM。

## <a name="enable-remote-desktop-for-linux-vm"></a>為 Linux VM 啟用遠端桌面
在實驗室創建期間，教師可以為**Linux**映射啟用**遠端桌面連線**。 在為範本選擇 Linux 映射時，將顯示啟用**遠端桌面連線**選項。 啟用此選項後，教師可以通過 RDP（遠端桌面）連接到範本 VM 和學生 VM。 

![為 Linux 映射啟用遠端桌面連線](../media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

在**啟用遠端桌面連線**訊息方塊中，選擇"**繼續使用遠端桌面**"。 

![為 Linux 映射啟用遠端桌面連線](../media/how-to-enable-remote-desktop-linux/enabling-remote-desktop-connection-dialog.png)

> [!IMPORTANT] 
> 啟用**遠端桌面連線**僅在 Linux 電腦上打開**RDP**埠。 如果已在虛擬機器映射上安裝和配置 RDP（例如：Ubuntu 資料科學虛擬機器映射），則您/學生可以通過 RDP 連接到 VM，而無需執行任何其他步驟。
> 
> 如果 VM 映射未安裝和配置 RDP，則需要首次使用 SSH 連接到 Linux 電腦，並安裝 RDP 和 GUI 包，以便您/學生以後可以使用 RDP 連接到 Linux 電腦。 有關詳細資訊，請參閱[安裝和配置遠端桌面以連接到 Azure 中的 Linux VM。](../../virtual-machines/linux/use-remote-desktop.md) 然後，發佈映射，以便學生可以將 RDP 連接到學生 Linux VM。 

## <a name="supported-operating-systems"></a>支援的作業系統
目前，以下作業系統支援遠端桌面連線：

- openSUSE Leap 42.3
- CentOS 型 7.5
- Debian 9 "Stretch"
- Ubuntu Server 16.04 LTS

## <a name="connect-to-the-template-vm"></a>連線至範本 VM 
教師必須首先使用 SSH 連接到範本 VM，並在其中安裝 RDP 和 GUI 包。 然後，教師可以使用 RDP 連接到範本 VM： 

1. 如果您在工具列上看到 **"自訂"範本**，請選擇它。 然後，在 **"自訂範本"** 對話方塊中選擇 **"繼續**"。 此操作將啟動範本 VM。  

    ![自訂範本](../media/how-to-enable-remote-desktop-linux/customize-template.png)
2. 啟動範本 VM 後，您可以選擇 **"連接"範本**，然後通過工具列上的**SSH 進行連接**。 

    ![創建實驗室後，通過 RDP 連接到範本](../media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 
3. 您將看到以下 **"連接到虛擬機器"** 對話方塊。 選擇文字方塊旁邊的 **"複製**"按鈕以將其複製到剪貼簿。 保存 SSH 連接字串。 使用此連接字串，從 SSH 終端機 (例如 [Putty](https://www.putty.org/)) 連線至虛擬機器。
 
    ![SSH 連接字串](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)
4. 安裝 RDP 和 GUI 包，以便您/學生以後可以使用 RDP 連接到 Linux 電腦。 有關詳細資訊，請參閱[安裝和配置遠端桌面以連接到 Azure 中的 Linux VM。](../../virtual-machines/linux/use-remote-desktop.md) 然後，發佈映射，以便學生可以將 RDP 連接到學生 Linux VM。
5. 安裝這些包後，可以使用工具列上的 **"連接到"範本**，然後選擇**通過 RDP 連接**以通過 RDP 連接到範本 VM。 保存 RDP 檔，並使用它通過 RDP 連接到範本 VM。 

## <a name="next-steps"></a>後續步驟
教師啟用遠端桌面連線功能後，學生可以通過 RDP/SSH 連接到其 VM。 有關詳細資訊，請參閱[在教室實驗室中為 Linux VM 使用遠端桌面](how-to-use-remote-desktop-linux-student.md)。 