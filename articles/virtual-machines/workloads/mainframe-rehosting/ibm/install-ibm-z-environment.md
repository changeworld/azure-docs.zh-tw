---
title: 在 Azure 上安裝 IBM zD&T 開發/測試環境 |Microsoft Docs
description: 將 IBM Z 開發和測試環境 (zD&T) 部署在 Azure 虛擬機器上 (VM) 基礎結構即服務 (IaaS) 。
services: virtual-machines-linux
ms.service: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 04/02/2019
tags: ''
keywords: ''
ms.openlocfilehash: 55eb9a0bca3f142c1065f867cebd840cc7958b7e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86499914"
---
# <a name="install-ibm-zdt-devtest-environment-on-azure"></a>在 Azure 上安裝 IBM zD&T 開發/測試環境

若要在 IBM Z 系統上建立大型主機工作負載的開發/測試環境，您可以在 Azure 虛擬機器上部署 IBM Z 開發和測試環境 (zD&T) ， (VM) 基礎結構即服務 (IaaS) 。

有了 zD&T，您可以利用 x86 平臺的節省成本來處理較不重要的開發和測試環境，然後將更新推送回 Z 系統生產環境。 如需詳細資訊，請參閱 [IBM ZD&T 安裝指示](https://www-01.ibm.com/support/docview.wss?uid=swg24044565#INSTALL)。

Azure 和 Azure Stack 支援下列版本：

- zD&T Personal Edition
- zD&T 平行 Sysplex
- zD&T 企業版

所有版本的 zD&T 只能在 x86 Linux 系統上執行，而不是在 Windows Server 上執行。 Red Hat Enterprise Linux (RHEL) 或 Ubuntu/Debian 均支援 Enterprise Edition。 RHEL 和 Debian VM 映射可供 Azure 使用。

本文說明如何在 Azure 上設定 zD&T Enterprise Edition，讓您可以使用 zD&T Enterprise Edition web server 來建立和管理環境。 安裝 zD&T 不會安裝任何環境。 您必須分別建立它們作為安裝套件。 例如，應用程式開發人員控制 (ADCD) 的散發套件是測試環境的磁片區映射。 它們包含在可從 IBM 取得之媒體發佈的 zip 映射中。 瞭解如何 [在 Azure 上設定 ADCD 環境](demo.md)。

如需詳細資訊，請參閱 IBM 知識中心中的 [zD&T 總覽](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zdt.overview.gs.doc/topics/c_product_overview.html) 。

本文說明如何在 Azure 上 (zD&T) Enterprise Edition，設定 Z 開發與測試環境。 然後，您可以使用 zD&T Enterprise Edition web server，在 Azure 上建立及管理以 Z 為基礎的環境。

## <a name="prerequisites"></a>Prerequisites

> [!NOTE]
> IBM 只允許將 zD&T Enterprise Edition 安裝在開發/測試環境中，*而不* 是實際執行環境。

- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- 您需要存取媒體，僅供 IBM 客戶和合作夥伴使用。 如需詳細資訊，請洽詢您的 IBM 代表，或參閱 [zD&T](https://www.ibm.com/us-en/marketplace/z-systems-development-test-environment) 網站上的連絡人資訊。

- [授權伺服器](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html)。 這是存取環境的必要項。 您建立的方式取決於您從 IBM 授權軟體的方式：

     - 以**硬體為基礎的授權伺服器**需要 USB 硬體裝置，其中包含存取軟體所有部分所需的有理數權杖。 您必須從 IBM 取得此。

     - 以**軟體為基礎的授權伺服器**需要您設定集中式伺服器來管理授權金鑰。 這是慣用的方法，需要您設定從管理伺服器中的 IBM 接收的金鑰。

## <a name="create-the-base-image-and-connect"></a>建立基底映射並連接

1. 在 Azure 入口網站中，使用您想要的作業系統設定來 [建立 VM](../../../linux/quick-create-portal.md) 。 本文假設 B4ms VM (有4個 vcpu 和 16 GB 的記憶體) 執行 Ubuntu 16.04。

2. 建立 VM 之後，開啟輸入埠22（適用于 SSH）、21（FTP）和9443（適用于 web 伺服器）。

3. 透過 [**連接]** 按鈕，取得 VM**總覽**分頁上所顯示的 SSH 認證。 選取 [ **ssh** ] 索引標籤，並將 ssh 登入命令複製到剪貼簿。

4. 從您的本機電腦登入 [Bash shell](../../../../cloud-shell/quickstart.md) ，並貼上命令。 它會採用**ssh \<user id\> \@ \<IP Address\> **形式。 當系統提示您輸入認證時，請輸入他們以建立與主目錄的連線。

## <a name="copy-the-installation-file-to-the-server"></a>將安裝檔案複製到伺服器

Web 服務器的安裝檔案是 **ZDT \_ Install \_ EE \_ v 12.0.0.1. tgz**。 它包含在 IBM 提供的媒體中。 您必須將此檔案上傳至您的 Ubuntu VM。

1. 從命令列輸入下列命令，以確保新建立的映射中的所有專案都是最新的：

    ```
    sudo apt-get update
    ```

2. 建立要安裝的目錄：

    ```
    mkdir ZDT
    ```

3. 將檔案從本機電腦複製到 VM：

    ```
    scp ZDT_Install_EE_V12.0.0.1.tgz  your_userid@<IP Address /ZDT>   =>
    ```
    
> [!NOTE]
> 此命令會將安裝檔案複製到主目錄中的 ZDT 目錄，這會根據您的用戶端執行的是 Windows 或 Linux 而有所不同。

## <a name="install-the-enterprise-edition"></a>安裝 Enterprise Edition

1. 移至 ZDT 目錄，並 \_ \_ 使用下列命令解壓縮 ZDT Install EE \_ v 12.0.0.1. tgz file：

    ```
    cd ZDT
    chmod 755 ZDT\_Install\_EE\_V12.0.0.0.tgz
    ```

2. 執行安裝程式：

    ```
    ./ZDT_Install_EE_V12.0.0.0.x86_64
    ```

3. 選取 **1** 以安裝 Enterprise Server。

4. 按下 **enter** 鍵，並仔細閱讀授權合約。 在授權結束時，輸入 **[是]** 以繼續。

5. 當系統提示您變更新建立之使用者的密碼時，請 **ibmsys1**，使用命令 **sudo passwd ibmsys1** ，然後輸入新密碼。

6. 若要確認安裝是否成功輸入

    ```
    dpkg -l | grep zdtapp
    ```

7. 確認輸出包含字串 **zdtapp 12.0.0.0**，指出已成功安裝封裝天然氣

### <a name="starting-enterprise-edition"></a>開始 Enterprise Edition

請記住，當 web 伺服器啟動時，它會在安裝程式期間所建立的 zD&T 使用者識別碼下執行。

1. 若要啟動網頁伺服器，請使用根使用者識別碼來執行下列命令：

    ```
    sudo /opt/ibm/zDT/bin/startServer
    ```

2. 依腳本複製 URL 輸出，如下所示：

    ```
    https://<your IP address or domain name>:9443/ZDTMC/login.htm
    ```

3. 將 URL 貼到網頁瀏覽器中，以開啟 zD&T 安裝的管理元件。

## <a name="next-steps"></a>接下來的步驟

[設定應用程式開發人員在 IBM zD&T v1 中控制散發 (ADCD) ](./demo.md)
