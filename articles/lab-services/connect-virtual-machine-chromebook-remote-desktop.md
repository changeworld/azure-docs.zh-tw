---
title: 如何從 Chromebook 連線到 Azure 實驗室服務 VM | Microsoft Docs
description: 了解如何從 Chromebook 連線到 Azure 實驗室服務中的虛擬機器。
services: devtest-lab, lab-services, virtual-machines
author: nicolela
ms.topic: article
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: ae2bc6e87f0b694e949057280233c19e0b98132c
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92892225"
---
# <a name="connect-to-a-vm-using-remote-desktop-protocol-on-a-chromebook"></a>使用 Chromebook 上的遠端桌面通訊協定連線到 VM
本節說明學生如何使用 RDP 從 Chromebook 連線到教室實驗室 VM。

## <a name="install-microsoft-remote-desktop-on-a-chromebook"></a>在 Chromebook 上安裝 Microsoft 遠端桌面
1. 在您的 Chromebook 上開啟 App Store，然後搜尋 **Microsoft 遠端桌面** 。

    ![Microsoft 遠端桌面](./media/how-to-use-classroom-lab/install-ms-remote-desktop-chromebook.png)
1. 安裝最新版的 Microsoft 遠端桌面。 

## <a name="access-the-vm-from-your-chromebook-using-rdp"></a>使用 RDP 從 Chromebook 存取 VM
1. 在已安裝 **Microsoft 遠端桌面** 的電腦上，開啟已下載的 **RDP** 檔案。 它應該會開始連線至 VM。 

    ![連接到 VM](./media/how-to-use-classroom-lab/connect-vm-chromebook.png)

1. 在出現提示時，請輸入您的密碼。
    ![顯示登入畫面的螢幕擷取畫面，您可以在其中輸入使用者名稱和密碼。](./media/how-to-use-classroom-lab/password-chromebook.png)


1. 如果出現下列警告，請選取 [繼續]。 

    ![憑證警告](./media/how-to-use-classroom-lab/certificate-error-chromebook.png)

1. 您應該會看到所連線 VM 的桌面。

## <a name="next-steps"></a>後續步驟
若要深入了解如何連線到 Linux VM，請參閱[連線到 Linux 虛擬機器](how-to-use-remote-desktop-linux-student.md)


