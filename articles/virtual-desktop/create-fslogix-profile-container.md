---
title: FSLogix 設定檔容器 NetApp Windows 虛擬桌面-Azure
description: 如何使用 Windows 虛擬桌面中的 Azure NetApp Files 建立 FSLogix 設定檔容器。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 11/25/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 7cd989d944a35af8b1fd932643826e1aedd03962
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370197"
---
# <a name="create-an-fslogix-profile-container-for-a-host-pool-using-azure-netapp-files"></a>使用 Azure NetApp Files 建立主機集區的 FSLogix 設定檔容器

我們建議使用 FSLogix 設定檔容器作為[Windows 虛擬桌面服務](overview.md)的使用者設定檔解決方案。 FSLogix 設定檔容器會將完整的使用者設定檔儲存在單一容器中，並設計為在 Windows 虛擬桌面之類的非持續性遠端運算環境中漫遊設定檔。 當您登入時，容器會使用本機支援的虛擬硬碟（VHD）和 Hyper-v 虛擬硬碟（VHDX），以動態方式連接到計算環境。 這些先進的篩選驅動程式技術可讓使用者設定檔立即可用，並以本機使用者設定檔的形式出現在系統中。 若要深入瞭解 FSLogix 設定檔容器，請參閱[FSLogix 設定檔容器和 Azure 檔案](fslogix-containers-azure-files.md)。

