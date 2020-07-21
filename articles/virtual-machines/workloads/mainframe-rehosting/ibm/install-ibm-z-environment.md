---
title: 在 Azure 上安裝 IBM zD&T 開發/測試環境 |Microsoft Docs
description: 在 Azure 虛擬機器（VM）基礎結構即服務（IaaS）上部署 IBM Z 開發和測試環境（zD&T）。
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
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86499914"
---
# <a name="install-ibm-zdt-devtest-environment-on-azure"></a>在 Azure 上安裝 IBM zD&T 開發/測試環境

若要針對 IBM Z 系統上的大型主機工作負載建立開發/測試環境，您可以在 Azure 虛擬機器（VM）基礎結構即服務（IaaS）上部署 IBM Z 開發和測試環境（zD&T）。

有了 zD&T，您就可以利用 x86 平臺的成本節約，讓您更不重要的開發和測試環境，然後再將更新推送回 Z 系統生產環境。 如需詳細資訊，請參閱[IBM ZD&T 安裝指示](https://www-01.ibm.com/support/docview.wss?uid=swg24044565#INSTALL)。

Azure 和 Azure Stack 支援下列版本：

- zD&T Personal Edition
- zD&T Parallel Sysplex
- zD&T Enterprise Edition

所有版本的 zD&T 只能在 x86 Linux 系統上執行，而不是在 Windows Server 上執行。 Red Hat Enterprise Linux （RHEL）或 Ubuntu/Debian 均支援 Enterprise Edition。 RHEL 和 Debian VM 映射均適用于 Azure。

本文說明如何在 Azure 上設定 zD&T Enterprise Edition，讓您可以使用 zD&T Enterprise Edition web 伺服器來建立和管理環境。 安裝 zD&T 並不會安裝任何環境。 您必須將這些分別建立為安裝套件。 例如，應用程式開發人員控制分佈（ADCD）是測試環境的大量影像。 這些套件包含在可從 IBM 取得之媒體發佈上的 zip 影像中。 瞭解如何[在 Azure 上設定 ADCD 環境](demo.md)。

如需詳細資訊，請參閱 IBM 知識中心的[zD&T 總覽](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zdt.overview.gs.doc/topics/c_product_overview.html)。

本文說明如何在 Azure 上設定 Z 開發和測試環境（zD&T） Enterprise Edition。 接著，您可以使用 zD&T Enterprise Edition web 伺服器，在 Azure 上建立和管理以 Z 為基礎的環境。

## <a name="prerequisites"></a>先決條件

> [!NOTE]
> IBM 只允許在開發/測試環境中安裝 zD&T Enterprise Edition，*而不*是在生產環境中。

- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- 您需要存取媒體，這僅供 IBM 客戶和合作夥伴使用。 如需詳細資訊，請洽詢您的 IBM 代表，或參閱[zD&T](https://www.ibm.com/us-en/marketplace/z-systems-development-test-environment)網站上的連絡人資訊。

- [授權伺服器](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html)。 這是存取環境的必要參數。 建立它的方式取決於您從 IBM 授權軟體的方式：

     - 以**硬體為基礎的授權伺服器**需要 USB 硬體裝置，其中包含存取軟體所有部分所需的有理數權杖。 您必須從 IBM 取得此。

     - 以**軟體為基礎的授權伺服器**要求您設定集中式伺服器，以管理授權金鑰。 這是慣用的方法，而且需要您設定在管理伺服器中從 IBM 收到的金鑰。

## <a name="create-the-base-image-and-connect"></a>建立基底映射並聯機

1. 在 Azure 入口網站中，使用您想要的作業系統設定來[建立 VM](../../../linux/quick-create-portal.md) 。 本文假設有一個執行 Ubuntu 16.04 的 B4ms VM （具有4個 vcpu 和 16 GB 的記憶體）。

2. 建立 VM 之後，針對 [SSH] 開啟 [輸入埠 22]、針對 [FTP] 開啟 [21]，並針對 web 伺服器開啟 [9443]。

3. 透過 [連線 **]** 按鈕取得 VM [**總覽**] 分頁上顯示的 SSH 認證。 選取 [ **ssh** ] 索引標籤，並將 [ssh 登入] 命令複製到剪貼簿。

4. 從您的本機電腦登入[Bash shell](../../../../cloud-shell/quickstart.md) ，並貼上命令。 它會以**ssh \<user id\> \@ \<IP Address\> **形式呈現。 當系統提示您提供認證時，請輸入它們以建立與您的主目錄的連接。

## <a name="copy-the-installation-file-to-the-server"></a>將安裝檔案複製到伺服器

Web 服務器的安裝檔案是**ZDT \_ Install \_ EE \_ v 12.0.0.1. tgz**。 它包含在 IBM 所提供的媒體中。 您必須將此檔案上傳到您的 Ubuntu VM。

1. 從命令列輸入下列命令，以確保新建立的映射中所有專案都是最新的：

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
> 此命令會將安裝檔案複製到主目錄中的 ZDT 目錄，這會根據您的用戶端是執行 Windows 或 Linux 而有所不同。

## <a name="install-the-enterprise-edition"></a>安裝 Enterprise Edition

1. 移至 ZDT 目錄，並 \_ \_ 使用下列命令將 ZDT Install EE \_ 12.0.0.1. tgz 檔案解壓縮：

    ```
    cd ZDT
    chmod 755 ZDT\_Install\_EE\_V12.0.0.0.tgz
    ```

2. 執行安裝程式：

    ```
    ./ZDT_Install_EE_V12.0.0.0.x86_64
    ```

3. 選取 [ **1** ] 以安裝 Enterprise Server。

4. 按下**enter**鍵，並仔細閱讀授權合約。 在授權結束時，輸入 **[是]** 繼續進行。

5. 當系統提示您變更新建立之使用者的密碼時，請**ibmsys1**，使用命令**sudo passwd ibmsys1**並輸入新的密碼。

6. 若要確認安裝是否成功，請輸入

    ```
    dpkg -l | grep zdtapp
    ```

7. 確認輸出包含字串**zdtapp 12.0.0.0**，表示已成功安裝套件天然氣

### <a name="starting-enterprise-edition"></a>正在啟動 Enterprise Edition

請記住，當 web 伺服器啟動時，它會在安裝過程中建立的 zD&T 使用者識別碼之下執行。

1. 若要啟動 web 伺服器，請使用根使用者識別碼來執行下列命令：

    ```
    sudo /opt/ibm/zDT/bin/startServer
    ```

2. 複製腳本輸出的 URL，如下所示：

    ```
    https://<your IP address or domain name>:9443/ZDTMC/login.htm
    ```

3. 將 URL 貼到網頁瀏覽器中，以開啟 zD&T 安裝的管理元件。

## <a name="next-steps"></a>後續步驟

[在 IBM zD&T v1 中設定由應用程式開發人員控制的散發（ADCD）](./demo.md)
