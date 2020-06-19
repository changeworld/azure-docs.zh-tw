---
title: 連線到 Azure Lab Services 中的 Linux VM | Microsoft Docs
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
ms.openlocfilehash: 0658360c093fb1928421f8f1746ed57c4cd347a4
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83700092"
---
# <a name="connect-to-linux-virtual-machines-in-a-classroom-lab-of-azure-lab-services"></a>連線至 Azure 實驗室服務的教室實驗室中的 Linux 虛擬機器
本文說明學生該如何使用下列項目連線到實驗室中的 Linux 虛擬機器 (VM)：
- SSH (安全 shell 通訊協定) 終端機
- GUI (圖形化使用者介面) 遠端桌面

> [!IMPORTANT] 
> SSH 會自動設定，讓學生和講師可以透過 SSH 連線到 Linux VM，而不需進行任何額外的設定。 不過，如果學生需要使用 GUI 遠端桌面進行連線，講師可能需要進行額外的設定。  如需詳細資料，請參閱[啟用 Linux 虛擬機器的遠端桌面](how-to-enable-remote-desktop-linux.md)。

## <a name="connect-to-the-student-vm-using-ssh"></a>使用 SSH 連線到學生 VM

1. 當學生直接登入實驗室入口網站 (`https://labs.azure.com`) 或藉由使用註冊連結 (`https://labs.azure.com/register/<registrationCode>`) 來登入時，系統就會針對學生可存取的每個實驗室顯示各自的圖格。 
   
1. 在圖格上，切換按鈕以啟動 VM (如果 VM 處於已停止狀態的話)。 

2. 選取 [連接]。 您會看到兩個可連線至 VM 的選項：**SSH** 和 **RDP**。

    ![學生 VM - 連線選項](../media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)

3. 選取 [SSH] 選項，您將會看到 [連線到虛擬機器] 對話方塊：  

    ![SSH 連接字串](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

4. 按一下文字方塊旁邊的 [複製] 按鈕，將 SSH 連線資訊複製到剪貼簿。 

5. 儲存 SSH 連線資訊 (例如在文字板中)，以便您在下一個步驟中使用此連線資訊。

6. 從 SSH 終端機 (例如 [Putty](https://www.putty.org/))，連線到您的 VM。

## <a name="connect-to-the-student-vm-using-gui-remote-desktop"></a>使用 GUI 遠端桌面連線到學生 VM
講師可選擇設定 VM，讓學生也可使用 GUI 遠端桌面進行連線。  在此情況下，學生必須向講師請教是否使用 [Microsoft 遠端桌面 (RDP)] 或 [X2Go] 用戶端應用程式連線到其 VM。  這兩個應用程式都可讓學生從遠端連線到其 VM，並在其本機電腦上顯示 Linux 圖形化桌面。

### <a name="connect-to-the-student-vm-using-microsoft-remote-desktop-rdp"></a>使用 Microsoft 遠端桌面 (RDP) 連線到學生 VM
在講師以適用於 Linux 圖形化桌面環境 (例如 XFCE 等) 的 RDP 和 GUI 套件設定實驗室之後，學生可以使用 Microsoft 遠端桌面 (RDP) 連線到其 Linux VM。 以下是連線的步驟： 

1. 在您 VM 的圖格上，確保 VM 正在執行中，然後按一下 [連線]。 您會看到兩個可連線至 VM 的選項：**SSH** 和 **RDP**。

    ![學生 VM - 連線選項](../media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)
2. 選取 [RDP] 選項。  當 RDP 檔案下載到您的電腦時，請將其儲存到您的 VM。

3. 如果您是從 Windows 電腦進行連線，通常已經安裝並設定 Microsoft 遠端桌面 (RDP) 用戶端。  因此，您只需要按一下 RDP 檔案加以開啟，並啟動遠端工作階段。

    相反地，如果您是從 Mac 或 Chromebook 進行連線，請參閱下列步驟：
   - [在 Mac 上使用 RDP 連線至 VM](connect-virtual-machine-mac-remote-desktop.md)。
   - [在 Chromebook 上使用 RDP 連線至 VM](connect-virtual-machine-chromebook-remote-desktop.md)。  

### <a name="connect-to-the-student-vm-using-x2go"></a>使用 X2Go 連線到學生 VM
在講師以適用於 Linux 圖形化桌面環境 (例如 MATE、XFCE 等) 的 X2Go 和 GUI 套件設定實驗室之後，學生可以使用 X2Go 連線到其 Linux VM。

學生必須向講師請教其已安裝哪個 Linux 圖形化桌面環境。  後續步驟中需要這項資訊，才能使用 X2Go 用戶端進行連線。

1. 在您的本機電腦上安裝 [X2Go 用戶端](https://wiki.x2go.org/doku.php/doc:installation:x2goclient)。

1. 遵循[第一節](how-to-use-remote-desktop-linux-student.md#connect-to-the-student-vm-using-ssh)中的指示，複製您 VM 的 SSH 連線資訊。  您需要此資訊，才能使用 X2Go 用戶端進行連線。

1. 在您取得 SSH 連線資訊後，請開啟 X2Go 用戶端，然後選取 [工作階段] > [新增工作階段]。
   ![X2Go 建立新的工作階段](../media/how-to-use-classroom-lab/x2go-new-session.png)

1. 根據您的 SSH 連線資訊，在 [工作階段喜好設定] 窗格中輸入值。  例如，您的連線資訊會如下所示：

    ```bash
    ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
    ```

    使用此範例，已輸入下列值：

   - **工作階段名稱** - 指定名稱，例如 VM 的名稱。
   - **主機** - VM 的識別碼；例如 **`ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com`** 。
   - **登入** - VM 的使用者名稱；例如**學生**。
   - **SSH 連接埠** - 指派給 VM 的唯一連接埠；例如 **12345**。
   - **工作階段類型** - 選取講師為您的 VM 設定的 Linux 圖形化桌面環境。  您必須向講師取得此資訊。

    最後，按一下 [確定] 以建立工作階段。

    ![X2Go 工作階段喜好設定](../media/how-to-use-classroom-lab/x2go-session-preferences.png)

1.  在右側窗格中按一下您的工作階段。

    ![X2Go 啟動新工作階段](../media/how-to-use-classroom-lab/x2go-start-session.png)

    > [!NOTE] 
    > 如果系統提示您輸入類似的訊息，請選取 [是] 繼續輸入您的密碼：**無法建立主機 '[`00000000-0000-0000-0000-000000000000.eastus2.cloudapp.eastus.cloudapp.azure.com`]:12345' 的真確性。ECDSA 金鑰指紋為 SHA256:00000000000000000000000000000000000000000000。您確定要繼續連線嗎 (是/否)？**

2. 出現提示時，請輸入您的密碼並按一下 [確定]。  您現在會從遠端連線至您 VM 的 GUI 桌面環境。

## <a name="next-steps"></a>後續步驟
若要了解如何為教室實驗室中的 Linux VM 啟用遠端桌面連線功能，請參閱[為 Linux 虛擬機器啟用遠端桌面](how-to-enable-remote-desktop-linux.md)。 