您可以使用[Azure NetApp Files](https://azure.microsoft.com/services/netapp/)建立 FSLogix 設定檔容器，這是一種便於使用的 azure 原生平臺服務，可協助客戶針對其 Windows 虛擬桌面環境快速且可靠地布建企業級 SMB 磁片區。 若要深入瞭解 Azure NetApp Files，請參閱[什麼是 Azure Netapp files？](../azure-netapp-files/azure-netapp-files-introduction.md)

本指南將說明如何設定 Azure NetApp Files 帳戶，並在 Windows 虛擬桌面中建立 FSLogix 設定檔容器。

本文假設您已設定[主機](create-host-pools-azure-marketplace.md)集區，並將其分組為 Windows 虛擬桌面環境中的一或多個租使用者。 若要瞭解如何設定租使用者，請參閱[在 Windows 虛擬桌面中建立租](tenant-setup-azure-active-directory.md)使用者和[我們的技術社區 blog 文章](https://techcommunity.microsoft.com/t5/Windows-IT-Pro-Blog/Getting-started-with-Windows-Virtual-Desktop/ba-p/391054)。

本指南中的指示專為 Windows 虛擬桌面使用者而提供。 如果您要尋找如何設定 Azure NetApp Files 並在 Windows 虛擬桌面之外建立 FSLogix 設定檔容器的一般指引，請參閱[設定 Azure Netapp files 和建立 NFS 磁片區快速入門](../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md)。

>[!NOTE]
>本文並未涵蓋安全存取 Azure NetApp Files 共用的最佳作法。

>[!NOTE]
>如果您要尋找 Azure 上不同 FSLogix 設定檔容器儲存體選項的比較資料，請參閱[FSLogix 設定檔容器的儲存體選項](store-fslogix-profile.md)。

## <a name="prerequisites"></a>Prerequisites

您必須先執行下列動作，才能建立主機集區的 FSLogix 設定檔容器：

- 安裝和設定 Windows 虛擬桌面
- 布建 Windows 虛擬桌面主機集區
- [啟用您的 Azure NetApp Files 訂用帳戶](../azure-netapp-files/azure-netapp-files-register.md)

## <a name="set-up-your-azure-netapp-files-account"></a>設定 Azure NetApp Files 帳戶

若要開始使用，您必須設定 Azure NetApp Files 帳戶。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 請確定您的帳戶具有「參與者」或「系統管理員」許可權。

2. 選取搜尋列右邊的 [ **Azure Cloud Shell] 圖示**，以開啟 [Azure Cloud Shell]。

3. 開啟 Azure Cloud Shell 之後，請選取 [ **PowerShell**]。

4. 如果這是您第一次使用 Azure Cloud Shell，請在相同的訂用帳戶中建立儲存體帳戶，以保留您的 Azure NetApp Files 和 Windows 虛擬桌面。

   ![[儲存體帳戶] 視窗，並在視窗底部以紅色反白顯示 [建立儲存體] 按鈕。](media/create-storage-button.png)

5. Azure Cloud Shell 載入之後，請執行下列兩個 Cmdlet。

   ```powershell
   az account set --subscription <subscriptionID>
   ```

   ```powershell
   az provider register --namespace Microsoft.NetApp --wait
   ```

6. 在視窗左側，選取 [**所有服務**]。 在出現于功能表頂端的 [搜尋] 方塊中，輸入**Azure NetApp Files** 。

   ![使用者在 [所有服務] 搜尋方塊中輸入「Azure NetApp Files」的螢幕擷取畫面。 搜尋結果會顯示 Azure NetApp Files 資源。](media/azure-netapp-files-search-box.png)


7. 在搜尋結果中選取 [ **Azure NetApp Files** ]，然後選取 [**建立**]。

8. 選取 [新增] 按鈕。
9. 當 [**新的 NetApp 帳戶**] 索引標籤開啟時，輸入下列值：

    - 針對 [**名稱**]，輸入您的 NetApp 帳戶名稱。
    - 針對 [**訂**用帳戶]，從下拉式功能表中選取您在步驟4中設定之儲存體帳戶的訂用帳戶。
    - 針對 [**資源群組**]，請從下拉式功能表中選取現有的資源群組，或選取 **[新建]** 來建立一個新的。
    - 針對 [**位置**]，從下拉式功能表中選取您 NetApp 帳戶的 [區域]。 此區域必須與您的工作階段主機 Vm 位於相同的區域。

   >[!NOTE]
   >Azure NetApp Files 目前不支援跨區域裝載磁片區。

10. 當您完成時，請選取 [**建立**] 以建立您的 NetApp 帳戶。

## <a name="create-a-capacity-pool"></a>建立容量集區

接下來，建立新的容量集區： 

1. 移至 Azure NetApp Files 功能表，然後選取您的新帳戶。
2. 在 [帳戶] 功能表中，選取 [儲存體服務] 底下的 [**容量**集區]。
3. 選取 [**新增集**區]。
4. 當 [**新增容量集**區] 索引標籤開啟時，輸入下列值：

    - 針對 [**名稱**]，輸入新容量集區的名稱。
    - 針對 [**服務等級**]，從下拉式功能表中選取您想要的值。 我們建議大部分環境的**Premium** 。
       >[!NOTE]
       >Premium 設定提供高階服務等級可用的最小輸送量，也就是 256 MBps。 您可能需要針對生產環境調整此輸送量。 最終輸送量是以[輸送量限制](../azure-netapp-files/azure-netapp-files-service-levels.md)中所述的關聯性為基礎。
    - 針對 [**大小（TiB）** ]，輸入最符合您需求的容量集區大小。 大小下限為 4 TiB。

5. 當您完成時，請選取 **[確定]** 。

## <a name="join-an-active-directory-connection"></a>加入 Active Directory 連接

之後，您必須加入 Active Directory 連接。

1. 在頁面左側的功能表中選取 [ **Active Directory 連接**]，然後選取 [**聯結**] 按鈕以開啟 [**聯結 Active Directory** ] 頁面。

   ![[聯結 Active Directory 連接] 功能表的螢幕擷取畫面。](media/active-directory-connections-menu.png)

2. 在 [**聯結 Active Directory** ] 頁面中輸入下列值，以加入連接：

    - 在 [**主要 DNS**] 中，輸入您的環境中可解析功能變數名稱的 DNS 伺服器 IP 位址。
    - 針對 [**網域**]，輸入您的完整功能變數名稱（FQDN）。
    - 針對 [ **SMB 伺服器（電腦帳戶）首碼**]，輸入您要附加到電腦帳戶名稱的字串。
    - 針對 **[使用者名稱]** ，輸入具有執行加入網域之許可權的帳戶名稱。
    - 針對 [**密碼**]，輸入帳戶的密碼。

  >[!NOTE]
  >最佳做法是確認您在 [[加入 Active Directory](create-fslogix-profile-container.md#join-an-active-directory-connection)連線] 中建立的電腦帳戶，已出現在 [**電腦**] 或**您企業相關 OU**底下的網域控制站中。

## <a name="create-a-new-volume"></a>建立新的磁碟區

接下來，您必須建立新的磁片區。

1. 選取 [**磁片**區]，然後選取 [**新增磁片**區]。

2. 當 [**建立磁片**區] 索引標籤開啟時，輸入下列值：

    - 針對 [**磁片區名稱**]，輸入新磁片區的名稱。
    - 針對 [**容量集**區]，從下拉式功能表中選取您剛才建立的容量集區。
    - 針對 [**配額（GiB）** ]，輸入適用于您環境的磁片區大小。
    - 針對 [**虛擬網路**]，從下拉式功能表中選取可連線到網域控制站的現有虛擬網路。
    - 在 [**子網**] 下，選取 [**新建**]。 請記住，此子網將會委派給 Azure NetApp Files。

3.  選取 **[下一步：通訊協定 \>] \>** 開啟 [通訊協定] 索引標籤，並設定磁片區存取參數。

## <a name="configure-volume-access-parameters"></a>設定磁片區存取參數

建立磁片區之後，請設定磁片區存取參數。

1.  選取 [ **SMB** ] 作為通訊協定類型。
2.  在 [ **Active Directory** ] 下拉式功能表的 [設定] 下，選取您原本在 [[加入 Active Directory 連接](create-fslogix-profile-container.md#join-an-active-directory-connection)] 中連接的相同目錄。 請記住，每個訂用帳戶有一個 Active Directory 的限制。
3.  在 [**共用名稱**] 文字方塊中，輸入工作階段主機集區及其使用者所使用的共用名稱。

4.  選取頁面底部的 [審核] 和 [**建立**]。 這會開啟 [驗證] 頁面。 成功驗證您的磁片區之後，請選取 [**建立**]。

5.  此時，新的磁片區將會開始部署。 部署完成之後，您就可以使用 Azure NetApp Files 共用。

6.  若要查看掛接路徑，請選取 [**移至資源**]，然後在 [總覽] 索引標籤中尋找它。

    ![[總覽] 畫面的螢幕擷取畫面，其中紅色箭號指向掛接路徑。](media/overview-mount-path.png)

## <a name="configure-fslogix-on-session-host-virtual-machines-vms"></a>在工作階段主機虛擬機器（Vm）上設定 FSLogix

本節是以[使用檔案共用建立主機集區的設定檔容器](create-host-pools-user-profile.md)為基礎。

1. 當您仍在工作階段主機 VM 中進行遠端處理時，請[下載 FSLogix 代理程式 .zip](https://go.microsoft.com/fwlink/?linkid=2084562&clcid=0x409)檔案。

2. 將下載的檔案解壓縮。

3. 在檔案中，移至 [ **x64** ] > [**發行**]，然後執行 [ **FSLogixAppsSetup**]。 [安裝] 功能表隨即開啟。

4.  如果您有產品金鑰，請在 [產品金鑰] 文字方塊中輸入。

5. 選取 [**我同意授權條款及條件**] 旁的核取方塊。

6. 選取 [安裝]。

7. 流覽至**C：\\Program Files\\FSLogix\\Apps**以確認代理程式已安裝。

8. 在 [開始] 功能表中，以系統管理員身分執行**RegEdit** 。

9. 流覽至**電腦\\HKEY_LOCAL_MACHINE\\軟體\\FSLogix**。

10. 建立名為**Profiles**的金鑰。

11.  建立名為**Enabled**的值，並將**REG_DWORD**類型設為**1**的資料值。

12. 使用**多字串**類型建立名為**VHDLocations**的值，並將其資料值設定為 Azure NETAPP Files 共用的 URI。

13. 建立名為**DeleteLocalProfileWhenVHDShouldApply**的值，並使用 DWORD 值1來避免現有的本機設定檔發生問題，然後再登入。

     >[!WARNING]
     >建立 DeleteLocalProfileWhenVHDShouldApply 值時請小心。 當 FSLogix 配置檔案系統判斷使用者應該有 FSLogix 設定檔，但本機設定檔已經存在時，設定檔容器將會永久刪除本機設定檔。 接著，使用者會使用新的 FSLogix 設定檔登入。

## <a name="assign-users-to-session-host"></a>將使用者指派給工作階段主機

1. 以系統管理員身分開啟**POWERSHELL ISE** ，並登入 Windows 虛擬桌面。

2. 執行下列 Cmdlet：

   ```powershell
   Import-Module Microsoft.RdInfra.RdPowershell
   # (Optional) Install-Module Microsoft.RdInfra.RdPowershell
   $brokerurl = "https://rdbroker.wvd.microsoft.com"
   Add-RdsAccount -DeploymentUrl $brokerurl
   ```

3. 當系統提示您提供認證時，請在 Windows 虛擬桌面租使用者上輸入具有租使用者建立者或 RDS 擁有者/RDS 參與者角色的使用者認證。

4. 執行下列 Cmdlet，將使用者指派給遠端桌面群組：

   ```powershell
   $wvdTenant = "<your-wvd-tenant>"
   $hostPool = "<wvd-pool>"
   $appGroup = "Desktop Application Group"
   $user = "<user-principal>"
   Add-RdsAppGroupUser $wvdTenant $hostPool $appGroup $user
   ```

## <a name="make-sure-users-can-access-the-azure-netapp-file-share"></a>請確定使用者可以存取 Azure NetApp File share

1. 開啟網際網路瀏覽器並移至 <https://rdweb.wvd.microsoft.com/webclient/index.html>。

2. 使用指派給遠端桌面群組之使用者的認證登入。

3. 當您建立使用者會話之後，請使用系統管理帳戶登入 Azure 入口網站。

4. 開啟 [ **Azure Netapp files**]，選取您的 Azure netapp files 帳戶，然後選取 [**磁片**區]。 [磁片區] 功能表開啟之後，請選取對應的磁片區。

   ![您稍早在 Azure 入口網站中設定的 NetApp 帳戶螢幕擷取畫面，並已選取 [磁片區] 按鈕。](media/netapp-account.png)

5. 移至 [**總覽**] 索引標籤，並確認 FSLogix 設定檔容器使用空間。

6. 使用遠端桌面直接連接到主機集區的任何 VM 部分，然後開啟檔案**瀏覽器。** 然後流覽至**掛接路徑**（在下列範例中，掛接路徑會 \\\\anf-SMB-3863.gt1107.onmicrosoft.com\\及-VOL）。

   在此資料夾中，應該會有一個設定檔 VHD （或 VHDX），如下列範例所示。

   ![掛接路徑中資料夾內容的螢幕擷取畫面。 內部是名為 "Profile_ssbb" 的單一 VHD 檔案。](media/mount-path-folder.png)

## <a name="next-steps"></a>後續步驟

您可以使用 FSLogix 設定檔容器來設定使用者設定檔共用。 若要瞭解如何使用新的容器建立使用者設定檔共用，請參閱[使用檔案共用建立主機集區的設定檔容器](create-host-pools-user-profile.md)。
