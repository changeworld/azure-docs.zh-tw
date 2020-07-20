---
title: 在 Azure Lab Services 中啟用適用於 Linux 的圖形化遠端桌面 | Microsoft Docs
description: 了解如何在 Azure Lab Services 中為實驗室中的 Linux 虛擬機器啟用遠端桌面。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 4f50510adf978aeb63b2b5337d21e6f9dec76196
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445758"
---
# <a name="enable-graphical-remote-desktop-for-linux-virtual-machines-in-azure-lab-services"></a>在 Azure Lab Services 中為 Linux 虛擬機器啟用圖形化遠端桌面
本文示範如何執行下列工作：

- 啟用 Linux VM 的圖形化遠端桌面工作階段
- 如何使用 RDP (遠端桌面通訊協定) 或 X2Go 遠端桌面用戶端連線至 Linux VM

## <a name="set-up-graphical-remote-desktop-solution"></a>設定圖形化遠端桌面解決方案
以 **Linux** 映像建立實驗室時，會自動設定 **SSH** (安全殼層) 存取權，讓講師可以從命令列使用 SSH 連線到範本 VM。  同樣地，當發佈範本 VM 時，學生也可以使用 SSH 連線至其 VM。

若要使用 **GUI** (圖形化使用者介面) 連線到 Linux VM，建議使用 **RDP** 或 **X2Go**。  這兩個選項都需要講師在範本 VM 上進行一些額外的設定：

### <a name="rdp-setup"></a>RDP 設定
若要使用 RDP，講師必須：
  - 啟用遠端桌面連線，開啟 RDP 的 VM 連接埠時尤其需要如此做。
  - 安裝 RDP 遠端桌面伺服器。
  - 安裝 Linux 圖形化桌面環境 (例如 MATE、XFCE 等等)。

### <a name="x2go-setup"></a>X2Go 設定
若要使用 X2Go，講師必須：
- 安裝 X2Go 遠端桌面伺服器。
- 安裝 Linux 圖形化桌面環境 (例如 MATE、XFCE 等等)。

X2Go 會使用對 SSH 啟用的同個連接埠。  因此，要在 VM 上對 X2Go 開啟連接埠時，無需額外設定。

> [!NOTE]
> 在某些情況下 X2Go 的效能較佳，例如使用 Ubuntu LTS 18.04 時。  如果您使用的是 RDP，而且注意到與圖形化桌面環境互動時會有延遲，可考慮嘗試 X2Go，效能可能會有所改善。

