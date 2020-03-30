---
title: 使用 Azure 實驗室服務設置 Linux 外殼腳本實驗室 |微軟文檔
description: 瞭解如何設置實驗室在 Linux 上教授 shell 腳本。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77443545"
---
# <a name="set-up-a-lab-to-teach-shell-scripting-on-linux"></a>設置一個實驗室來教授 Linux 上的 shell 腳本
本文介紹如何設置一個實驗室來在 Linux 上教授 shell 腳本。 指令碼是系統管理中很有用的部分，可讓管理員避免重複的工作。 在此範例案例中，課程涵蓋了傳統的 Bash 指令碼和增強型指令碼。 增強型指令碼是結合了 Bash 命令與 Ruby 的指令碼。 這種方法可讓 Ruby 傳遞資料，以及讓 Bash 命令與殼層互動。 

參與這些指令碼課程的學生可透過 Linux 虛擬機器了解 Linux 的基本概念，同時也能熟悉 Bash 殼層指令碼。 此 Linux 虛擬機器已啟用遠端桌面存取，且已安裝 [gedit](https://help.gnome.org/users/gedit/stable/) 和 [Visual Studio Code](https://code.visualstudio.com/) 文字編輯器。

## <a name="lab-configuration"></a>實驗室組態
要設置此實驗，需要 Azure 訂閱才能開始。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。 獲得 Azure 訂閱後，可以在 Azure 實驗室服務中創建新的實驗室帳戶，也可以使用現有的實驗室帳戶。 有關創建新實驗室帳戶，請參閱以下教程：[設置實驗室帳戶的教程](tutorial-setup-lab-account.md)。

創建實驗室帳戶後，在實驗室帳戶中啟用以下設置： 

| 實驗室帳戶設置 | Instructions |
| ----------- | ------------ |  
| 市場映射 | 啟用[Ubuntu 伺服器 18.04 LTS](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.0001-com-ubuntu-server-bionic)映射，以便在實驗室帳戶中使用。 有關詳細資訊，請參閱[指定可供實驗室建立者使用的市場映射](specify-marketplace-images.md)。 | 

請按照[本教程](tutorial-setup-classroom-lab.md)創建新實驗室並應用以下設置：

| 實驗室設置 | 值/說明 | 
| ------------ | ------------------ |
| 虛擬機器 （VM） 大小 | 小型  |
| VM 映射 | [烏本圖伺服器 18.04 LTS](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.0001-com-ubuntu-server-bionic) |
| 啟用遠端桌面連線 | 啟用。 <p>啟用此設置將允許教師和學生使用遠端桌面 （RDP） 連接到其 VM。 有關詳細資訊，請參閱在[Azure 實驗室服務 中的實驗室中為 Linux 虛擬機器啟用遠端桌面](how-to-enable-remote-desktop-linux.md#connect-to-the-template-vm)。 </p>|


## <a name="install-desktop-and-xrdp"></a>安裝桌面和 xrdp
[預設情況下，Ubuntu 伺服器 18.04 LTS](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.0001-com-ubuntu-server-bionic)映射未安裝遠端桌面伺服器。 按照["安裝"中的說明進行配置遠端桌面以連接到 Azure 文章中的 Linux VM，](../../virtual-machines/linux/use-remote-desktop.md)以安裝範本電腦上通過遠端桌面協定進行連接所需的包。

## <a name="install-ruby"></a>安裝 Ruby
Ruby 是一種開源動態語言，可與 bash 腳本結合使用。 本節演示如何使用來`apt-get`安裝最新版本的[Ruby](https://www.ruby-lang.org/)。

1. 通過運行以下命令安裝更新：

    ```bash
    sudo apt-get update 
    sudo apt-get upgrade 
    ```
2.  安裝[紅寶石](https://www.ruby-lang.org/)。  Ruby 是一種開源動態語言，可與 bash 腳本結合使用。 
    
    ```bash
    sudo apt-get install ruby-full
    ```

## <a name="install-development-tools"></a>安裝開發工具
本節介紹如何安裝幾個文字編輯器。 Gedit 是 gnome 桌面環境的預設文字編輯器。 它設計為通用文字編輯器。 Visual Studio Code 是一個文字編輯器，包括對調試和原始程式碼管理集成的支援。

> [!NOTE]
> 有幾個不同的文字編輯器可用。 視覺工作室代碼和 gedit 只是兩個示例。

1. 安裝[格迪特](https://help.gnome.org/users/gedit/stable/)。

    ```bash
    sudo apt-get install gedit
    ```
1. 安裝[視覺化工作室代碼](https://code.visualstudio.com/)。  可以使用快照存儲安裝視覺化工作室代碼。  有關備用安裝選項，請參閱[視覺化工作室代碼備用下載](https://code.visualstudio.com/#alt-downloads)。

    ```bash
    sudo snap install vscode --classic 
    ```

    範本現已更新，並具有完成實驗所需的程式設計語言和開發工具。 範本映射現在可以發佈到實驗室。 選擇範本頁上的 **"發佈"** 按鈕，將範本發佈到實驗室。  

## <a name="cost"></a>成本 
如果要估計本實驗的成本，可以使用以下示例：
 
對於有 25 名學生的班級，有 20 小時的上課時間和 10 小時的家庭作業或作業配額，實驗室的價格是： 

25 名學生 = （20 + 10） 小時 = 20 個實驗室單位 = 每小時 0.01 USD = 150 USD

有關定價的詳細資訊，請參閱以下文檔[：Azure 實驗室服務定價](https://azure.microsoft.com/pricing/details/lab-services/)。

## <a name="conclusion"></a>結論
本文介紹了為腳本類創建實驗室的步驟。 雖然本文側重于在Linux電腦上設置Ruby腳本工具，但相同的設置可用於其他腳本類，如Linux上的Python。

## <a name="next-steps"></a>後續步驟
設置任何實驗室通常採取以下步驟：

- [新增使用者](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [設置配額](how-to-configure-student-usage.md#set-quotas-for-users)
- [設置計畫](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [電子郵件註冊連結給學生](how-to-configure-student-usage.md#send-invitations-to-users)。 





