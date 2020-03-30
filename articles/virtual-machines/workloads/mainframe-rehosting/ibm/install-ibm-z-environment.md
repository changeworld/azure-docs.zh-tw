---
title: 在 Azure 上安裝 IBM zD&T 開發/測試環境 |微軟文檔
description: 在 Azure 虛擬機器 （VM） 基礎結構上部署 IBM Z 開發和測試環境 （zD&T） 作為服務 （IaaS）。
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
ms.openlocfilehash: 67af4eae03b773fad9cf38964152c1fb9d623bd5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "72025948"
---
# <a name="install-ibm-zdt-devtest-environment-on-azure"></a>在 Azure 上安裝 IBM zD&T 開發/測試環境

要為 IBM Z Systems 上的主機工作負載創建開發/測試環境，可以在 Azure 虛擬機器 （VM） 基礎結構（IaaS） 上部署 IBM Z 開發和測試環境 （zD&T）。

借助 zD&T，您可以利用 x86 平臺的成本節約，為您的不太關鍵的開發和測試環境，然後將更新推回 Z 系統生產環境。 有關詳細資訊，請參閱 IBM [ZD&T 安裝說明](https://www-01.ibm.com/support/docview.wss?uid=swg24044565#INSTALL)。

Azure 和 Azure 堆疊支援以下版本：

- zD&T 個人版
- zD&T 並行系統
- zD&T 企業版

zD&T 的所有版本僅在 x86 Linux 系統上運行，而不是 Windows 伺服器運行。 紅帽企業版 （RHEL） 或 Ubuntu/Debian 都支援企業版。 RHEL 和 Debian VM 映射都可用於 Azure。

本文介紹如何在 Azure 上設置 zD&T 企業版，以便可以使用 zD&T 企業版 Web 服務器創建和管理環境。 安裝 zD&T 不會安裝任何環境。 您必須單獨創建這些安裝包。 例如，應用程式開發人員控制的分發 （ADCD） 是測試環境的卷映射。 它們包含在 IBM 提供的媒體分發上的 zip 圖像中。 瞭解如何在[Azure 上設置 ADCD 環境](demo.md)。

有關詳細資訊，請參閱 IBM 知識中心中的[zD&T 概述](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zdt.overview.gs.doc/topics/c_product_overview.html)。

本文介紹如何在 Azure 上設置 Z 開發和測試環境 （zD&T） 企業版。 然後，可以使用 zD&T 企業版 Web 服務器在 Azure 上創建和管理基於 Z 的環境。

## <a name="prerequisites"></a>Prerequisites

> [!NOTE]
> IBM 允許僅在開發/測試環境中安裝 zD&T 企業版，*而不是*生產環境。

- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- 您需要訪問媒體，媒體僅對 IBM 客戶和合作夥伴可用。 有關詳細資訊，請聯繫您的 IBM 代表或查看[zD&T](https://www.ibm.com/us-en/marketplace/z-systems-development-test-environment)網站上的聯繫資訊。

- [許可伺服器](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html)。 這是訪問環境所必需的。 創建軟體的方式取決於您從 IBM 許可軟體的方式：

     - **基於硬體的許可伺服器**需要一個 USB 硬體設備，該設備包含訪問軟體所有部分所需的 Rational 權杖。 您必須從 IBM 獲取此資料。

     - **基於軟體的許可伺服器**要求您設置一個集中式伺服器來管理許可金鑰。 此方法是首選，要求您在管理伺服器中設置從 IBM 接收的金鑰。

## <a name="create-the-base-image-and-connect"></a>創建基本映射並連接

1. 在 Azure 門戶中，使用所需的作業系統配置[創建 VM。](/azure/virtual-machines/linux/quick-create-portal) 本文假定運行 Ubuntu 16.04 的 B4ms VM（具有 4 個 vCPU 和 16 GB 記憶體）。

2. 創建 VM 後，打開 SSH 的入站埠 22，為 FTP 打開 21 埠，為 Web 服務器打開 9443。

3. 通過 **"連接**"按鈕獲取 VM**概述**邊欄選項卡上顯示的 SSH 憑據。 選擇**SSH**選項卡並將 SSH 登錄命令複製到剪貼簿。

4. 從本地 PC 登錄到[Bash 外殼](/azure/cloud-shell/quickstart)並粘貼該命令。 它將在**ssh\<\>\@\<使用者 ID IP\>位址**的表單中。 當提示您輸入憑據時，請輸入它們以建立與主目錄的連接。

## <a name="copy-the-installation-file-to-the-server"></a>將安裝檔案複製到伺服器

Web 服務器的安裝檔是**ZDT\_\_安裝\_EE V12.0.0.1.tgz**。 它包含在 IBM 提供的介質中。 您必須將此檔上載到 Ubuntu VM。

1. 從命令列中，輸入以下命令以確保新創建的映射中的所有內容都是最新的：

    ```
    sudo apt-get update
    ```

2. 創建要安裝到的目錄：

    ```
    mkdir ZDT
    ```

3. 將檔從本地電腦複製到 VM：

    ```
    scp ZDT_Install_EE_V12.0.0.1.tgz  your_userid@<IP Address /ZDT>   =>
    ```
    
> [!NOTE]
> 此命令將安裝檔案複製到主目錄中的 ZDT 目錄，該目錄因用戶端是否運行 Windows 或 Linux 而異。

## <a name="install-the-enterprise-edition"></a>安裝企業版

1. 轉到 ZDT 目錄並使用以下命令解壓縮 ZDT\_安裝\_EE\_V12.0.0.1.tgz 檔：

    ```
    cd ZDT
    chmod 755 ZDT\_Install\_EE\_V12.0.0.0.tgz
    ```

2. 運行安裝程式：

    ```
    ./ZDT_Install_EE_V12.0.0.0.x86_64
    ```

3. 選擇**1**可安裝企業伺服器。

4. 按**Enter**並仔細閱讀授權合約。 在許可證結束時，輸入 **"是**"以繼續。

5. 當提示更改新創建使用者的密碼**ibmsys1**時，請使用命令**sudo passwd ibmsys1**並輸入新密碼。

6. 驗證安裝是否成功輸入

    ```
    dpkg -l | grep zdtapp
    ```

7. 驗證輸出是否包含字串**zdtapp 12.0.0.0，** 指示已成功安裝封裝氣體

### <a name="starting-enterprise-edition"></a>啟動企業版

請記住，當 Web 服務器啟動時，它在安裝過程中創建的 zD&T 使用者 ID 下運行。

1. 要啟動 Web 服務器，請使用根使用者 ID 運行以下命令：

    ```
    sudo /opt/ibm/zDT/bin/startServer
    ```

2. 複製腳本的 URL 輸出，如下所示：

    ```
    https://<your IP address or domain name>:9443/ZDTMC/login.htm
    ```

3. 將 URL 粘貼到 Web 瀏覽器中，以打開 zD&T 安裝的管理元件。

## <a name="next-steps"></a>後續步驟

[在 IBM zD&T v1 中設置應用程式開發人員控制分發 （ADCD）](./demo.md)
