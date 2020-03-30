---
title: 在 IBM zD&T v1 中設置應用程式開發人員控制分發 （ADCD） |微軟文檔
description: 在 Azure 虛擬機器 （VM） 上運行 IBM Z 開發和測試環境 （zD&T） 環境。
services: virtual-machines-linux
ms.service: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
ms.author: edprice
editor: edprice
ms.topic: conceptual
ms.date: 02/22/2019
tags: ''
keywords: ''
ms.openlocfilehash: 66f80c79219090c27da37dfc1d9149df5604961f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "68841383"
---
# <a name="set-up-an-application-developers-controlled-distribution-adcd-in-ibm-zdt-v1"></a>在 IBM zD&T v1 中設置應用程式開發人員控制分發 （ADCD）

您可以在 Azure 虛擬機器 （VM） 上運行 IBM Z 開發和測試環境 （zD&T）。 此環境類比 IBM Z 系列體系結構。 它可以承載各種 Z 系列作業系統或安裝（也稱為 Z 實例或包），這些作業系統或安裝可通過稱為 IBM 應用程式開發人員控制的分發 （ADCD） 的自訂捆綁包提供。

本文介紹如何在 Azure 上的 zD&T 環境中設置 ADCD 實例。 ADCd 為在 zD&T 中運行的開發和測試環境創建完整的 Z 系列作業系統實現。