> [!IMPORTANT]
>  有些市集映像已經安裝圖形化桌面環境和遠端桌面伺服器。  例如，[Linux 適用的資料科學虛擬機器 (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) 已經[安裝 XFCE 與 X2Go 伺服器並設定為接受用戶端連線](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro#x2go)。

## <a name="enable-remote-desktop-connection-for-rdp"></a>啟用 RDP 遠端桌面連線檔案

只有使用 RDP 連線時，才需要執行此步驟。  如果您打算改用 X2Go，可以跳到下一節，因為 X2Go 使用的是 SSH 連接埠。

1.  在實驗室建立期間，講師可以選擇 [啟用遠端桌面連線]。  講師必須**啟用**此選項，才能在 RDP 遠端桌面工作階段所需的 Linux VM 上開啟連接埠。  否則，如果將此選項保持**停用**，則只會為 SSH 開啟連接埠。
  
    ![啟用 Linux 映像的遠端桌面連線](./media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

2. 在 [啟用遠端桌面連線] 訊息方塊中，選取 [繼續使用遠端桌面]。 

    ![啟用 Linux 映像的遠端桌面連線](./media/how-to-enable-remote-desktop-linux/enabling-remote-desktop-connection-dialog.png)

## <a name="install-rdp-or-x2go"></a>安裝 RDP 或 X2Go

實驗室建立後，講師需要確定已在範本 VM 上安裝圖形化桌面環境和遠端桌面伺服器。  講師必須先使用 SSH 連線到範本 VM，才能安裝以下項目適用的套件：
- RDP 或 X2Go 遠端桌面伺服器。
- 圖形化桌面環境，例如 MATE、XFCE 等等。

設定完成後，講師可以使用 **Microsoft 遠端桌面 (RDP)** 用戶端或 **X2Go** 用戶端連線至範本 VM。

請遵循下列步驟來設定範本 VM：

1. 如果您在工具列上看到 [自訂範本]，請加以選取。 接著，在 [自訂範本] 對話方塊上選取 [繼續]。 此動作會啟動範本 VM。  

    ![自訂範本](./media/how-to-enable-remote-desktop-linux/customize-template.png)
1. 啟動範本 VM 之後，您可選取 [連線範本]，然後選取工具列上的 [透過 SSH 連線]。 

    ![在實驗室建立後透過 RDP 連線到範本](./media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 
1. 您會看到下列 [連線到虛擬機器] 對話方塊。 選取文字方塊旁邊的 [複製] 按鈕，將其複製到剪貼簿。 儲存 SSH 連線資訊。 使用此連線資訊從 SSH 終端機 (如 [Putty](https://www.putty.org/)) 連線至虛擬機器。
 
    ![SSH 連接字串](./media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

4. 安裝 RDP 或 X2Go，以及您選擇的圖形化桌面環境。  請參考下列指示：
    - [安裝和設定 RDP](https://docs.microsoft.com/azure/virtual-machines/linux/use-remote-desktop)
    - [安裝和設定 X2Go](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/X2GoRemoteDesktop)

## <a name="connect-to-the-template-vm-via-the-gui"></a>透過 GUI 連線至範本 VM

範本 VM 設定完成後，講師可以使用 **Microsoft 遠端桌面 (RDP)** 用戶端或 **X2Go** 用戶端，透過 GUI 連線。  您使用的用戶端，則取決於將 RDP 還是 X2Go 設定為範本 VM 上的遠端桌面伺服器。  

### <a name="microsoft-remote-desktop-rdp-client"></a>Microsoft 遠端桌面 (RDP) 用戶端

Microsoft 遠端桌面 (RDP) 用戶端用於連線至已設定 RDP 的範本 VM。  遠端桌面用戶端可以使用於 Windows、Chromebook、Mac 等。  如需更多詳細資料，請參閱[遠端桌面用戶端](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)的相關文章。

請根據用來連線到範本 VM 的電腦類型，遵循下列步驟進行：

- Windows
  1. 按一下實驗室工具列上的 [連線至範本]，然後選取 [透過 RDP 連線]，以連線至範本 VM。 
  1. 儲存並使用 RDP 檔案，以使用遠端桌面用戶端連線至範本 VM。 
  1. 通常 Windows 上已安裝並設定遠端桌面用戶端。  因此，您只需要按一下 RDP 檔案加以開啟，並啟動遠端工作階段。

- Mac
  1. 按一下實驗室工具列上的 [連線到範本]，然後選取 [透過 RDP連線] 以儲存 RDP 檔案。  
  1. 然後參閱[在 Mac 上使用 RDP 連線至 VM](connect-virtual-machine-mac-remote-desktop.md) (英文) 的操作說明文章。

- Chromebook
  1. 按一下實驗室工具列上的 [連線到範本]，然後選取 [透過 RDP連線] 以儲存 RDP 檔案。  
  1. 然後參閱[在 Chromebook 上使用 RDP 連線至 VM](connect-virtual-machine-chromebook-remote-desktop.md) (英文) 的操作說明文章。

### <a name="x2go-client"></a>X2Go 用戶端

X2Go 用戶端用於連線至已設定 X2Go 的範本 VM。  使用範本 VM 的 SSH 連線資訊時，請遵循[使用 X2Go 連線至 VM](how-to-use-remote-desktop-linux-student.md#connect-to-the-student-vm-using-x2go) (英文) 操作說明文章中的步驟進行。

## <a name="next-steps"></a>後續步驟
講師在其範本 VM 上將 RDP 或 X2Go 設定完畢並發佈後，學生即可透過 GUI 遠端桌面或 SSH 連線到其 VM。

如需詳細資訊，請參閱
 - [連線至 Linux VM](how-to-use-remote-desktop-linux-student.md) (英文)
