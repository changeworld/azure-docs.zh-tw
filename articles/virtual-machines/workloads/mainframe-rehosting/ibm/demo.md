---
title: 在 IBM zD&T v1 中設定應用程式開發人員控制散發（ADCD） |Microsoft Docs
description: 在 Azure 虛擬機器（Vm）上執行 IBM Z 開發和測試環境（zD&T）環境。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "68841383"
---
# <a name="set-up-an-application-developers-controlled-distribution-adcd-in-ibm-zdt-v1"></a>在 IBM zD&T v1 中設定由應用程式開發人員控制的散發（ADCD）

您可以在 Azure 虛擬機器（Vm）上執行 IBM Z 開發和測試環境（zD&T）環境。 此環境會模擬 IBM Z 系列架構。 它可以裝載各種 Z 系列作業系統或安裝（也稱為 Z 實例或套件），這些是透過稱為 IBM 應用程式開發人員控制散發（ADCDs）的自訂群組合來提供。

本文說明如何在 Azure 上的 zD&T 環境中設定 ADCD 實例。 ADCDs 針對在 zD&T 中執行的開發和測試環境，建立完整的 Z 系列作業系統實現。

如同 zD&T，ADCDs 僅供 IBM 客戶和合作夥伴使用，而且僅供開發和測試之用。 它們不會用於生產環境。 許多 IBM 安裝套件都可透過[Passport 優勢](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.guide.adcd.doc/topics/installation_ps.html)或[IBM PartnerWorld](https://www.ibm.com/partnerworld/public)下載。

## <a name="prerequisites"></a>Prerequisites

- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- 先前已在 Azure 上設定的[zD&T 環境][ibm-install-z]。 本文假設您使用的是稍早建立的相同 Ubuntu 16.04 VM 映射。

- 透過 IBM PartnerWorld 或 Passport 的優點存取 ADCD 媒體。

- [授權伺服器](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html)。 這是執行 IBM zD&T 的必要參數。 建立它的方式取決於您從 IBM 授權軟體的方式：

  - 以**硬體為基礎的授權伺服器**需要 USB 硬體裝置，其中包含存取軟體所有部分所需的有理數權杖。 您必須從 IBM 取得此。

  - 以**軟體為基礎的授權伺服器**要求您設定集中式伺服器，以管理授權金鑰。 這是慣用的方法，而且需要您設定在管理伺服器中從 IBM 收到的金鑰。

## <a name="download-the-installation-packages-from-passport-advantage"></a>從 Passport 優點下載安裝套件

需要 ADCD 媒體的存取權。 下列步驟假設您是 IBM 客戶，而且可以使用 Passport 優勢。 IBM 合作夥伴可以使用[Ibm PartnerWorld](https://www.ibm.com/partnerworld/public)。

> [!NOTE]
> 本文假設您使用 Windows 電腦來存取 Azure 入口網站並下載 IBM 媒體。 如果您使用的是 Mac 或 Ubuntu desktop，取得 IBM 媒體的命令和程式可能會稍有不同。

1. 登入[Passport 的優勢](https://www.ibm.com/software/howtobuy/passportadvantage/paocustomer)。

2. 選取 [**軟體下載**和**媒體存取**]。

3. 選取 [**方案供應專案和合約編號**]，然後按一下 [**繼續**]。

4. 輸入 [元件描述] 或 [元件編號]，然後按一下 [搜尋**工具**]。

5. （選擇性）按一下 [字母順序] 清單，以依名稱顯示和查看產品。

6. 選取 [**作業系統] 欄位**中的**所有作業系統**，以及 [**語言] 欄位**中的**所有語言**。 然後按一下 [**移至**]。

7. 按一下 [**選取個別**檔案] 以展開清單，並顯示要下載的個別媒體。

8. 確認您想要下載的套件，選取 [**下載**]，然後將檔案下載至您要的目錄。

## <a name="upload-the-adcd-packages"></a>上傳 ADCD 套件

現在您已有套件，您必須將其上傳至 Azure 上的 VM。

1. 在 Azure 入口網站中，起始與您所建立之 Ubuntu VM 的**ssh**會話。 移至您的 VM，選取 [**總覽**] 分頁，然後選取 [連線 **]**。

2. 選取 [ **ssh** ] 索引標籤，然後將 ssh 命令複製到剪貼簿。

3. 使用您的認證和所選擇的[SSH 用戶端](/azure/virtual-machines/linux/use-remote-desktop)登入您的 VM。 本示範使用適用于 Windows 10 的 Linux 擴充功能，它會將 bash shell 新增至 Windows 命令提示字元。 PuTTY 也同樣可行。

4. 登入時，建立目錄來上傳 IBM 套件。 請記住，Linux 會區分大小寫。 例如，此示範會假設封裝已上傳至：

        /home/MyUserID/ZDT/adcd/nov2017/volumes

5. 使用 SSH 用戶端（例如[WinSCP](https://winscp.net/eng/index.php)）上傳檔案。 由於 SCP 是 SSH 的一部分，因此會使用 SSH 使用的埠22。 如果您的本機電腦不是 Windows，您可以在 SSH 會話中輸入[scp 命令](http://man7.org/linux/man-pages/man1/scp.1.html)。

6. 起始上傳至您建立的 Azure VM 目錄，這會成為 zD&T 的映射儲存體。

    > [!NOTE]
    > 請確定該**ADCDTOOLS。XML**包含在上傳至**Home/MYUSERID/ZDT/adcd/nov2017**目錄中。 稍後您將會用到此資訊。

7. 等候檔案上傳，這可能需要一些時間，視您的 Azure 連線而定。

8. 當上傳完成時，流覽至 [磁片區] 目錄，並將所有**gz**的磁片區解壓縮：

    ```
        gunzip \*.gz
    ```
    
![檔案瀏覽器顯示解壓縮的 gz 磁片區](media/01-gunzip.png)

## <a name="configure-the-image-storage"></a>設定映射儲存體

下一個步驟是將 zD&T 設定為使用上傳的套件。 ZD&T 內的映射儲存進程可讓您掛接和使用映射。 它可以使用 SSH 或 FTP。

1. 啟動**zDTServer**。 若要這樣做，您必須在根層級。 依序輸入下列兩個命令：
    ```
        sudo su -
        /opt/ibm/zDT/bin/startServer
    ```
2. 請記下命令所輸出的 URL，並使用此 URL 來存取 web 伺服器。 看起來像這樣：
     > HTTPs：//（您的 VM 名稱或 IP 位址）： 9443/ZDTMC/index .html
     >
     > 請記住，您的 web 存取會使用埠9443。 使用此來登入 web 伺服器。 ZD&T 的使用者識別碼是**zdtadmin** ，密碼則是**password**。

    ![IBM zD&T Enterprise Edition 歡迎畫面](media/02-welcome.png)

3. 在 [**快速入門**] 頁面的 [**設定**] 底下，選取 [**映射儲存體**]。

     ![IBM zD&T Enterprise Edition 快速入門畫面](media/03-quickstart.png)

4. 在 [**設定映射存放裝置**] 頁面上，選取 [ **SSH 檔案傳輸通訊協定**]。

5. 在 [**主機名稱**] 中輸入**Localhost** ，並輸入您上傳映射的目錄路徑。 例如，/home/MyUserID/ZDT/adcd/nov2017/volumes。

6. 輸入 VM 的 [**使用者識別碼**] 和 [**密碼**]。 請勿使用 ZD&T 使用者識別碼和密碼。

7. 測試連線以確定您擁有存取權，然後選取 [**儲存**] 以儲存設定。

## <a name="configure-the-target-environments"></a>設定目標環境

下一個步驟是設定 zD&T 目標環境。 此模擬託管環境是您的映射執行位置。

1. 在 [**快速入門**] 頁面的 [**設定**] 底下，選取 [**目標環境**]。

2. 在 [**設定目標環境**] 頁面上，選取 [**新增目標**]。

3. 選取 [ **Linux**]。 IBM 支援兩種類型的環境，Linux 和雲端（OpenStack），但此示範會在 Linux 上執行。

4. 在 [**新增目標環境**] 頁面上，針對 [**主機名稱**] 輸入**localhost**。 將 [ **SSH 埠**] 設定為**22**。

5. 在 [**目標環境標籤**] 方塊中，輸入標籤，例如**MyCICS。**

     ![[新增目標環境] 畫面](media/04-add-target.png)

## <a name="configure-adcd-and-deploy"></a>設定 ADCD 和部署

完成先前的設定步驟之後，您必須將 zD&T 設定為使用套件和目標環境。 同樣地，您可以使用 zD&T 中的映射儲存體程式，這可讓您掛接和使用映射。 它可以使用 SSH 或 FTP。

1. 在 [**快速入門**] 頁面的 [**設定**] 底下，選取 [ **ADCD**]。 隨即會出現一組指示，告訴您必須先完成才能裝載 ADCD 套件的步驟。 這解釋了為什麼我們以先前的方式命名目標目錄。

2. 假設所有影像都已上傳至正確的目錄，請按一下右下角顯示的**ADCD 連結影像**（如下列螢幕擷取畫面中的步驟7所示）。

     ![IBM zD&T Enterprise Edition-設定 ADCD 畫面](media/05-adcd.png)

## <a name="create-the-image"></a>建立映像

當先前的設定步驟完成時，會出現 [**使用 ADCD 元件建立映射**] 頁面。

1. 選取磁片區（在此案例中為2017年11月），以顯示該磁片區中的不同套件。

2. 針對此示範，請選取 **[客戶資訊控制系統（CICS）-5.3**]。

3. 在 [**映射名稱**] 方塊中，輸入影像的名稱，例如**MyCICS 影像**。

4. 選取右下方的 [**建立映射**] 按鈕。

     ![IBM zD&T Enterprise Edition-使用 ADCD 元件來建立映射畫面](media/06-adcd.png)

5. 在出現的視窗中，告訴您已成功部署映射，請選擇 [**部署映射**]。

6. 在 [將**映射部署到目標環境**] 頁面上，選取您在前一頁（**MyCICS 影像**）上建立的映射，以及稍早建立的目標環境（**MyCICS**）。

7. 在下一個畫面上，提供您的 VM 認證（也就是不是 ztadmin 認證）。

8. 在 [屬性] 窗格中，輸入**中央處理器（CPs）** 的數目、**系統記憶體的數量（GB）**，以及執行映射的**部署目錄**。 因為這是示範，請將它保持在最少。

9. 請確定已選取此方塊，以**在部署後自動向 z/OS 發出 IPL 命令**。

     ![屬性畫面](media/07-properties.png)

10. 選取 [**完成**]。

11. 從 [將**映射部署到目標環境**] 頁面選取 [**部署映射**]。

您的映射現在可以部署，並準備好由3270終端機模擬器掛接。

> [!NOTE]
> 如果您收到錯誤，指出您沒有足夠的磁碟空間，請注意該區域需要 151 Gb。

恭喜！ 您現在正在 Azure 上執行 IBM 大型主機環境。

## <a name="learn-more"></a>深入了解

- [大型主機遷移：誤解和事實](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [Azure 上的 IBM DB2 pureScale](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure)
- [疑難排解](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
- [揭密大型主機到 Azure 的遷移](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-on-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
