---
title: 使用 Azure 實驗室服務來設定 Linux 殼層指令碼實驗室 | Microsoft Docs
description: 了解如何設定實驗室來教授 Linux 上的殼層指令碼。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: ba14f2fb5263367014b57741c78d6e509df044b3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85444959"
---
# <a name="set-up-a-lab-to-teach-shell-scripting-on-linux"></a>設定實驗室來教授 Linux 上的殼層指令碼
本文說明如何設定實驗室來教授 Linux 上的殼層指令碼。 指令碼是系統管理中很有用的部分，可讓管理員避免重複的工作。 在此範例案例中，課程涵蓋了傳統的 Bash 指令碼和增強型指令碼。 增強型指令碼是結合了 Bash 命令與 Ruby 的指令碼。 這種方法可讓 Ruby 傳遞資料，以及讓 Bash 命令與殼層互動。 

參與這些指令碼課程的學生可透過 Linux 虛擬機器了解 Linux 的基本概念，同時也能熟悉 Bash 殼層指令碼。 此 Linux 虛擬機器已啟用遠端桌面存取，且已安裝 [gedit](https://help.gnome.org/users/gedit/stable/) 和 [Visual Studio Code](https://code.visualstudio.com/) 文字編輯器。

## <a name="lab-configuration"></a>實驗室組態
若要設定此實驗室，您需要 Azure 訂用帳戶才能開始進行。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。 擁有 Azure 訂用帳戶之後，您就可以在 Azure 實驗室服務中建立新的實驗室帳戶，或使用現有的實驗室帳戶。 請參閱下列教學課程來建立新的實驗室帳戶：[設定實驗室帳戶的教學課程](tutorial-setup-lab-account.md)。

建立實驗室帳戶之後，請在實驗室帳戶中啟用下列設定： 

| 實驗室帳戶設定 | Instructions |
| ----------- | ------------ |  
| Marketplace 映像 | 啟用 Ubuntu Server 18.04 LTS 映像，以便在您的實驗室帳戶中使用。 如需詳細資訊，請參閱[指定實驗室建立者可用的 Marketplace 映像](specify-marketplace-images.md)。 | 

請遵循[本教學課程](tutorial-setup-classroom-lab.md)來建立新的實驗室，並套用下列設定：

| 實驗室設定 | 值/指示 | 
| ------------ | ------------------ |
| 虛擬機器 (VM) 大小 | 小型  |
| VM 映像 | Ubuntu Server 18.04 LTS|
| 啟用遠端桌面連線 | 啟用。 <p>啟用此設定可讓教師和學生使用遠端桌面 (RDP) 連線到其 VM。 如需詳細資訊，請參閱[在 Azure 實驗室服務中為實驗室中的 Linux 虛擬機器啟用遠端桌面](how-to-enable-remote-desktop-linux.md)。 </p>|

## <a name="install-desktop-and-rdp"></a>安裝桌面和 RDP
Ubuntu Server 18.04 LTS 映像預設不會安裝遠端桌面伺服器。 請遵循[在 Azure 中安裝和設定遠端桌面以連線至 Linux VM](../virtual-machines/linux/use-remote-desktop.md) 一文中的指示，安裝範本機器要能透過遠端桌面通訊協定 (RDP) 來連線所需要的套件。

## <a name="install-ruby"></a>安裝 Ruby
Ruby 是一種可與 bash 指令碼結合的開放原始碼動態語言。 本節說明如何使用 `apt-get` 來安裝最新版的 [Ruby](https://www.ruby-lang.org/)。

1. 執行下列命令來安裝更新：

    ```bash
    sudo apt-get update 
    sudo apt-get upgrade 
    ```
2.  安裝 [Ruby](https://www.ruby-lang.org/)。  Ruby 是一種可與 bash 指令碼結合的開放原始碼動態語言。 
    
    ```bash
    sudo apt-get install ruby-full
    ```

## <a name="install-development-tools"></a>安裝開發工具
本節說明如何安裝一些文字編輯器。 Gedit 是 gnome 桌面環境的預設文字編輯器。 其已設計為一般用途的文字編輯器。 Visual Studio Code 是一種內含偵錯和原始檔控制整合支援的文字編輯器。

> [!NOTE]
> 有數種不同的文字編輯器可供使用。 Visual Studio Code 和 gedit 只是其中兩例。

1. 安裝 [gedit](https://help.gnome.org/users/gedit/stable/)。

    ```bash
    sudo apt-get install gedit
    ```
1. 安裝 [Visual Studio Code](https://code.visualstudio.com/)。  您可以使用 Snap Store 來安裝 Visual Studio Code。  如需其他安裝選項，請參閱 [Visual Studio Code 的其他下載](https://code.visualstudio.com/#alt-downloads)。

    ```bash
    sudo snap install vscode --classic 
    ```

    此範本現已更新，並具有要完成實驗室所需的程式設計語言和開發工具。 範本映像現在可以發佈至實驗室。 在 [範本] 頁面上選取 [發佈] 按鈕，就能將範本發佈至實驗室。  

## <a name="cost"></a>成本 
如果您想要估計此實驗室的成本，可以使用下列範例：
 
針對 25 名學生的課程，其中 20 小時是已排定課程的時間，10 小時是家庭作業或指派工作的配額，此實驗室的價格為： 

25 名學生 * (20 + 10) 小時 * 20 個實驗室單位 * 0.01 美元/小時 = 150 美元

如需定價的詳細資訊，請參閱下列文件：[Azure 實驗室服務定價](https://azure.microsoft.com/pricing/details/lab-services/)。

## <a name="conclusion"></a>結論
本文已逐步引導您完成建立指令碼類別實驗室的步驟。 雖然本文著重在說明如何在 Linux 機器上設定 Ruby 指令碼工具，但相同的設定也可用於 Linux 上的其他指令碼類別，例如 Python。

## <a name="next-steps"></a>後續步驟
接下來是設定任何實驗室的一般步驟：

- [新增使用者](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [設定配額](how-to-configure-student-usage.md#set-quotas-for-users)
- [設定排程](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [學生的電子郵件註冊連結](how-to-configure-student-usage.md#send-invitations-to-users)。 





