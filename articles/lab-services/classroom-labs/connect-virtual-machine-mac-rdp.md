---
title: 如何從 Mac 連接到 Azure 實驗室服務 VM |微軟文檔
description: 本文
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/17/2020
ms.author: spelluru
ms.openlocfilehash: 0d3484c1008e00bcfde3adb399e925c0e054f49e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79503084"
---
# <a name="connect-to-a-vm-using-rdp-on-a-mac"></a>在 Mac 上使用 RDP 連線至 VM
本節演示如何使用 RDP 從 Mac 連接到教室實驗室 VM。

## <a name="install-microsoft-remote-desktop-on-a-mac"></a>在 Mac 上安裝 Microsoft 遠端桌面
1. 在您的 Mac 上開啟 App Store，然後搜尋 **Microsoft 遠端桌面**。

    ![Microsoft 遠端桌面](../media/how-to-use-classroom-lab/install-ms-remote-desktop.png)
1. 安裝最新版的 Microsoft 遠端桌面。 

## <a name="access-the-vm-from-your-mac-using-rdp"></a>使用 RDP 從 Mac 存取 VM
1. 在已安裝 **Microsoft 遠端桌面**的電腦上，開啟已下載的 **RDP** 檔案。 它應該會開始連線至 VM。 

    ![連接到 VM](../media/how-to-use-classroom-lab/connect-linux-vm.png)
1. 如果出現下列警告，請選取 [繼續]****。 

    ![憑證警告](../media/how-to-use-classroom-lab/certificate-error.png)
1. 您應該會看到 VM。 

    > [!NOTE]
    > 下列範例適用於 CentOS Linux VM。 

    ![VM](../media/how-to-use-classroom-lab/vm-ui.png)


## <a name="next-steps"></a>後續步驟
要瞭解如何使用 RDP 連接到 Linux VM，請參閱[為 Linux 虛擬機器使用遠端桌面](how-to-use-remote-desktop-linux-student.md)


