---
title: 如何從 Mac 連線到 Azure 實驗室服務 VM | Microsoft Docs
description: 了解如何從 Mac 連線到 Azure 實驗室服務中的虛擬機器。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 674bea13093e185ae991148c2cf96599a273d0bc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85444619"
---
# <a name="connect-to-a-vm-using-remote-desktop-protocol-on-a-mac"></a>使用 Mac 上的遠端桌面通訊協定連線到 VM
本節說明學生如何使用 RDP 從 Mac 連線到教室實驗室 VM。

## <a name="install-microsoft-remote-desktop-on-a-mac"></a>在 Mac 上安裝 Microsoft 遠端桌面
1. 在您的 Mac 上開啟 App Store，然後搜尋 **Microsoft 遠端桌面**。

    ![Microsoft 遠端桌面](./media/how-to-use-classroom-lab/install-ms-remote-desktop.png)
1. 安裝最新版的 Microsoft 遠端桌面。 

## <a name="access-the-vm-from-your-mac-using-rdp"></a>使用 RDP 從 Mac 存取 VM
1. 在已安裝 **Microsoft 遠端桌面**的電腦上，開啟已下載的 **RDP** 檔案。 它應該會開始連線至 VM。 

    ![連接到 VM](./media/how-to-use-classroom-lab/connect-linux-vm.png)
1. 如果出現下列警告，請選取 [繼續]。 

    ![憑證警告](./media/how-to-use-classroom-lab/certificate-error.png)
1. 您應該會看到 VM。 

    > [!NOTE]
    > 下列範例適用於 CentOS Linux VM。 

    ![VM](./media/how-to-use-classroom-lab/vm-ui.png)


## <a name="next-steps"></a>後續步驟
若要了解如何使用 RDP 連線到 Linux VM，請參閱[使用 Linux 虛擬機器的遠端桌面](how-to-use-remote-desktop-linux-student.md)


