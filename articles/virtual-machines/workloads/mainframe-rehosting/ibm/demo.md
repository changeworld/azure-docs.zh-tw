---
title: 設定應用程式開發人員在 IBM zD&T v1 中控制散發 (ADCD) |Microsoft Docs
description: 在 Azure 虛擬機器上執行 IBM Z 開發和測試環境 (zD&T) 的環境)  (Vm。
services: virtual-machines-linux
ms.service: virtual-machines-linux
ms.subservice: workloads
documentationcenter: ''
author: njray
manager: edprice
ms.author: edprice
editor: edprice
ms.topic: conceptual
ms.date: 02/22/2019
tags: ''
keywords: ''
ms.openlocfilehash: dcd354b906b4d6c92d8b3186fc8e09c94a31ca55
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968395"
---
# <a name="set-up-an-application-developers-controlled-distribution-adcd-in-ibm-zdt-v1"></a>設定應用程式開發人員在 IBM zD&T v1 中控制散發 (ADCD) 

您可以在 Azure 虛擬機器上執行 IBM Z 開發和測試環境 (zD&T) 環境)  (Vm。 此環境會模擬 IBM Z 系列架構。 它可以裝載各種 Z 系列作業系統或安裝 (也稱為 Z 實例或套件) ，這些套件可透過稱為 IBM 應用程式開發人員控制散發 (ADCDs) 的自訂套件提供取得。

本文說明如何在 Azure 上的 zD&T 環境中設定 ADCD 實例。 ADCDs 為在 zD&T 中執行的開發和測試環境，建立完整的 Z 系列作業系統開發。

如同 zD&T，ADCDs 只適用于 IBM 客戶和合作夥伴，而且僅供開發和測試之用。 它們不會用於生產環境。 有許多 IBM 安裝套件可透過 [Passport 優點](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.guide.adcd.doc/topics/installation_ps.html) 或 [IBM PartnerWorld](https://www.ibm.com/partnerworld/public)下載。

## <a name="prerequisites"></a>Prerequisites

- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- 先前在 Azure 上設定的 [zD&T 環境][ibm-install-z] 。 本文假設您使用稍早建立的相同 Ubuntu 16.04 VM 映射。

- 透過 IBM PartnerWorld 或 Passport 優勢存取 ADCD 媒體。

- [授權伺服器](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html)。 這是執行 IBM zD&T 的必要項。 您建立的方式取決於您從 IBM 授權軟體的方式：

  - 以 **硬體為基礎的授權伺服器** 需要 USB 硬體裝置，其中包含存取軟體所有部分所需的有理數權杖。 您必須從 IBM 取得此。

  - 以 **軟體為基礎的授權伺服器** 需要您設定集中式伺服器來管理授權金鑰。 這是慣用的方法，需要您設定從管理伺服器中的 IBM 接收的金鑰。

## <a name="download-the-installation-packages-from-passport-advantage"></a>從 Passport 優點下載安裝套件

需要 ADCD 媒體的存取權。 下列步驟假設您是 IBM 客戶，而且可以使用 Passport 優勢。 IBM 合作夥伴可以使用 [Ibm PartnerWorld](https://www.ibm.com/partnerworld/public)。

> [!NOTE]
> 本文假設您使用 Windows 電腦來存取 Azure 入口網站以及下載 IBM 媒體。 如果您使用的是 Mac 或 Ubuntu desktop，取得 IBM 媒體的命令和流程可能會稍有不同。

1. 登入 [Passport 優勢](https://www.ibm.com/software/howtobuy/passportadvantage/paocustomer)。

2. 選取 [ **軟體下載** 和 **媒體存取**]。

3. 選取 **方案供應專案和合約編號**，然後按一下 [ **繼續**]。

4. 輸入部分描述或元件編號，然後按一下 [ **Finder**]。

5. （選擇性）按一下 [字母順序] 清單，依名稱顯示和查看產品。

6. 在 [**作業系統] 欄位** 中選取 [**所有作業系統**]，然後在 [**語言] 欄位** 中選取 [**所有語言**]。 然後，按一下 [ **移至**]。

7. 按一下 [ **選取個別** 檔案]，展開清單並顯示要下載的個別媒體。

8. 確認您要下載的套件 () ，選取 [ **下載**]，然後將檔案下載到您想要的目錄。

## <a name="upload-the-adcd-packages"></a>將 ADCD 套件上傳 (s) 

既然您已將套件 () ，您必須將其上傳至 Azure 上的 VM。

1. 在 Azure 入口網站中，使用您建立的 Ubuntu VM 起始 **ssh** 會話。 移至您的 VM，選取 **總覽** 分頁，然後選取 **[連線]**。

2. 選取 [ **ssh** ] 索引標籤，然後將 ssh 命令複製到剪貼簿。

3. 使用您所選擇的認證和 [SSH 用戶端](../../../linux/use-remote-desktop.md) 來登入您的 VM。 本示範使用適用于 Windows 10 的 Linux 擴充功能，這會將 bash shell 新增至 Windows 命令提示字元。 PuTTY 也一樣適用。

4. 登入時，建立要上傳 IBM 套件的目錄。 請記住，Linux 會區分大小寫。 例如，此示範假設套件已上傳至：

    `/home/MyUserID/ZDT/adcd/nov2017/volumes`

5. 使用 SSH 用戶端（例如[WinSCP](https://winscp.net/eng/index.php)）上傳檔案。 由於 SCP 是 SSH 的一部分，因此它會使用埠22，也就是 SSH 使用的埠22。 如果您的本機電腦不是 Windows，您可以在 SSH 會話中輸入 [scp 命令](http://man7.org/linux/man-pages/man1/scp.1.html) 。

6. 起始上傳至您所建立的 Azure VM 目錄，這會成為 zD&T 的映射儲存體。

    > [!NOTE]
    > 請確定上傳至 **home/MyUserID/ZDT/adcd/nov2017** 目錄中包含 **ADCDTOOLS.XML** 。 稍後您將會用到此資訊。

7. 等候檔案上傳，這可能需要一些時間，視您連線到 Azure 而定。

8. 上傳完成時，請流覽至磁片區目錄，並解壓縮所有 **gz** 磁片區：

    ```console
    gunzip \*.gz
    ```
    
![顯示解壓縮 gz 磁片區的檔案瀏覽器](media/01-gunzip.png)

## <a name="configure-the-image-storage"></a>設定映射儲存體

下一步是設定 zD&T 使用上傳的套件 (s) 。 ZD&T 中的映射儲存體進程可讓您掛接和使用映射。 它可以使用 SSH 或 FTP。

1. 啟動 **zDTServer**。 若要這樣做，您必須在根層級上。 依序輸入下列兩個命令：
    ```console
    sudo su -
    /opt/ibm/zDT/bin/startServer
    ```
2. 請記下命令的 URL 輸出，並使用此 URL 來存取 web 伺服器。 它看起來像這樣：
     > HTTPs:// (您的 VM 名稱或 IP 位址) ： 9443/ZDTMC/index.html
     >
     > 請記住，您的 web 存取會使用埠9443。 使用此登入 web 伺服器。 ZD&T 的使用者識別碼為 **zdtadmin** ，密碼為 **password**。

    ![IBM zD&T Enterprise Edition 歡迎畫面](media/02-welcome.png)

3. 在 [ **快速入門** ] 頁面的 [ **設定**] 底下，選取 [ **映射存放裝置**]。

     ![IBM zD&T Enterprise Edition 快速入門畫面](media/03-quickstart.png)

4. 在 [ **設定映射儲存體** ] 頁面上，選取 [ **SSH 檔案傳輸通訊協定**]。

5. 在 [ **主機名稱**] 中，輸入 **Localhost** ，然後輸入上傳映射的目錄路徑。 例如，/home/MyUserID/ZDT/adcd/nov2017/volumes。

6. 輸入 VM 的 **使用者識別碼** 和 **密碼** 。 請勿使用 ZD&T 使用者識別碼和密碼。

7. 請測試連線以確定您有存取權，然後選取 [ **儲存** ] 以儲存設定。

## <a name="configure-the-target-environments"></a>設定目標環境

下一步是設定 zD&T 目標環境。 此模擬託管環境是您的映射執行所在的位置。

1. 在 [ **快速入門** ] 頁面的 [ **設定**] 底下，選取 [ **目標環境**]。

2. 在 [ **設定目標環境** ] 頁面上，選取 [ **加入目標**]。

3. 選取 [ **Linux**]。 IBM 支援兩種類型的環境： Linux 和雲端 (OpenStack) ，但此示範會在 Linux 上執行。

4. 在 [ **新增目標環境** ] 頁面的 [ **主機名稱**] 中，輸入 **localhost**。 將 **SSH 埠** 設定為 **22**。

5. 在 [ **目標環境標籤** ] 方塊中，輸入標籤，例如 **MyCICS。**

     ![新增目標環境畫面](media/04-add-target.png)

## <a name="configure-adcd-and-deploy"></a>設定 ADCD 和部署

完成先前的設定步驟之後，您必須設定 zD&T 使用封裝 () 和目標環境。 同樣地，您可以使用 zD&T 中的映射儲存體進程，這可讓您掛接和使用映射。 它可以使用 SSH 或 FTP。

1. 在 [ **快速入門** ] 頁面的 [ **設定**] 底下，選取 [ **ADCD**]。 隨即出現一組指示，告訴您需要先完成的步驟，才能裝載 ADCD 封裝。 這解釋了為什麼我們以稍早的方式來命名目標目錄。

2. 假設所有影像都已上傳至正確的目錄，請按一下右下方的 ADCD 連結中所顯示的 **影像** ，如下列螢幕擷取畫面所示 () 。

     ![IBM zD&T Enterprise Edition-設定 ADCD 畫面](media/05-adcd.png)

## <a name="create-the-image"></a>建立映像

當先前的設定步驟完成時，[ **使用 ADCD 元件建立映射** ] 頁面隨即出現。

1. 選取在此案例中 (11 月2017的磁片區，) 顯示該磁片區中的不同套件。

2. 針對此示範，請選取 [ **客戶資訊控制系統] (CICS) -5.3**。

3. 在 [ **映射名稱** ] 方塊中，輸入映射的名稱，例如 **MyCICS 映射**。

4. 選取右下方的 [ **建立影像** ] 按鈕。

     ![IBM zD&T 企業版-使用 ADCD 元件建立映射畫面](media/06-adcd.png)

5. 在出現的視窗中，告訴您映射已成功部署，請選擇 [ **部署映射**]。

6. 在 [將 **映射部署至目標環境** ] 頁面上，選取您在前一頁建立的映射 (**MyCICS 映射**) ，以及稍早 (**MyCICS**) 建立的目標環境。

7. 在下一個畫面上，提供 VM (的認證，而不是 ztadmin 認證) 。

8. 在 [屬性] 窗格中，輸入 **(CPs) 的中央處理器** 數目、 **系統記憶體 (GB** 的數量) ，以及執行中映射的 **部署目錄** 。 因為這是一個示範，請將它保持在最小。

9. 確定已選取此方塊，在 **部署後自動將 IPL 命令發出至 z/OS**。

     ![屬性畫面](media/07-properties.png)

10. 選取 [ **完成**]。

11. 選取 [將 **映射部署至目標環境**] 頁面中的 [**部署映射**]。

您的映射現在已可部署，並已準備好由3270終端機模擬器掛接。

> [!NOTE]
> 如果您收到錯誤，指出沒有足夠的磁碟空間，請注意該區域需要 151 Gb。

恭喜！ 您現在正在 Azure 上執行 IBM 大型主機環境。

## <a name="learn-more"></a>深入了解

- [大型主機遷移：誤解和事實](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [Azure 上的 IBM DB2 pureScale](../../../linux/ibm-db2-purescale-azure.md)
- [疑難排解](../../../troubleshooting/index.yml)
- [將大型主機揭開至 Azure 遷移](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-on-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
