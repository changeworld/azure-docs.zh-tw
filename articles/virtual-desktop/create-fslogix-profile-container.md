---
title: FSLogix 設定檔案容器 NetApp Windows 虛擬桌面 - Azure
description: 如何使用 Windows 虛擬桌面中的 Azure NetApp 檔案創建 FSLogix 配置檔案容器。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 149fb5818ee360c7333997655ea9eb8d7ded346c
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81270888"
---
# <a name="create-an-fslogix-profile-container-for-a-host-pool-using-azure-netapp-files"></a>使用 Azure NetApp 檔案為主機池建立 FSLogix 設定檔容器

我們建議使用 FSLogix 配置檔容器作為[Windows 虛擬桌面服務](overview.md)的使用者配置檔解決方案。 FSLogix 設定檔容器將完整的使用者設定檔案儲存在單個容器中,並設計用於在非持久性遠端計算環境中(如 Windows 虛擬桌面)中漫遊設定檔。 登錄時,容器使用本地支援的虛擬硬碟 (VHD) 和超 V 虛擬硬碟 (VHDX) 動態附加到計算環境。 這些高級篩選器驅動程式技術允許使用者設定檔立即可用,並完全像本地使用者設定檔一樣顯示在系統中。 要瞭解有關 FSLogix 設定檔容器的更多內容,請參閱[FSLogix 設定檔容器和 Azure 檔案](fslogix-containers-azure-files.md)。

