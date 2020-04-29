---
title: 使用 Azure 實驗室服務設定 Linux shell 腳本實驗室 |Microsoft Docs
description: 瞭解如何設定實驗室來教授 Linux 上的 shell 腳本。
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
ms.date: 09/30/2019
ms.author: spelluru
ms.openlocfilehash: 100a485588c77f6977001dae984b30ebcb1de557
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77443545"
---
# <a name="set-up-a-lab-to-teach-shell-scripting-on-linux"></a>設定實驗室來教 Linux 上的 shell 腳本
本文說明如何設定實驗室來教您在 Linux 上的 shell 腳本。 指令碼是系統管理中很有用的部分，可讓管理員避免重複的工作。 在此範例案例中，課程涵蓋了傳統的 Bash 指令碼和增強型指令碼。 增強型指令碼是結合了 Bash 命令與 Ruby 的指令碼。 這種方法可讓 Ruby 傳遞資料，以及讓 Bash 命令與殼層互動。 

參與這些指令碼課程的學生可透過 Linux 虛擬機器了解 Linux 的基本概念，同時也能熟悉 Bash 殼層指令碼。 此 Linux 虛擬機器已啟用遠端桌面存取，且已安裝 [gedit](https://help.gnome.org/users/gedit/stable/) 和 [Visual Studio Code](https://code.visualstudio.com/) 文字編輯器。

## <a name="lab-configuration"></a>實驗室組態
若要設定此實驗室，您需要 Azure 訂用帳戶才能開始使用。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。 擁有 Azure 訂用帳戶之後，您可以在 Azure 實驗室服務中建立新的實驗室帳戶，或使用現有的實驗室帳戶。 請參閱下列教學課程，以建立新的實驗室帳戶：[設定實驗室帳戶的教學](tutorial-setup-lab-account.md)課程。

建立實驗室帳戶之後，請啟用實驗室帳戶中的下列設定： 

| 實驗室帳戶設定 | Instructions |
| ----------- | ------------ |  
| Marketplace 映射 | 啟用[Ubuntu Server 18.04 LTS](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.0001-com-ubuntu-server-bionic)映射，以便在您的實驗室帳戶中使用。 如需詳細資訊，請參閱[指定可供實驗室建立者使用的 Marketplace 映射](specify-marketplace-images.md)。 | 

遵循[此教學](tutorial-setup-classroom-lab.md)課程來建立新的實驗室，並套用下列設定：

| 實驗室設定 | 值/指示 | 
| ------------ | ------------------ |
| 虛擬機器（VM）大小 | 小型  |
| VM 映射 | [Ubuntu Server 18.04 LTS](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.0001-com-ubuntu-server-bionic) |
| 啟用遠端桌面連線 | 啟用。 <p>啟用此設定可讓教師和學生使用遠端桌面（RDP）連接到其 Vm。 如需詳細資訊，請參閱[在 Azure 實驗室服務中的實驗室中啟用 Linux 虛擬機器的遠端桌面](how-to-enable-remote-desktop-linux.md#connect-to-the-template-vm)。 </p>|


## <a name="install-desktop-and-xrdp"></a>安裝 desktop 和 xrdp
[Ubuntu server 18.04 LTS](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.0001-com-ubuntu-server-bionic)映射預設不會安裝遠端桌面伺服器。 依照在[Azure 中安裝和設定遠端桌面連線到 LINUX VM 一](../../virtual-machines/linux/use-remote-desktop.md)文中的指示，安裝範本機器上所需的套件，以透過遠端桌面通訊協定進行連線。

## <a name="install-ruby"></a>安裝 Ruby
Ruby 是一種開放原始碼動態語言，可以與 bash 腳本結合。 本節說明如何使用`apt-get`來安裝最新版本的[Ruby](https://www.ruby-lang.org/)。

1. 執行下列命令來安裝更新：

    ```bash
    sudo apt-get update 
    sudo apt-get upgrade 
    ```
2.  安裝[Ruby](https://www.ruby-lang.org/)。  Ruby 是一種開放原始碼動態語言，可以與 bash 腳本結合。 
    
    ```bash
    sudo apt-get install ruby-full
    ```

## <a name="install-development-tools"></a>安裝開發工具
本節說明如何安裝一些文字編輯器。 Gedit 是 gnome 桌面環境的預設文字編輯器。 其設計為一般用途的文字編輯器。 Visual Studio Code 是一種文字編輯器，其中包含對偵錯工具和原始檔控制整合的支援。

> [!NOTE]
> 有數個不同的文字編輯器可供使用。 Visual Studio Code 和 gedit 只是兩個範例。

1. 安裝[gedit](https://help.gnome.org/users/gedit/stable/)。

    ```bash
    sudo apt-get install gedit
    ```
1. 安裝 [Visual Studio Code](https://code.visualstudio.com/)。  您可以使用 [貼齊存放區] 來安裝 Visual Studio 程式碼。  如需其他安裝選項，請參閱[Visual Studio Code 其他下載](https://code.visualstudio.com/#alt-downloads)。

    ```bash
    sudo snap install vscode --classic 
    ```

    此範本現在已更新，並具有完成實驗室所需的程式設計語言和開發工具。 範本映射現在可以發佈至實驗室。 選取 [範本] 頁面上的 [**發佈**] 按鈕，將範本發佈至實驗室。  

## <a name="cost"></a>成本 
如果您想要估計此實驗室的成本，可以使用下列範例：
 
對於25名學生的課程，其中有20小時的排程類別時間，以及10小時的家庭作業或指派配額，實驗室的價格如下： 

25名學生 * （20 + 10）小時 * 20 個實驗室單位 * 0.01 美元/小時 = 150 美元

如需定價的詳細資訊，請參閱下列檔： [Azure 實驗室服務定價](https://azure.microsoft.com/pricing/details/lab-services/)。

## <a name="conclusion"></a>結論
本文逐步引導您建立腳本類別的實驗室。 雖然本文著重于在 Linux 電腦上設定 Ruby 腳本工具，但相同的設定可用於 Linux 上的其他腳本類別，例如 Python。

## <a name="next-steps"></a>後續步驟
下一步是設定任何實驗室的一般步驟：

- [新增使用者](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [設定配額](how-to-configure-student-usage.md#set-quotas-for-users)
- [設定排程](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [學生的電子郵件註冊連結](how-to-configure-student-usage.md#send-invitations-to-users)。 