與 zD&T 一樣，ADC 只提供給 IBM 客戶和合作夥伴，僅用於開發和測試目的。 它們不用於生產環境。 許多 IBM 安裝套裝軟體可通過[Passport 優勢](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.guide.adcd.doc/topics/installation_ps.html)或[IBM 合作夥伴世界](https://www.ibm.com/partnerworld/public)下載。

## <a name="prerequisites"></a>Prerequisites

- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- [zD&T 環境][ibm-install-z]以前在 Azure 上設置。 本文假定您使用的是之前創建的同一 Ubuntu 16.04 VM 映射。

- 通過 IBM 合作夥伴世界或護照優勢訪問 ADCD 媒體。

- [許可伺服器](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html)。 這是運行 IBM zD&T 所必需的。 創建軟體的方式取決於您從 IBM 許可軟體的方式：

  - **基於硬體的許可伺服器**需要一個 USB 硬體設備，該設備包含訪問軟體所有部分所需的 Rational 權杖。 您必須從 IBM 獲取此資料。

  - **基於軟體的許可伺服器**要求您設置一個集中式伺服器來管理許可金鑰。 此方法是首選，要求您在管理伺服器中設置從 IBM 接收的金鑰。

## <a name="download-the-installation-packages-from-passport-advantage"></a>從 Passport 優勢下載安裝包

需要訪問 ADCD 介質。 以下步驟假定您是 IBM 客戶，可以使用 Passport 優勢。 IBM 合作夥伴可以使用[IBM 合作夥伴世界](https://www.ibm.com/partnerworld/public)。

> [!NOTE]
> 本文假定 Windows PC 用於訪問 Azure 門戶和下載 IBM 媒體。 如果您使用的是 Mac 或 Ubuntu 桌面，則獲取 IBM 媒體的命令和過程可能略有不同。

1. 登錄[護照優勢](https://www.ibm.com/software/howtobuy/passportadvantage/paocustomer)。

2. 選擇**軟體下載****和媒體訪問**。

3. 選擇 **"計畫"產品/服務及協定編號**，然後按一下"**繼續**"。

4. 輸入零件說明或零件號，然後按一下 **"查找器**"。

5. 可選，按一下按字母順序清單按名稱顯示和查看產品。

6. 選擇 **"作業系統"欄位中****的所有作業系統**，在 **"語言"欄位中**選擇 **"所有語言**"。 然後，按一下"**轉到**"。

7. 按一下 **"選擇單個檔**"展開清單並顯示要下載的各個媒體。

8. 驗證要下載的包，選擇 **"下載**"，然後將檔下載到所需的目錄。

## <a name="upload-the-adcd-packages"></a>上傳 ADCD 包

現在，您已經擁有了包，您必須將它們上載到 Azure 上的 VM。

1. 在 Azure 門戶中，使用創建的 Ubuntu VM 啟動**ssh**會話。 轉到 VM，選擇 **"概述"** 邊欄選項卡，然後選擇"**連接**"。

2. 選擇**SSH**選項卡，然後將 ssh 命令複製到剪貼簿。

3. 使用憑據和選擇的[SSH 用戶端](/azure/virtual-machines/linux/use-remote-desktop)登錄到 VM。 本演示使用 Windows 10 的 Linux 擴展，該擴展將 bash shell 添加到 Windows 命令提示符。 普蒂也工作得很好。

4. 登錄後，創建一個目錄以上載 IBM 包。 請記住，Linux 對大小寫很敏感。 例如，本演示假定包上載到：

        /home/MyUserID/ZDT/adcd/nov2017/volumes

5. 使用 SSH 用戶端（如[WinSCP）](https://winscp.net/eng/index.php)上載檔。 由於 SCP 是 SSH 的一部分，它使用埠 22，這是 SSH 使用的埠。 如果本地電腦不是 Windows，則可以在 SSH 會話中鍵入[scp 命令](http://man7.org/linux/man-pages/man1/scp.1.html)。

6. 啟動上載到您創建的 Azure VM 目錄，該目錄將成為 zD&T 的圖像存儲。

    > [!NOTE]
    > 確保**ADCDTOOLS。XML**包含在上載到**家庭/MyUserID/ZDT/adcd/nov2017**目錄中。 稍後您將會用到此資訊。

7. 等待檔上載，這可能需要一些時間，具體取決於您與 Azure 的連接。

8. 上載完成後，導航到卷目錄並解壓縮所有**gz**卷：

    ```
        gunzip \*.gz
    ```
    
![顯示解壓縮 gz 卷的檔資源管理器](media/01-gunzip.png)

## <a name="configure-the-image-storage"></a>配置映射存儲

下一步是將 zD&T 配置為使用上載的包。 zD&T 中的圖像預存程序允許您裝載和使用圖像。 它可以使用 SSH 或 FTP。

1. 啟動**zDTServer**。 為此，您必須位於根級別。 按順序輸入以下兩個命令：
    ```
        sudo su -
        /opt/ibm/zDT/bin/startServer
    ```
2. 請注意命令的 URL 輸出，並使用此 URL 訪問 Web 服務器。 它類似于：
     > HTTPs：//（您的 VM 名稱或 IP 位址）：9443/ZDTMC/index.html
     >
     > 請記住，您的 Web 訪問使用埠 9443。 使用此選項可登錄到 Web 服務器。 ZD&T 的使用者 ID 為**zdtadmin，** 密碼為**密碼**。

    ![IBM zD&T 企業版歡迎畫面](media/02-welcome.png)

3. 在 **"快速入門"** 頁上，在 **"配置**"下，選擇**映射存儲**。

     ![IBM zD&T 企業版快速入門螢幕](media/03-quickstart.png)

4. 在 **"配置映射存儲**"頁上，選擇**SSH 檔案傳輸協定**。

5. 對於**主機名稱**，鍵入**本地主機**並輸入上載圖像的目錄路徑。 例如，/home/MyUserID/ZDT/adcd/nov2017/卷。

6. 輸入 VM**的使用者 ID**和**密碼**。 請勿使用 ZD&T 使用者 ID 和密碼。

7. 測試連接以確保您具有存取權限，然後選擇 **"保存"** 以保存配置。

## <a name="configure-the-target-environments"></a>配置目標環境

下一步是配置 zD&T 目標環境。 此類比託管環境是映射運行的位置。

1. 在 **"快速入門"** 頁上，在 **"配置**"下，選擇 **"目標環境**"。

2. 在 **"配置目標環境**"頁上，選擇 **"添加目標**"。

3. 選擇**Linux**。 IBM 支援兩種類型的環境，Linux 和雲（OpenStack），但此演示在 Linux 上運行。

4. 在 **"添加目標環境**"頁上，對於**主機名稱**，請輸入**本地主機**。 將**SSH 埠**設置為**22**。

5. 在 **"目標環境"標籤**框中，輸入 **"MyCICS"** 等標籤。

     ![添加目標環境螢幕](media/04-add-target.png)

## <a name="configure-adcd-and-deploy"></a>配置 ADCD 並部署

完成前面的配置步驟後，必須配置 zD&T 才能使用包和目標環境。 同樣，在 zd&T 中使用映射預存程序，這允許您裝載和使用圖像。 它可以使用 SSH 或 FTP。

1. 在 **"快速入門"** 頁上，在 **"配置**"下，選擇**ADCD**。 將顯示一組說明，告訴您需要在裝載 ADCD 包之前完成的步驟。 這解釋了為什麼我們以前面的方式命名目標目錄。

2. 假設所有圖像都上載到正確的目錄，請按一下右下角顯示的**ADCD**連結的 IMAGE（如下圖所示的步驟 7 所示）。

     ![IBM zD&T 企業版 - 配置 ADCD 螢幕](media/05-adcd.png)

## <a name="create-the-image"></a>建立映像

完成上一個配置步驟後，將顯示 **"使用 ADCD 元件創建映射"** 頁。

1. 選擇卷（本例中為 2017 年 11 月）以顯示該卷中的不同包。

2. 在本演示中，選擇**客戶資訊控制系統 （CICS） - 5.3**。

3. 在 **"圖像名稱"** 框中，鍵入圖像的名稱，如**MyCICS 圖像**。

4. 選擇右下角的 **"創建圖像**"按鈕。

     ![IBM zD&T 企業版 - 使用 ADCD 元件螢幕創建圖像](media/06-adcd.png)

5. 在顯示的視窗中，告訴您映射已成功部署，請選擇 **"部署映射**"。

6. 在 **"將圖像部署到目標環境**"頁上，選擇您在上一頁 **（MyCICS 映射**）上創建的圖像和之前創建的目標環境 **（MyCICS**）。

7. 在下一個螢幕上，為 VM 提供憑據（即，不是 ztadmin 憑據）。

8. 在"屬性"窗格中，輸入**中央處理器 （CP）** 的數量、**系統記憶體量 （GB）** 和正在運行的映射**的部署目錄**。 由於這是一個演示，保持它小。

9. 確保選中該框，以便**部署後自動將 IPL 命令發出到 z/OS。**

     ![屬性螢幕](media/07-properties.png)

10. 選擇 **"完成**"。

11. 選擇 **"將映射**從 **"部署到目標環境**"頁。

映射現在可以部署，並且可以由 3270 終端模擬器安裝。

> [!NOTE]
> 如果收到錯誤說磁碟空間不足，請注意該區域需要 151 Gb。

恭喜！ 現在，您正在 Azure 上運行 IBM 大型機環境。

## <a name="learn-more"></a>深入了解

- [大型機遷移：神話和事實](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [Azure 上的 IBM DB2 pureScale](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure)
- [疑難排解](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
- [將大型機與 Azure 遷移解密](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-on-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
