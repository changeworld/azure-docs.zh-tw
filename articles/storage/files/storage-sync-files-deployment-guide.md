---
title: 部署 Azure 檔案同步 | Microsoft Docs
description: 瞭解如何使用 Azure 入口網站、PowerShell 或 Azure CLI，從開始到完成部署 Azure 檔案同步。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: a57956de574f74308747edd463851eb1ea4dbb42
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92489484"
---
# <a name="deploy-azure-file-sync"></a>部署 Azure 檔案同步
使用 Azure 檔案同步，將組織的檔案共用集中在 Azure 檔案服務中，同時保有內部部署檔案伺服器的彈性、效能及相容性。 Azure 檔案同步會將 Windows Server 轉換成 Azure 檔案共用的快速快取。 您可以使用 Windows Server 上可用的任何通訊協定來從本機存取資料，包括 SMB、NFS 和 FTPS。 您可以視需要存取多個散佈於世界各地的快取。

強烈建議您先閱讀[規劃 Azure 檔案服務部署](storage-files-planning.md)和[規劃 Azure 檔案同步部署](storage-sync-files-planning.md)，再完成本文章中描述的步驟。

## <a name="prerequisites"></a>必要條件

# <a name="portal"></a>[入口網站](#tab/azure-portal)

1. 在您想要部署 Azure 檔案同步的相同區域中的 Azure 檔案共用。如需詳細資訊，請參閱：
    - Azure 檔案同步的[區域可用性](storage-sync-files-planning.md#azure-file-sync-region-availability)。
    - [建立檔案共用](storage-how-to-create-file-share.md)以取得如何建立檔案共用的逐步說明。
1. 至少一個支援的 Windows Server 或 Windows Server 叢集實例與 Azure 檔案同步同步。如需支援的 Windows Server 版本和建議的系統資源的詳細資訊，請參閱 [windows 檔案伺服器考慮](storage-sync-files-planning.md#windows-file-server-considerations)。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 在您想要部署 Azure 檔案同步的相同區域中的 Azure 檔案共用。如需詳細資訊，請參閱：
    - Azure 檔案同步的[區域可用性](storage-sync-files-planning.md#azure-file-sync-region-availability)。
    - [建立檔案共用](storage-how-to-create-file-share.md)以取得如何建立檔案共用的逐步說明。
1. 至少一個支援的 Windows Server 或 Windows Server 叢集實例與 Azure 檔案同步同步。如需支援的 Windows Server 版本和建議的系統資源的詳細資訊，請參閱 [windows 檔案伺服器考慮](storage-sync-files-planning.md#windows-file-server-considerations)。

1. Az PowerShell 模組可以搭配 PowerShell 5.1 或 PowerShell 6 + 使用。 您可以在任何支援的系統（包括非 Windows 系統）上使用 Az PowerShell 模組進行 Azure 檔案同步，不過，伺服器註冊 Cmdlet 一律必須在您註冊的 Windows Server 實例上執行 (這可以直接完成或透過 PowerShell 遠端處理) 來執行。 在 Windows Server 2012 R2 上，您可以確認您至少執行 PowerShell 5.1。 \*藉由查看 **$PSVersionTable**物件的**PSVersion**屬性值：

    ```powershell
    $PSVersionTable.PSVersion
    ```

    如果您的 **PSVersion** 值小於5.1，則在 \* 安裝最新的 Windows Server 2012 R2 的情況下，您可以下載並安裝 [Windows Management Framework (WMF) 5.1](https://www.microsoft.com/download/details.aspx?id=54616)來輕鬆進行升級。 要下載並安裝的適用于 Windows Server 2012 R2 的套件為**win 8.1 andw2k12r2-KB \* \* \* \* \* \* \* -x64。** 

    PowerShell 6 + 可以與任何支援的系統搭配使用，並可透過其 [GitHub 頁面](https://github.com/PowerShell/PowerShell#get-powershell)下載。 

    > [!Important]  
    > 如果您打算使用伺服器註冊 UI，而不是直接向 PowerShell 註冊，您必須使用 PowerShell 5.1。

1. 如果您選擇使用 PowerShell 5.1，請確定至少已安裝 .NET 4.7.2。 深入瞭解您系統上的 [.NET Framework 版本和](https://docs.microsoft.com/dotnet/framework/migration-guide/versions-and-dependencies) 相依性。

    > [!Important]  
    > 如果您要在 Windows Server Core 上安裝 .NET 4.7.2 +，就必須使用 `quiet` 和 `norestart` 旗標安裝，否則安裝將會失敗。 例如，如果安裝 .NET 4.8，命令看起來會如下所示：
    > ```PowerShell
    > Start-Process -FilePath "ndp48-x86-x64-allos-enu.exe" -ArgumentList "/q /norestart" -Wait
    > ```

1. Az PowerShell 模組，您可以依照此處的指示來安裝： [安裝和設定 Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps)。
     
    > [!Note]  
    > 當您安裝 Az PowerShell 模組時，現在會自動安裝 Az. Microsoft.storagesync 模組。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 在您想要部署 Azure 檔案同步的相同區域中的 Azure 檔案共用。如需詳細資訊，請參閱：
    - Azure 檔案同步的[區域可用性](storage-sync-files-planning.md#azure-file-sync-region-availability)。
    - [建立檔案共用](storage-how-to-create-file-share.md)以取得如何建立檔案共用的逐步說明。
1. 至少一個支援的 Windows Server 或 Windows Server 叢集實例與 Azure 檔案同步同步。如需支援的 Windows Server 版本和建議的系統資源的詳細資訊，請參閱 [windows 檔案伺服器考慮](storage-sync-files-planning.md#windows-file-server-considerations)。

1. [安裝 Azure CLI](/cli/azure/install-azure-cli)

   如果您想要的話，也可以使用 Azure Cloud Shell 來完成本教學課程中的步驟。  Azure Cloud Shell 是透過瀏覽器使用的互動式 Shell 環境。  使用下列其中一種方法來開始 Cloud Shell：

   - 選取程式碼區塊右上角的 [試試看]。 **試用它** 將會開啟 Azure Cloud Shell，但不會自動將程式碼複製到 Cloud Shell。

   - 開啟 Cloud Shell，方法是前往 [https://shell.azure.com](https://shell.azure.com)

   - 在 [ [Azure 入口網站](https://portal.azure.com)中右上角的功能表列上，選取 [ **Cloud Shell** ] 按鈕

1. 登入。

   如果您使用的是 CLI 的本機安裝，請使用 [az login](/cli/azure/reference-index#az-login) 命令登入。

   ```azurecli
   az login
   ```

    請遵循您終端機上顯示的步驟，來完成驗證程序。

1. 安裝 [az filesync](/cli/azure/ext/storagesync/storagesync) Azure CLI 擴充功能。

   ```azurecli
   az extension add --name storagesync
   ```

   安裝 **microsoft.storagesync** 延伸模組參考之後，您會收到下列警告。

   ```output
   The installed extension 'storagesync' is experimental and not covered by customer support. Please use with discretion.
   ```

---

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>準備 Windows Server 以搭配 Azure 檔案同步使用
針對要與 Azure 檔案同步搭配使用的每個伺服器 (包括容錯移轉叢集中的每個伺服器節點)，停用 [Internet Explorer 增強式安全性設定]****。 此動作只需要在初始伺服器註冊時執行。 您可以在註冊伺服器後重新啟用它。

# <a name="portal"></a>[入口網站](#tab/azure-portal)
> [!Note]  
> 如果您要在 Windows Server Core 上部署 Azure 檔案同步，可以略過此步驟。

1. 開啟 [伺服器管理員]。
2. 按一下 [本機伺服器]****：  
    ![位於伺服器管理員 UI 左側的 [本機伺服器]](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
3. 選取 [屬性]**** 子窗格上的 [IE 增強式安全性設定]**** 連結。  
    ![位於伺服器管理員 UI 中的 [IE 增強式安全性設定] 窗格](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
4. 在 [ **Internet Explorer 增強式安全性**設定] 對話方塊中，針對 [系統**管理員**和**使用者**] 選取 [**關閉**]：  
    ![已選取 [關閉] 的 [Internet Explorer 增強式安全性設定] 快顯視窗](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
若要停用 [Internet Explorer 增強式安全性設定]，請從提升權限的 PowerShell 工作階段中執行下列命令：

```powershell
$installType = (Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\").InstallationType

# This step is not required for Server Core
if ($installType -ne "Server Core") {
    # Disable Internet Explorer Enhanced Security Configuration 
    # for Administrators
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A7-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force
    
    # Disable Internet Explorer Enhanced Security Configuration 
    # for Users
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A8-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force
    
    # Force Internet Explorer closed, if open. This is required to fully apply the setting.
    # Save any work you have open in the IE browser. This will not affect other browsers,
    # including Microsoft Edge.
    Stop-Process -Name iexplore -ErrorAction SilentlyContinue
}
``` 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

遵循 Azure 入口網站或 PowerShell 的指示。

---

## <a name="deploy-the-storage-sync-service"></a>部署儲存體同步服務 
部署 Azure 檔案同步的第一步，先將**儲存體同步服務**資源放到所選訂用帳戶的資源群組中。 建議您佈建需要的少數資源群組即可。 您將在伺服器與此資源之間建立信任關係，而一個伺服器只能註冊到一個儲存體同步服務。 因此，建議您根據需要部署多個儲存體同步服務，以分隔伺服器群組。 請記住，不同儲存體同步服務中的伺服器不可以彼此同步。

> [!Note]
> 儲存體同步服務會從已部署的訂用帳戶和資源群組繼承存取權限。 我們建議您仔細檢查誰可以存取此服務。 具有寫入存取權的實體可以開始將新的一組檔案從已註冊伺服器同步到此儲存體同步服務，並讓資料流向實體可存取的 Azure 儲存體。

# <a name="portal"></a>[入口網站](#tab/azure-portal)
若要部署儲存體同步服務，請移至 [Azure 入口網站](https://portal.azure.com/)，按一下 [ *建立資源* ]，然後搜尋 Azure 檔案同步。在搜尋結果中，選取 [ **Azure 檔案同步**]，然後選取 [ **建立** ] 以開啟 [ **部署儲存體同步** 處理] 索引標籤。

在開啟的窗格中，輸入下列資訊：

- **名稱**：儲存體同步服務的每個區域)  (唯一名稱。
- **訂用帳戶**：您要在其中建立儲存體同步服務的訂用帳戶。 視您組織的設定策略而定，您可能具有一或多個訂用帳戶的存取權限。 Azure 訂用帳戶是針對每個雲端服務 (例如 Azure 檔案服務) 計費的最基本容器。
- **資源群組**：資源群組是 Azure 資源的邏輯群組，例如儲存體帳戶或儲存體同步服務。 您可以建立新的資源群組，或使用現有的資源群組進行 Azure 檔案同步。 (我們建議使用資源群組作為容器，以邏輯方式為您的組織隔離資源，例如將 HR 資源或特定專案的資源分組。 ) 
- **位置**：您想要在其中部署 Azure 檔案同步的區域。這份清單只提供支援的區域。

完成時，請選取 [建立]**** 來部署儲存體同步服務。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
將 `<Az_Region>` 、 `<RG_Name>` 和取代 `<my_storage_sync_service>` 為您自己的值，然後使用下列命令來建立及部署儲存體同步服務：

```powershell
$hostType = (Get-Host).Name

if ($installType -eq "Server Core" -or $hostType -eq "ServerRemoteHost") {
    Connect-AzAccount -UseDeviceAuthentication
}
else {
    Connect-AzAccount
}

# this variable holds the Azure region you want to deploy 
# Azure File Sync into
$region = '<Az_Region>'

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = @()
Get-AzLocation | ForEach-Object { 
    if ($_.Providers -contains "Microsoft.StorageSync") { 
        $regions += $_.Location 
    } 
}

if ($regions -notcontains $region) {
    throw [System.Exception]::new("Azure File Sync is either not available in the selected Azure Region or the region is mistyped.")
}

# the resource group to deploy the Storage Sync Service into
$resourceGroup = '<RG_Name>'

# Check to ensure resource group exists and create it if doesn't
$resourceGroups = @()
Get-AzResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    New-AzResourceGroup -Name $resourceGroup -Location $region
}

$storageSyncName = "<my_storage_sync_service>"
$storageSync = New-AzStorageSyncService -ResourceGroupName $resourceGroup -Name $storageSyncName -Location $region
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

遵循 Azure 入口網站或 PowerShell 的指示。

---

## <a name="install-the-azure-file-sync-agent"></a>安裝 Azure 檔案同步代理程式
Azure 檔案同步代理程式是可下載的套件，可讓 Windows Server 能夠和 Azure 檔案共用進行同步處理。 

# <a name="portal"></a>[入口網站](#tab/azure-portal)
您可以從 [Microsoft 下載中心](https://go.microsoft.com/fwlink/?linkid=858257) \(英文\) 下載該代理程式。 下載之後，請按兩下 MSI 套件以啟動 Azure 檔案同步代理程式安裝。

> [!Important]  
> 如果您打算將 Azure 檔案同步搭配容錯移轉叢集使用，則必須在叢集中的每個節點上安裝 Azure 檔案同步代理程式。 叢集中的每個節點都必須註冊，才能搭配 Azure 檔案同步使用。

建議您採取下列動作︰
- 保留預設的安裝路徑 (C:\Program Files\Azure\StorageSyncAgent)，以簡化疑難排解和伺服器維護。
- 啟用 Microsoft Update 以將 Azure 檔案同步保持在最新狀態。 Azure 檔案同步代理程式的所有更新 (包括功能更新和 Hotfix) 都會從 Microsoft Update 進行。 建議您將最新的更新安裝至 Azure 檔案同步。如需詳細資訊，請參閱 [Azure 檔案同步更新原則](storage-sync-files-planning.md#azure-file-sync-agent-update-policy)。

當 Azure 檔案同步代理程式安裝結束時，伺服器註冊 UI 會自動開啟。 您必須先有儲存體同步服務才能註冊；請參閱下一節以了解如何建立儲存體同步服務。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
執行下列 PowerShell 程式碼，下載適合您作業系統的 Azure 檔案同步代理程式版本，並將其安裝在您的系統上。

> [!Important]  
> 如果您打算將 Azure 檔案同步搭配容錯移轉叢集使用，則必須在叢集中的每個節點上安裝 Azure 檔案同步代理程式。 叢集中的每個節點都必須經過註冊，才能使用 Azure 檔案同步。

```powershell
# Gather the OS version
$osver = [System.Environment]::OSVersion.Version

# Download the appropriate version of the Azure File Sync agent for your OS.
if ($osver.Equals([System.Version]::new(10, 0, 17763, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2019 `
        -OutFile "StorageSyncAgent.msi" 
} elseif ($osver.Equals([System.Version]::new(10, 0, 14393, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2016 `
        -OutFile "StorageSyncAgent.msi" 
} elseif ($osver.Equals([System.Version]::new(6, 3, 9600, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2012R2 `
        -OutFile "StorageSyncAgent.msi" 
} else {
    throw [System.PlatformNotSupportedException]::new("Azure File Sync is only supported on Windows Server 2012 R2, Windows Server 2016, and Windows Server 2019")
}

# Install the MSI. Start-Process is used to PowerShell blocks until the operation is complete.
# Note that the installer currently forces all PowerShell sessions closed - this is a known issue.
Start-Process -FilePath "StorageSyncAgent.msi" -ArgumentList "/quiet" -Wait

# Note that this cmdlet will need to be run in a new session based on the above comment.
# You may remove the temp folder containing the MSI and the EXE installer
Remove-Item -Path ".\StorageSyncAgent.msi" -Recurse -Force
```
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

遵循 Azure 入口網站或 PowerShell 的指示。

---

## <a name="register-windows-server-with-storage-sync-service"></a>向儲存體同步服務註冊 Windows Server
向儲存體同步服務註冊 Windows Server 會在您的伺服器 (或叢集) 與儲存體同步服務之間建立信任關係。 一部伺服器只能向一個儲存體同步服務註冊，但可以與其他關聯至相同儲存體同步服務的伺服器和 Azure 檔案共用同步。

> [!Note]
> 伺服器註冊會使用您的 Azure 認證來建立儲存體同步服務和 Windows Server 之間的信任關係；但是，之後伺服器會建立並使用自有的身分識別，只要伺服器維持註冊狀態，且目前的共用存取簽章權杖 (儲存體 SAS) 有效，此身分識別就會是有效的。 如果伺服器未註冊，新的 SAS 權杖就無法發行至伺服器，並會因此移除伺服器存取您 Azure 檔案共用的能力，進而停止任何同步。

註冊伺服器的系統管理員必須是指定儲存體同步服務之管理角色 **擁有** 者或 **參與者** 的成員。 這可以在儲存體同步服務的 Azure 入口網站中，于 [存取控制] 下設定 ** (IAM) ** 。

您也可以區分系統管理員從允許的伺服器註冊伺服器，也可以在儲存體同步服務中設定同步。 如此一來，您就必須建立自訂角色，以列出只允許註冊伺服器的系統管理員，並授與您的自訂角色下列許可權：

* "Microsoft.storagesync/storageSyncServices/registeredServers/write"
* "Microsoft.storagesync/storageSyncServices/read"
* "Microsoft.storagesync/storageSyncServices/workflow/read"
* "Microsoft.storagesync/storageSyncServices/workflow/operations/read"

# <a name="portal"></a>[入口網站](#tab/azure-portal)
安裝 Azure 檔案同步代理程式之後，伺服器註冊 UI 應該會自動開啟。 如果沒有，您可以從其檔案位置手動開啟它：C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe。 伺服器註冊 UI 開啟時，請選取 [登入]**** 以開始。

登入之後，系統會提示您輸入下列資訊：

![伺服器註冊 UI 的螢幕擷取畫面](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Azure 訂用帳戶**：包含儲存體同步服務的訂用帳戶 (請參閱[部署儲存體同步服務](#deploy-the-storage-sync-service))。 
- **資源群組**：包含儲存體同步服務的資源群組。
- **儲存體同步服務**：您要註冊的目標儲存體同步服務名稱。

選取適當的資訊之後，請選取 [註冊]**** 以完成伺服器註冊。 在註冊過程中，系統會提示您額外再登入一次。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
$registeredServer = Register-AzStorageSyncServer -ParentObject $storageSync
```
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

遵循 Azure 入口網站或 PowerShell 的指示。

---

## <a name="create-a-sync-group-and-a-cloud-endpoint"></a>建立同步群組和雲端端點
同步群組定義一組檔案的同步拓撲。 同步群組內的端點會與彼此保持同步。 同步群組必須包含一個雲端端點，代表 Azure 檔案共用和一個或多個伺服器端點。 伺服器端點代表已註冊之伺服器上的路徑。 伺服器可以在多個同步群組中有多個伺服器端點。 您可以根據需要建立多個同步群組，以適當地描述您想要的同步拓撲。

雲端端點是指向 Azure 檔案共用的指標。 所有伺服器端點都會與雲端端點同步，使雲端端點成為中樞。 Azure 檔案共用的儲存體帳戶必須位於儲存體同步服務的所在區域。 Azure 檔案共用的全部內容都會同步，但有一個例外狀況：系統會佈建一個特殊資料夾，其相當於 NTFS 磁碟區上隱藏的「系統磁碟區資訊」資料夾。 此目錄稱為 ".SystemShareInformation"。 它包含重要的同步中繼資料，而這不會同步到其他端點。 請勿使用或刪除此資料夾！

> [!Important]  
> 您可以對同步群組中的任何雲端端點或伺服器端點進行變更，您的檔案將會與同步群組中的其他端點同步。 如果直接對雲端端點 (Azure 檔案共用) 進行變更，則必須先由 Azure 檔案同步變更偵測作業探索到該變更。 針對雲端端點的變更偵測作業，每隔 24 小時才會起始一次。 如需詳細資訊，請參閱 [Azure 檔案服務常見問題集](storage-files-faq.md#afs-change-detection)。

建立雲端端點的系統管理員必須是儲存體帳戶的管理角色 **擁有** 者的成員，而該儲存體帳戶包含雲端端點所指向的 Azure 檔案共用。 這可以在儲存體帳戶 Azure 入口網站的 [存取控制] 下設定 ** (IAM) ** 。

# <a name="portal"></a>[入口網站](#tab/azure-portal)
若要建立同步群組，請在 [Azure 入口網站](https://portal.azure.com/)中，移至您的儲存體同步服務，然後選取 [ **+ 同步群組**：

![在 Azure 入口網站中建立新的同步群組](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

在開啟的窗格中，輸入下列資訊以建立具有雲端端點的同步群組：

- **同步組名**：要建立的同步組名。 此名稱在儲存體同步服務中必須是唯一的，但可以是任何對您而言合理的名稱。
- **訂用帳戶**：您在[部署儲存體同步服務](#deploy-the-storage-sync-service)中部署儲存體同步服務的訂用帳戶。
- **儲存體帳戶**：如果您選取 [選取儲存體帳戶]****，則會顯示另一個窗格，您可在其中選取具有您想要同步之 Azure 檔案共用的儲存體帳戶。
- **Azure 檔案共用**：您要同步處理的 Azure 檔案共用名稱。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
若要建立同步群組，請執行下列 PowerShell。 請記得以所需的同步群組名稱取代 `<my-sync-group>`。

```powershell
$syncGroupName = "<my-sync-group>"
$syncGroup = New-AzStorageSyncGroup -ParentObject $storageSync -Name $syncGroupName
```

一旦成功建立同步群組之後，您就可以建立雲端端點。 請務必將 `<my-storage-account>` 和 `<my-file-share>` 取代為預期的值。

```powershell
# Get or create a storage account with desired name
$storageAccountName = "<my-storage-account>"
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup | Where-Object {
    $_.StorageAccountName -eq $storageAccountName
}

if ($storageAccount -eq $null) {
    $storageAccount = New-AzStorageAccount `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup `
        -Location $region `
        -SkuName Standard_LRS `
        -Kind StorageV2 `
        -EnableHttpsTrafficOnly:$true
}

# Get or create an Azure file share within the desired storage account
$fileShareName = "<my-file-share>"
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $fileShareName -and $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    $fileShare = New-AzStorageShare -Context $storageAccount.Context -Name $fileShareName
}

# Create the cloud endpoint
New-AzStorageSyncCloudEndpoint `
    -Name $fileShare.Name `
    -ParentObject $syncGroup `
    -StorageAccountResourceId $storageAccount.Id `
    -AzureFileShareName $fileShare.Name
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用 [az microsoft.storagesync sync-group](/cli/azure/ext/storagesync/storagesync/sync-group#ext-storagesync-az-storagesync-sync-group-create) 命令來建立新的同步群組。  若要為所有 CLI 命令預設的資源群組，請使用 [az configure](/cli/azure/reference-index#az-configure)。

```azurecli
az storagesync sync-group create --resource-group myResourceGroupName \
                                 --name myNewSyncGroupName \
                                 --storage-sync-service myStorageSyncServiceName \
```

使用 [az microsoft.storagesync sync-group cloud-endpoint](/cli/azure/ext/storagesync/storagesync/sync-group/cloud-endpoint#ext-storagesync-az-storagesync-sync-group-cloud-endpoint-create) 命令來建立新的雲端端點。

```azurecli
az storagesync sync-group cloud-endpoint create --resource-group myResourceGroup \
                                                --storage-sync-service myStorageSyncServiceName \
                                                --sync-group-name mySyncGroupName \
                                                --name myNewCloudEndpointName \
                                                --storage-account mystorageaccountname \
                                                --azure-file-share-name azure-file-share-name
```

---

## <a name="create-a-server-endpoint"></a>建立伺服器端點
伺服器端點代表已註冊伺服器上的特定位置，例如伺服器磁碟區上的資料夾。 伺服器端點必須是已註冊伺服器 (不是裝載共用) 上的路徑，而且若要使用雲端階層，此路徑必須位在非系統磁碟區上。 不支援網路連接儲存裝置 (NAS)。

> [!NOTE]
> 不支援在磁片區上建立伺服器端點之後變更路徑或磁碟機號。 請確定您在已註冊的伺服器上使用最終路徑。

# <a name="portal"></a>[入口網站](#tab/azure-portal)
若要新增伺服器端點，請移至新建立的同步群組，然後選取 [新增伺服器端點]****。

![在 [同步群組] 窗格中新增新的伺服器端點](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

在 [新增伺服器端點]**** 窗格中，輸入下列資訊以建立伺服器端點：

- **已註冊的伺服器**：您要建立伺服器端點的伺服器或叢集名稱。
- **路徑**：要同步處理為同步群組一部分的 Windows Server 路徑。
- **雲端階層處理**：啟用或停用雲端階層處理的開關。 透過雲端階層處理，可將不常使用或存取的檔案分層處理至 Azure 檔案服務。
- **磁碟區可用空間**：在伺服器端點所在磁碟區上要保留的可用空間數量。 例如，如果具有單一伺服器端點之磁碟區上的磁碟區可用空間是設定為 50%，則大約有一半的資料量會分層處理至 Azure 檔案服務。 無論雲端階層處理是否啟用，您的 Azure 檔案共用在同步群組中一律會有完整的資料複本。

若要新增伺服器端點，請選取 [建立]****。 您的檔案現在會在 Azure 檔案共用和 Windows Server 之間保持同步。 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
執行下列 PowerShell 命令以建立伺服器端點，並請務必將取代為所 `<your-server-endpoint-path>` `<your-volume-free-space>` 需的值，並檢查選用的初始下載原則的選擇性設定。

```powershell
$serverEndpointPath = "<your-server-endpoint-path>"
$cloudTieringDesired = $true
$volumeFreeSpacePercentage = <your-volume-free-space>

if ($cloudTieringDesired) {
    # Ensure endpoint path is not the system volume
    $directoryRoot = [System.IO.Directory]::GetDirectoryRoot($serverEndpointPath)
    $osVolume = "$($env:SystemDrive)\"
    if ($directoryRoot -eq $osVolume) {
        throw [System.Exception]::new("Cloud tiering cannot be enabled on the system volume")
    }

    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath `
        -CloudTiering `
        -VolumeFreeSpacePercent $volumeFreeSpacePercentage
} else {
    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath
}
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用 [az microsoft.storagesync sync-group server-endpoint](/cli/azure/ext/storagesync/storagesync/sync-group/server-endpoint#ext-storagesync-az-storagesync-sync-group-server-endpoint-create) 命令來建立新的伺服器端點。

```azurecli
# Create a new sync group server endpoint 
az storagesync sync-group server-endpoint create --resource-group myResourceGroupName \
                                                 --name myNewServerEndpointName
                                                 --registered-server-id 91beed22-7e9e-4bda-9313-fec96c286e0
                                                 --server-local-path d:\myPath
                                                 --storage-sync-service myStorageSyncServiceNAme
                                                 --sync-group-name mySyncGroupName

# Create a new sync group server endpoint with additional optional parameters
az storagesync sync-group server-endpoint create --resource-group myResourceGroupName \
                                                 --storage-sync-service myStorageSyncServiceName \
                                                 --sync-group-name mySyncGroupName \
                                                 --name myNewServerEndpointName \
                                                 --registered-server-id 91beed22-7e9e-4bda-9313-fec96c286e0 \
                                                 --server-local-path d:\myPath \
                                                 --cloud-tiering on \
                                                 --volume-free-space-percent 85 \
                                                 --tier-files-older-than-days 15 \
                                                 --offline-data-transfer on \
                                                 --offline-data-transfer-share-name myfilesharename \

```

---

## <a name="configure-firewall-and-virtual-network-settings"></a>設定防火牆和虛擬網路設定

### <a name="portal"></a>入口網站
如果您想要設定 Azure 檔案同步來使用防火牆和虛擬網路設定，請執行下列動作：

1. 從 Azure 入口網站中，流覽至您要保護的儲存體帳戶。
1. 選取左側功能表上的 [ **防火牆和虛擬網路** ] 按鈕。
1. 選取 [**允許存取來源**] 下的 [選取的**網路**]。
1. 請確定您的伺服器 IP 或虛擬網路列在適當的區段底下。
1. 請確定已核取 [ **允許信任的 Microsoft 服務存取此儲存體帳戶** ]。
1. 選取 [儲存] 以儲存您的設定。

![設定防火牆和虛擬網路設定以搭配 Azure 檔案同步使用](media/storage-sync-files-deployment-guide/firewall-and-vnet.png)

## <a name="onboarding-with-azure-file-sync"></a>透過 Azure 檔案同步上架
第一次於 Azure 檔案同步上架時，如果不想要停機，同時還要保留完整檔案忠實性和存取控制清單 (ACL)，其建議步驟如下所示：
 
1. 部署儲存體同步服務。
1. 建立同步群組。
1. 在具有完整資料集的伺服器上安裝 Azure 檔案同步代理程式。
1. 註冊該伺服器，並在共用上建立伺服器端點。 
1. 讓同步服務將所有資料完整上傳至 Azure 檔案共用 (雲端端點)。  
1. 完成初始上傳之後，在其餘的各個伺服器上安裝 Azure 檔案同步代理程式。
1. 在其餘的各個伺服器上建立新的檔案共用。
1. 如有需要，請使用雲端階層處理原則，在新的檔案共用上建立伺服器端點。 (此步驟需有額外的儲存體以進行初始設定。)
1. 讓 Azure 檔案同步代理程式快速還原完整的命名空間，而不需要實際的資料傳輸。 在完整命名空間同步之後，同步引擎將會根據伺服器端點的雲端階層處理原則，填滿本機磁碟空間。 
1. 確定同步完成，並視需要測試拓撲。 
1. 將使用者和應用程式重新導向到這個新共用。
1. 您可以選擇性地刪除伺服器上任何重複的共用。
 
如果您沒有額外儲存體可供初始上架，而想要連結至現有的共用，您可以在 Azure 檔案共用中預先植入資料。 唯有您可接受停機時間，且絕對保證在初始上架程序期間，伺服器共用上的資料不會變更，才建議使用此方法。 
 
1. 確定在上架程式期間，任何伺服器上的資料都不會變更。
1. 使用任何資料傳輸工具透過 SMB 預先植入 Azure 檔案共用與伺服器資料。 例如 Robocopy。 您也可以使用 AzCopy over REST。 請務必使用 AzCopy 搭配適當的參數，以保留 Acl 時間戳記和屬性。
1. 使用指向現有共用的所需伺服器端點，建立 Azure 檔案同步拓撲。
1. 讓同步服務在所有端點上完成調整程序。 
1. 完成調整後，您可以開啟共用進行變更。
 
目前，預先植入方法有幾個限制- 
- 在同步拓撲完全啟動並執行之前，伺服器上的資料變更會導致伺服器端點發生衝突。  
- 建立雲端端點之後，Azure 檔案同步會在開始初始同步處理之前，先執行程式來偵測雲端中的檔案。完成此程式所需的時間會視各種因素而異，例如網路速度、可用的頻寬，以及檔案和資料夾的數目。 根據預覽版本中的粗略估計，偵測程序會以大約每秒 10 個檔案的速度執行。因此，即使預先植入的執行速度快，但若在雲端預先植入資料，則系統完整執行的整體時間可能會更長。

## <a name="self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service"></a>透過舊版和 VSS (磁碟區陰影複製服務) 的自助式還原

> [!IMPORTANT]
> 下列資訊僅適用于第9版 (或以上的儲存體同步代理程式) 。 低於9的版本將不會有 StorageSyncSelfService Cmdlet。

先前的版本是一項 Windows 功能，可讓您利用磁片區的伺服器端 VSS 快照集，將可還原的檔案版本呈現給 SMB 用戶端。
這項功能可讓您直接針對資訊工作者（而不是根據 IT 系統管理員的還原），提供強大的案例，通常稱為自助還原。

VSS 快照集和先前的版本可獨立于 Azure 檔案同步之外運作。不過，雲端階層處理必須設定為相容模式。 許多 Azure 檔案同步的伺服器端點都可以存在於相同的磁片區上。 您必須針對每個磁片區進行下列 PowerShell 呼叫，而您打算或使用雲端階層處理的伺服器端點甚至是一個伺服器端點。

```powershell
Import-Module '<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll'
Enable-StorageSyncSelfServiceRestore [-DriveLetter] <string> [[-Force]] 
```

VSS 快照集會採用整個磁片區。 根據預設，指定的磁片區最多可有64個快照集，並授與足夠的空間來儲存快照集。 VSS 會自動處理這種情況。 預設快照集排程會從星期一到星期五的每天取得兩個快照集。 該排程可透過 Windows 排程工作來設定。 上述 PowerShell Cmdlet 會執行兩項作業：
1. 它會在指定的磁片區上設定 Azure 檔案同步雲端階層處理以與舊版相容，並保證可以從舊版還原檔案，即使該檔案已分層到伺服器上的雲端也一樣。 
1. 它會啟用預設 VSS 排程。 然後，您可以決定稍後再進行修改。 

> [!Note]  
> 有兩個重點值得注意：
>- 如果您使用-Force 參數，且目前已啟用 VSS，則會覆寫目前的 VSS 快照集排程，並將其取代為預設排程。 執行 Cmdlet 之前，請務必先儲存您的自訂設定。
> - 如果您在叢集節點上使用此 Cmdlet，您也必須在叢集中的所有其他節點上執行它！ 

為了查看是否已啟用自助式還原相容性，您可以執行下列 Cmdlet。

```powershell
Get-StorageSyncSelfServiceRestore [[-Driveletter] <string>]
```

它會列出伺服器上的所有磁片區，以及每個磁片區的雲端階層處理相容日數目。 系統會根據每個磁片區的最大可能快照集和預設快照集排程，自動計算這個數位。 因此，根據預設，呈現給資訊工作者的所有舊版本都可以用來從還原。 如果您變更預設排程來取得更多快照集，也是如此。
但是，如果您變更排程的方式會導致磁片區上的可用快照集早于相容的日期值，則使用者將無法使用舊版快照 (舊版) 還原。

> [!Note]
> 啟用自助式還原可能會影響您的 Azure 儲存體耗用量和帳單。 這項影響僅限於伺服器上目前階層式檔案。 啟用這項功能可確保雲端中有可用的檔案版本，可透過舊版 (VSS 快照集) 專案參考。
>
> 如果您停用此功能，Azure 儲存體耗用量將會變慢，直到 [相容的天數] 視窗經過為止。 沒有辦法加速這項功能。 

預設的 VSS 快照集數目上限為每個磁片區 (64) ，以及要取得的預設排程，最多會導致資訊工作者可從中還原的舊版45天，取決於您可以在磁片區上儲存的 VSS 快照集數目。

如果每個磁片區的最大 64 VSS 快照集不是正確的設定，您可以透過登錄機 [碼來變更](https://docs.microsoft.com/windows/win32/backup/registry-keys-for-backup-and-restore#maxshadowcopies)該值。
若要讓新的限制生效，您必須重新執行 Cmdlet，以在先前啟用的每個磁片區上啟用先前的版本相容性，並使用-Force 旗標，將每個磁片區的最大 VSS 快照集數目帶到帳戶。 這會導致新計算的相容天數。 請注意，這項變更只會在新階層的檔案上生效，並覆寫您可能進行的 VSS 排程上的任何自訂。

## <a name="migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync"></a>將 DFS 複寫 (DFS-R) 部署移轉至 Azure 檔案同步
若要將 DFS-R 部署移轉至 Azure 檔案同步處理：

1. 建立同步群組來代表要取代的 DFS-R 拓樸。
2. 在要移轉 DFS R 拓撲中，於有一組完整資料的伺服器上啟動。 在該伺服器上安裝 Azure 檔案同步。
3. 註冊該伺服器，並為要移轉的第一個伺服器建立伺服器端點。 請勿啟用雲端階層。
4. 將所有資料同步至 Azure 檔案共用 (雲端端點)。
5. 在其餘的各個 DFS-R 伺服器上安裝並註冊 Azure 檔案同步代理程式。
6. 停用 DFS-R。 
7. 在每一個 DFS-R 伺服器上建立伺服器端點。 請勿啟用雲端階層。
8. 確定同步完成，並視需要測試拓撲。
9. 淘汰 DFS-R。
10. 現在可在所需的任何伺服器端點上啟用雲端階層。

如需詳細資訊，請參閱[Azure 檔案同步與分散式檔案系統 (DFS) 互通](storage-sync-files-planning.md#distributed-file-system-dfs)。

## <a name="next-steps"></a>後續步驟
- [新增或移除 Azure 檔案同步伺服器端點](storage-sync-files-server-endpoint.md)
- [向 Azure 檔案同步註冊或取消註冊伺服器](storage-sync-files-server-registration.md)
- [監視 Azure 檔案同步](storage-sync-files-monitoring.md)