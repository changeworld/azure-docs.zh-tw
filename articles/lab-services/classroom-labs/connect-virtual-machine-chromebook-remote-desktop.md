---
title: 如何從 Chromebook 連線到 Azure 實驗室服務 VM | Microsoft Docs
description: 了解如何從 Chromebook 連線到 Azure 實驗室服務中的虛擬機器。
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
ms.date: 05/11/2020
ms.author: nicolela
ms.openlocfilehash: 581ba32f536728fec88ddf3120637f9347e2e925
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83704373"
---
# <a name="connect-to-a-vm-using-remote-desktop-protocol-on-a-chromebook"></a>使用 Chromebook 上的遠端桌面通訊協定連線到 VM
本節說明學生如何使用 RDP 從 Chromebook 連線到教室實驗室 VM。

## <a name="install-microsoft-remote-desktop-on-a-chromebook"></a>在 Chromebook 上安裝 Microsoft 遠端桌面
1. 在您的 Chromebook 上開啟 App Store，然後搜尋 **Microsoft 遠端桌面**。

    ![Microsoft 遠端桌面](../media/how-to-use-classroom-lab/install-ms-remote-desktop-chromebook.png)
1. 安裝最新版的 Microsoft 遠端桌面。 

## <a name="access-the-vm-from-your-chromebook-using-rdp"></a>使用 RDP 從 Chromebook 存取 VM
1. 在已安裝 **Microsoft 遠端桌面**的電腦上，開啟已下載的 **RDP** 檔案。 它應該會開始連線至 VM。 

    ![連接到 VM](../media/how-to-use-classroom-lab/connect-vm-chromebook.png)

1. 在出現提示時，請輸入您的密碼。
    ![連線到 VM](../media/how-to-use-classroom-lab/password-chromebook.png)


1. 如果出現下列警告，請選取 [繼續]。 

    ![憑證警告](../media/how-to-use-classroom-lab/certificate-error-chromebook.png)

1. 您應該會看到所連線 VM 的桌面。

## <a name="next-steps"></a>後續步驟
若要深入了解如何連線到 Linux VM，請參閱[連線到 Linux 虛擬機器](how-to-use-remote-desktop-linux-student.md)


