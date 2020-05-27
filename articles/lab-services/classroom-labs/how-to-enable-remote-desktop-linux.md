---
title: 在 Azure Lab Services 中啟用適用於 Linux 的遠端桌面 | Microsoft Docs
description: 了解如何在 Azure Lab Services 中為實驗室中的 Linux 虛擬機器啟用遠端桌面。
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
ms.date: 04/30/2020
ms.author: spelluru
ms.openlocfilehash: a40575340fc5b1c202be6b001807085954439f03
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588151"
---
# <a name="enable-remote-desktop-for-linux-virtual-machines-in-a-lab-in-azure-lab-services"></a>在 Azure Lab Services 中為實驗室中的 Linux 虛擬機器啟用遠端桌面
本文示範如何執行下列工作：

- 為 Linux VM 啟用遠端桌面
- 教師如何透過遠端桌面連線 (RDP) 連線至範本 VM。

## <a name="enable-remote-desktop-for-linux-vm"></a>為 Linux VM 啟用遠端桌面
在實驗室建立期間，教師可以針對 **Linux** 映像啟用**遠端桌面連線**。 若已針對範本選取 Linux 映像，則會顯示 [啟用遠端桌面連線] 選項。 啟用此選項後，教師可透過 RDP (遠端桌面) 連線到範本 VM 和學生 VM。 

![啟用 Linux 映像的遠端桌面連線](../media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

在 [啟用遠端桌面連線] 訊息方塊中，選取 [繼續使用遠端桌面]。 

![啟用 Linux 映像的遠端桌面連線](../media/how-to-enable-remote-desktop-linux/enabling-remote-desktop-connection-dialog.png)

> [!IMPORTANT] 
> 啟用**遠端桌面連線**，只會在 Linux 電腦上開啟 **RDP** 連接埠。 如果已在虛擬機器映像上安裝和設定 RDP，您/學生即可透過 RDP 連線到 VM，而不需執行任何額外的步驟。
> 
> 如果 VM 映像並未安裝和設定 RDP，您必須第一次使用 SSH 連線到 Linux 電腦並安裝 RDP 和 GUI 套件，讓您/學生稍後可以使用 RDP 連線到 Linux 電腦。 如需詳細資訊，請參閱[在 Azure 中安裝和設定遠端桌面以連線至 Linux VM](../../virtual-machines/linux/use-remote-desktop.md)。 接著，您會發佈映像，以便學生透過 RDP 連線到學生 Linux VM。 

## <a name="supported-operating-systems"></a>支援的作業系統
目前，下列作業系統支援遠端桌面連線：

- openSUSE Leap 42.3
- CentOS 型 7.5
- Debian 9 "Stretch"
- Ubuntu Server 16.04 LTS

## <a name="connect-to-the-template-vm"></a>連線至範本 VM 
教師必須先使用 SSH 連線到範本 VM，然後在其上安裝 RDP 和 GUI 套件。 然後，教師可以使用 RDP 來連線至範本 VM： 

1. 如果您在工具列上看到 [自訂範本]，請加以選取。 接著，在 [自訂範本] 對話方塊上選取 [繼續]。 此動作會啟動範本 VM。  

    ![自訂範本](../media/how-to-enable-remote-desktop-linux/customize-template.png)
2. 啟動範本 VM 之後，您可選取 [連線範本]，然後選取工具列上的 [透過 SSH 連線]。 

    ![在實驗室建立後透過 RDP 連線到範本](../media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 
3. 您會看到下列 [連線到虛擬機器] 對話方塊。 選取文字方塊旁邊的 [複製] 按鈕，將其複製到剪貼簿。 儲存 SSH 連接字串。 使用此連接字串，從 SSH 終端機 (例如 [Putty](https://www.putty.org/)) 連線至虛擬機器。
 
    ![SSH 連接字串](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)
4. 安裝 RDP 和 GUI 套件，讓您/學生稍後可以使用 RDP 連線到 Linux 電腦。 如需詳細資訊，請參閱[在 Azure 中安裝和設定遠端桌面以連線至 Linux VM](../../virtual-machines/linux/use-remote-desktop.md)。 接著，您會發佈映像，以便學生透過 RDP 連線到學生 Linux VM。
5. 安裝這些套件之後，您可使用工具列上的 [連線至範本]，然後選取 [透過 RDP 連線] 以透過 RDP 連線到範本 VM。 儲存 RDP 檔案並使用該檔案，以透過 RDP 連線到範本 VM。 

## <a name="next-steps"></a>後續步驟
在講師啟用遠端桌面連線功能之後，學生就可以透過 RDP/SSH 連線到其 VM。 如需詳細資訊，請參閱[在教室實驗室中使用適用於 Linux VM 的遠端桌面](how-to-use-remote-desktop-linux-student.md)。 