您可以使用[Azure NetApp 檔案](https://azure.microsoft.com/services/netapp/)創建 FSLogix 配置檔容器,這是一種易於使用的 Azure 本機平臺服務,可協助客戶快速可靠地為其 Windows 虛擬桌面環境預配企業級 SMB 卷。 要瞭解有關 Azure NetApp 檔的更多內容,請參閱[什麼是 Azure NetApp 檔?](../azure-netapp-files/azure-netapp-files-introduction.md)

本指南將介紹如何設置 Azure NetApp 檔案帳戶,並在 Windows 虛擬桌面中創建 FSLogix 配置檔容器。

本文假定您已經在 Windows 虛擬桌面環境中設置[主機池](create-host-pools-azure-marketplace.md)並分組到一個或多個租戶中。 要瞭解如何設置租戶,請參閱[在 Windows 虛擬桌面中創建租戶](tenant-setup-azure-active-directory.md)和[我們的技術社區部落格文章](https://techcommunity.microsoft.com/t5/Windows-IT-Pro-Blog/Getting-started-with-Windows-Virtual-Desktop/ba-p/391054)。

本指南中的說明是專門針對 Windows 虛擬桌面使用者的。 如果要尋找有關如何在 Windows 虛擬桌面之外設定 Azure NetApp 檔和建立 FSLogix 設定檔容器的更常規指南,請參閱[設定 Azure NetApp 檔並創建 NFS 卷快速啟動](../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md)。

>[!NOTE]
>本文不介紹保護對 Azure NetApp 檔共享的訪問的最佳做法。

>[!NOTE]
>如果要尋找有關 Azure 上不同 FSLogix 設定檔案容器儲存選項的比較材料,請參考[FSLogix 設定檔案容器的儲存選項](store-fslogix-profile.md)。

## <a name="prerequisites"></a>Prerequisites

在為主機池建立 FSLogix 設定檔容器之前,必須:

- 設定與設定 Windows 虛擬桌面
- 預先使用 Windows 虛擬桌面主機池
- [開啟 Azure NetApp 檔案訂閱](../azure-netapp-files/azure-netapp-files-register.md)

## <a name="set-up-your-azure-netapp-files-account"></a>設定 Azure NetApp 檔案帳戶

要開始,您需要設置 Azure NetApp 檔案帳戶。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 確保您的帳戶具有參與者或管理員許可權。

2. 選擇搜索欄右側的**Azure 雲外殼圖示**以打開 Azure 雲外殼。

3. 開啟 Azure 雲外殼後,選擇**PowerShell**。

4. 如果這是您第一次使用 Azure 雲外殼,請在同一訂閱中創建存儲帳戶,以保留 Azure NetApp 檔和 Windows 虛擬桌面。

   ![視窗底部的創建存儲按鈕以紅色突出顯示的存儲帳戶視窗。](media/create-storage-button.png)

5. 載入 Azure 雲外殼後,運行以下兩個 cmdlet。

   ```azurecli
   az account set --subscription <subscriptionID>
   ```

   ```azurecli
   az provider register --namespace Microsoft.NetApp --wait
   ```

6. 在視窗的左側,選擇 **「所有服務**」。。 在選單頂端顯示的搜尋框中輸入**Azure NetApp 檔案**。

   ![在「所有服務」搜尋框中輸入「Azure NetApp 檔」的用戶的螢幕截圖。 搜索結果顯示 Azure NetApp 檔資源。](media/azure-netapp-files-search-box.png)


7. 在搜尋結果中選擇**Azure NetApp 檔**,然後選擇 **"創建**"。

8. 選擇「**新增**」 按鈕。
9. 開啟 **'新增 NetApp 帳號**'選項卡時,輸入以下值:

    - 對於**名稱**,請輸入您的 NetApp 帳戶名稱。
    - 對於**訂閱**,從下拉菜單中選擇您在步驟 4 中設置的存儲帳戶的訂閱。
    - 對於**資源組**,從下拉菜單中選擇現有資源組,或者通過選擇 **"創建新**"來創建新資源組。
    - 對於**位置**,從下拉菜單中選擇 NetApp 帳戶的區域。 此區域必須與會話主機 VM 的區域相同。

   >[!NOTE]
   >Azure NetApp 檔目前不支援跨區域安裝卷。

10. 完成後,選擇 **「創建**」以創建 NetApp 帳戶。

## <a name="create-a-capacity-pool"></a>建立容量池

接下來,建立新的容量池: 

1. 轉到 Azure NetApp 檔案功能表並選擇新帳戶。
2. 在帳號選單中,選擇「儲存服務」下的**容量池**。
3. 選擇 **「添加池**」。。
4. 開啟 **'新增容量池**'選項卡時,輸入以下值:

    - 對於**名稱**,輸入新容量池的名稱。
    - 對於**服務級別**,從下拉選單中選擇所需的值。 我們建議大多數環境**都提供進階版**。
       >[!NOTE]
       >高級"設置提供進階服務等級(256 MBps)的最小輸送量。 您可能需要為生產環境調整此輸送量。 最終輸送量基於[輸送量限制](../azure-netapp-files/azure-netapp-files-service-levels.md)中描述的關係。
    - 對於**大小 (TiB),** 輸入最適合您需求的容量池大小。 最小尺寸為 4 TiB。

5. 完成後，按一下 [確定]****。

## <a name="join-an-active-directory-connection"></a>加入活動目錄連線

之後,您需要加入活動目錄連接。

1. 在頁面左側的選單中選擇 **「活動目錄」連接**,然後選擇 **「加入**」按鈕以打開 **「加入活動目錄」** 頁。

   ![加入活動目錄連接功能表的螢幕截圖。](media/active-directory-connections-menu.png)

2. 在 **'加入活動目錄'** 頁中輸入以下值以加入連線:

    - 對於**主 DNS,** 輸入環境中可解析網域名稱的 DNS 伺服器的 IP 位址。
    - 對於**域**,輸入完全限定的功能變數名稱 (FQDN)。
    - 對於**SMB 伺服器(電腦帳戶)前置碼**,輸入要追加到電腦帳戶名稱的字串。
    - 對於**使用者名**,輸入具有執行域聯接許可權的帳戶的名稱。
    - 對於**密碼**,輸入帳戶的密碼。

## <a name="create-a-new-volume"></a>建立新的磁碟區

接下來,您需要創建新卷。

1. 選擇 **"卷**",然後選擇 **"添加卷**"。

2. 開啟「**建立標籤**」選項卡時,輸入以下值:

    - 對於**卷名稱**,輸入新卷的名稱。
    - 對於**容量池**,從下拉菜單中選擇剛剛創建的容量池。
    - 對於**配額 (GiB),** 輸入適合您的環境的卷大小。
    - 對於**虛擬網路**,從下拉選單中選擇與網域控制器連接的現有虛擬網路。
    - 在**子網**下,選擇 **"創建新**"。 請記住,此子網將委派給 Azure NetApp 檔。

3.  選擇 **「下\>一步:協定**」以打開「協定」選項卡並配置卷訪問參數。

## <a name="configure-volume-access-parameters"></a>設定已參考參數

建立捲後,配置卷訪問參數。

1.  選擇**SMB**作為協定類型。
2.  在 **「活動目錄**」下拉選單中的「設定」下,選擇最初在[「加入活動目錄」連接](create-fslogix-profile-container.md#join-an-active-directory-connection)中連接的同一目錄。 請記住,每個訂閱有一個活動目錄的限制。
3.  在 **"共用名稱**"文字框中,輸入會話主機池及其使用者使用的共用的名稱。

4.  選擇 **「查看 + 在**頁面底部建立」。 這將打開驗證頁。 成功驗證卷後,選擇 **"創建**"。

5.  此時,新卷將開始部署。 部署完成後,可以使用 Azure NetApp 檔共用。

6.  要查看裝載路徑,請選擇 **「轉到資源」** 並在「概述」選項卡中尋找它。

    !["概述"螢幕的螢幕截圖,紅色箭頭指向裝載路徑。](media/overview-mount-path.png)

## <a name="configure-fslogix-on-session-host-virtual-machines-vms"></a>在工作階段主機虛擬機器 (VM) 上設定 FSLogix

將檔案[分享主式主式的主式池建立設定檔容器](create-host-pools-user-profile.md)。

1. [下載 FSLogix 代理 .zip 檔案](https://go.microsoft.com/fwlink/?linkid=2084562&clcid=0x409),而您仍在工作階段主機 VM 中遠端。

2. 解壓縮下載的檔。

3. 在檔案中,轉到**x64** > **版本**並執行**FSLogixAppsSetup.exe**。 安裝功能表將打開。

4.  如果您有產品密鑰,請在"產品密鑰"文字框中輸入它。

5. 選中**我同意許可條款和條件**旁邊的複選框。

6. 選取 [安裝]  。

7. 導航到**\\C:\\程式\\檔案 FSLogix 應用**以確認已安裝的代理。

8. 從"開始"功能表中,以管理員身份運行**RegEdit。**

9. 導航到**\\計算機\\\\HKEY_LOCAL_MACHINE 軟體 FSLogix。**

10. 建立名為**設定檔**的鍵。

11.  建立名為 **「 已啟用」** 的值 **,REG_DWORD**類型設定為**1**。

12. 使用**多字串**類型創建名為**VHDData 的值**,並將其資料值設置為 Azure NetApp 檔共享的 URI。

13. 創建名為 **「刪除本地設定檔時VHD應應用**」的值,DWORD 值為 1,以避免在登錄前出現現有本地設定檔的問題。

     >[!WARNING]
     >建立"刪除本地配置檔時應應用"值時要小心。 當 FSLogix 設定檔系統確定使用者應具有 FSLogix 配置檔,但本地設定檔已存在時,設定檔容器將永久刪除本地設定檔。 然後,使用者將使用新的 FSLogix 配置檔登錄。

## <a name="assign-users-to-session-host"></a>將使用者配置給工作階段主機

1. 以管理員身份打開**PowerShell ISE**並登錄到 Windows 虛擬桌面。

2. 執行下列 Cmdlet：

   ```powershell
   Import-Module Microsoft.RdInfra.RdPowershell
   # (Optional) Install-Module Microsoft.RdInfra.RdPowershell
   $brokerurl = "https://rdbroker.wvd.microsoft.com"
   Add-RdsAccount -DeploymentUrl $brokerurl
   ```

3. 當提示您輸入認證時,在 Windows 虛擬桌面租戶上輸入具有租戶建立者或 RDS 擁有者/RDS 參與者角色的使用者的認證。

4. 執行以下 cmdlet 將使用者配置給遠端桌面群組:

   ```powershell
   $wvdTenant = "<your-wvd-tenant>"
   $hostPool = "<wvd-pool>"
   $appGroup = "Desktop Application Group"
   $user = "<user-principal>"
   Add-RdsAppGroupUser $wvdTenant $hostPool $appGroup $user
   ```

## <a name="make-sure-users-can-access-the-azure-netapp-file-share"></a>確保使用者可以存取 Azure NetApp 檔案分享

1. 開啟您的網際網路瀏覽器,然後<https://rdweb.wvd.microsoft.com/webclient/index.html>轉到 。

2. 使用分配給遠端桌面組的使用者的憑據登錄。

3. 建立使用者會話後,使用管理帳戶登錄到 Azure 門戶。

4. 打開**Azure NetApp 檔案**,選擇 Azure NetApp 檔案帳戶,然後選擇 **「卷**」。 打開"卷"菜單后,選擇相應的卷。

   ![在 Azure 門戶中設置的 NetApp 帳戶的螢幕截圖,選中"卷"按鈕。](media/netapp-account.png)

5. 跳到 **「概述」** 選項卡,確認 FSLogix 設定檔容器正在使用空間。

6. 使用遠端桌面直接連接到主機池的任何 VM 部分,然後打開**檔案資源管理器。** 然後導航到**裝載路徑**(在下面的示例中,裝載路徑anf-SMB-3863.gt1107.onmicrosoft.com \\ \\ \\af-VOL)。

   在此資料夾中,應該有類似於以下範例中的配置檔 VHD(或 VHDX)。

   ![裝載路徑中資料夾內容的屏幕截圖。 裡面是一個名為"Profile_ssbb"的 VHD 檔。](media/mount-path-folder.png)

## <a name="next-steps"></a>後續步驟

您可以使用 FSLogix 設定檔容器來設置使用者設定檔共用。 要瞭解如何使用新容器建立使用者設定檔分享,請參閱[使用檔案共享為主機池建立設定檔容器](create-host-pools-user-profile.md)。
