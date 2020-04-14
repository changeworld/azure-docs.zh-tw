---
title: 建立 Azure 虛擬機器技術資產
description: 瞭解如何為 Azure 應用商店創建和配置虛擬機 (VM) 產品/服務的技術資產。
author: dannyevers
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: eb885d0e965579b1ab2d66395f9f96eab0845bae
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266496"
---
# <a name="create-your-azure-virtual-machine-technical-assets"></a>建立 Azure 虛擬機器技術資產

> [!IMPORTANT]
> 我們將 Azure 虛擬機器產品的管理從雲合作夥伴門戶遷移到合作夥伴中心。 在遷移產品/服務之前,請按照[雲合作夥伴門戶的虛擬機產品/服務創建技術資產](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-technical-assets)以管理您的產品/服務的說明進行操作。

本文介紹如何為 Azure 應用商店創建和配置虛擬機 (VM) 產品/服務的技術資產。 VM 包含兩個元件:作業系統虛擬硬碟 (VHD) 和可選關聯資料磁碟 VHD:

* **操作系統 VHD** – 包含隨產品/服務部署的作業系統和解決方案。 準備 VHD 的過程因它是基於 Linux 的、基於 Windows 的還是基於自訂的 VM 而異。
* **數據磁碟 VHD** - VM 專用的持久存儲。 不要使用作業系統 VHD(例如,C: 驅動器)來存儲持久性資訊。

VM 映射包含一個作業系統磁碟和最多 16 個數據磁碟。 即使磁碟為空,也使用每個數據磁碟的一個 VHD。

> [!NOTE]
> 無論您使用哪種作業系統,都只添加解決方案所需的最小數據磁碟數。 客戶無法在部署時刪除屬於映射的磁碟,但始終可以在部署期間或部署後添加磁碟。

> [!IMPORTANT]
> 計劃中的每個 VM 映像必須具有相同的數據磁碟數。

## <a name="fundamental-technical-knowledge"></a>基本技術知識

設計、構建和測試這些資產需要時間,需要對 Azure 平臺和用於構建產品/服務的技術進行技術知識。 除了解決方案領域之外,您的工程團隊還應瞭解以下 Microsoft 技術:

* 對於 [Azure 服務](https://azure.microsoft.com/services/)的基本了解
* 如何[設計和架構 Azure 應用程式](https://azure.microsoft.com/solutions/architecture/)
* 具備 [Azure 虛擬機器](https://azure.microsoft.com/services/virtual-machines/)、[Azure 儲存體](https://azure.microsoft.com/services/?filter=storage)和 [Azure 網路](https://azure.microsoft.com/services/?filter=networking)的運用知識
* 具備 [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) 的運用知識
* 具備 [JSON](https://www.json.org/) 的運用知識

## <a name="suggested-tools--optional"></a>建議的工具 = 選擇

請考慮使用以下文稿環境之一來説明管理 VM 和 VHD:

* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
* [Azure CLI](https://code.visualstudio.com/)

此外,請考慮將以下工具新增到開發環境:

* [Azure 儲存體總管](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Visual Studio Code](https://code.visualstudio.com/)
  * 延伸模組：[Azure Resource Manager 工具](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  * 延伸模組：[Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  * 延伸模組：[Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

檢視[Azure 開發人員工具](https://azure.microsoft.com/product-categories/developer-tools/)頁中的可用工具,如果使用 Visual Studio,則檢視[視覺化工作室應用商店](https://marketplace.visualstudio.com/)。

## <a name="create-a-vm-image-using-an-approved-base"></a>使用已批准的基建立 VM 映像

> [!NOTE]
> 要使用在自己內部構建的映射創建虛擬機器技術資產,請轉到[使用自己的映射創建 VM。](#create-a-vm-using-your-own-image)

本節介紹使用已批准的基座的各個方面,例如使用遠端桌面協定 (RDP)、為 VM 選擇大小、安裝最新的 Windows 更新以及通用 VHD 映射。

以下各節主要側重於基於視窗的 VHD。 有關創建基於 Linux 的 VHD 的詳細資訊,請參閱[Azure 認可的發行版上的 Linux。](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)

> [!WARNING]
> 按照本主題中的指南,使用 Azure 創建包含預配置、認可的作業系統的 VM。 如果這與解決方案不相容,則可以使用經批准的作業系統創建和配置本地 VM。 接著您可以依照[準備 Windows VHD 或 VHDX 以上傳至 Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image) 中所述，設定並準備上傳。

### <a name="select-an-approved-base"></a>選擇已核准的基礎映像

選擇 Windows 作業系統或 Linux 作為基礎。

#### <a name="windows"></a>Windows

基於 Windows 的 VM 映射的作業系統 VHD 必須基於包含 Windows 伺服器或 SQL Server 的 Azure 批准的基本映射。 首先,從 Azure 門戶中的以下映射之一創建 VM:

* Windows Server ([2016](https://www.microsoft.com/evalcenter/evaluate-windows-server-2016)、[2012 R2 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)、[2012 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)、[2008 R2 SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview))
* [SQL 伺服器 2014](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (企業, 標準, Web)
* [SQL 伺服器 2012 SP2(](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance)企業、標準、Web)

> [!NOTE]
> 如果您使用的是當前 Azure 門戶或 Azure PowerShell,則 2014 年 9 月 8 日及以後發布的 Windows 伺服器映像將獲得批准。

#### <a name="linux"></a>Linux

Azure 提供了一系列已批准的 Linux 發行版。 如需目前的清單，請參閱[經 Azure 背書的 Linux 發佈](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)。

### <a name="create-vm-in-the-azure-portal"></a>在 Microsoft Azure 入口網站網站中建立 VM

依以下步驟在[Azure 門戶](https://ms.portal.azure.com/)中建立基本 VM 映射:

1. 使用與要用於發佈 VM 產品/服務的 Azure 訂閱關聯的 Microsoft 帳戶登入[Azure 門戶](https://ms.portal.azure.com/)。
2. 建立新的資源群組，並提供您的**資源群組名稱**、**訂用帳戶**以及**資源群組位置**。 有關詳細資訊,請參閱[管理資源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)。
3. 選擇左側的**虛擬機**以顯示虛擬機詳細資訊頁。
4. 選擇 **+ 新增**以開啟**虛擬機器體驗**。
5. 從下拉清單中選擇圖像或按下 **「瀏覽所有公共和私有映射**」以搜尋或瀏覽所有可用的虛擬機器映射。
6. 選取要使用下列建議部署的 VM 大小：
    * 如果您計劃在本地開發 VHD,則大小並不重要。 請考慮使用其中一個較小的 VM。
    * 如果您打算在 Azure 中開發映像，請考慮使用其中一個建議的 VM 大小做為選取的映像。

7. 在 **"磁碟'** 部分中,展開 **「進階**」部分,並將 **「使用託管磁碟」** 選項設定為 **「否**」。
8. 提供創建 VM 所需的其他詳細資訊。
9. 選擇 **「查看 + 建立」** 以檢視您的選擇。 當您看到 [驗證成功]  訊息時，請選取 [建立]  。

Azure 開始預配指定的虛擬機器。 您可以通過選擇左側的 **「虛擬機器」** 選項卡來追蹤其進度。 建立後,狀態將更改為 **「正在運行**」。

如果在創建新基於 Azure 的 VHD 時遇到困難,請參閱[VHD 創建期間的常見問題 (FAQ)。](https://aka.ms/VHDcreationIssues)

### <a name="connect-to-your-azure-vm"></a>連線至您的 Azure VM

本節介紹如何連接到在 Azure 上創建的 VM 並登錄到 VM。 成功連接後,可以使用 VM,就像您本地登錄到其主機伺服器一樣。

#### <a name="connect-to-a-windows-based-vm"></a>連線至 Windows 型 VM

使用遠端桌面用戶端連接到 Azure 上託管的基於 Windows 的 VM。 大部分的 Windows 版本原本就支援遠端桌面通訊協定 (RDP)。 對於其他作業系統,您可以在[遠端桌面用戶端](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)中找到有關用戶端的詳細資訊。

本文詳細介紹了如何使用內建 Windows RDP 支援連接到 VM:[如何連線到執行 Windows 的 Azure 虛擬機器](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon)。

> [!TIP]
> 在此過程中,您可能會收到安全警告。 例如,諸如".rdp 檔來自未知發行者"或"無法驗證您的使用者認證"等警告。 您可以放心忽略這些警告。

#### <a name="connect-to-a-linux-based-vm"></a>連線至 Linux 型 VM

要連接到基於 Linux 的 VM,您需要一個安全的 shell 協定 (SSH) 用戶端。 以下步驟使用免費[的 PuTTY](https://www.ssh.com/ssh/putty/) SHH 端子。

1. 移至 [Azure 入口網站](https://ms.portal.azure.com/)。
2. 搜尋並選擇**虛擬機器**。
3. 選擇要連接到的 VM。
4. 如果 VM 尚未運行,則啟動它。
5. 選擇 VM 的名稱以打開其 **「概述」** 頁。
6. 請注意 VM 的公共 IP 位址和 DNS 名稱(如果未設置這些值,則必須[創建網路介面](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface))。
7. 開啟 PuTTY 應用程式。
8. 在 [PuTTY 設定] 對話方塊中，輸入您 VM 的 IP 位址或 DNS 名稱。

    :::image type="content" source="media/avm-putty.png" alt-text="說明 PuTTY 終端設置。主機名或 IP 位址和埠框將突出顯示。":::

9. 選擇 **「打開**」以打開 PuTTY 終端。
10. 出現提示後,輸入 Linux VM 帳戶的帳戶名稱和密碼。

如果存在連接問題,請參閱 SSH 客戶端的文檔。 例如,[第 10 章:常見錯誤訊息](https://www.ssh.com/ssh/putty/putty-manuals)。

有關詳細資訊(包括如何將桌面添加到預配的 Linux VM),請參閱[安裝和設定遠端桌面以連接到 Azure 中的 Linux VM。](https://docs.microsoft.com/azure/virtual-machines/linux/use-remote-desktop)

## <a name="create-a-vm-using-your-own-image"></a>使用您自己的映像建立 VM

本節介紹如何創建和部署使用者提供的虛擬機 (VM) 映射。 可以通過從 Azure 部署的虛擬硬碟 (VHD) 提供作業系統和數據磁碟 VHD 映射來執行此操作。

> [!NOTE]
> 要選擇使用已批准的基本映射,請[按照使用已批准的基](#create-a-vm-image-using-an-approved-base)創建 VM 映射中的說明進行操作。

1. 將映射上載到 Azure 儲存帳戶。
2. 部署 VM 映射。
3. 捕獲 VM 映射。

### <a name="upload-your-images-to-an-azure-storage-account"></a>將映像上傳到 Azure 儲存帳戶

1. 登入 Azure[門戶](https://portal.azure.com/)。
2. 將通用作業系統 VHD 和數據磁碟 VHD 上傳到 Azure 儲存帳戶。

### <a name="deploy-your-image"></a>部署映像

使用 Azure 門戶或 Azure PowerShell 創建映射。

#### <a name="deploy-using-the-azure-portal"></a>使用 Azure 入口網站進行部署

1. 在主頁上,選擇 **「創建資源**」,搜索「範本部署」,然後選擇「**創建**」。
2. 編輯**器中選擇"構建您自己的範本**"

    :::image type="content" source="media/avm-custom-deployment.png" alt-text="說明自定義部署頁。":::

3. 將此[JSON 範本](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-deploy-json-template)貼上到編輯器中,然後選擇 **「儲存**」 。
4. 為顯示的**自訂部署**屬性頁面提供參數值。

    | 參數 | 描述 |
    | ------------ | ------------- |
    | 儲存體帳戶名稱 | 儲存格 2 中的內容 |
    | 輸入儲存體容器名稱 | 一般化 VHD 所在的儲存體帳戶名稱 |
    | 公用 IP 的 DNS 名稱 | 公共 IP DNS 名稱。 部署產品/服務後,在 Azure 門戶中定義公共 IP 位址的 DNS 名稱。 |
    | 管理員使用者名稱 | 新 VM 系統管理員帳戶的使用者名稱 |
    | 管理員密碼 | 新 VM 管理員帳戶密碼 |
    | OS 類型 | VM 作業系統:Windows 或 Linux |
    | 訂用帳戶識別碼 | 讓選取的訂用帳戶取得安全性建議 |
    | Location | 部署的地理位置 |
    | VM 大小 | [Azure VM 大小](https://docs.microsoft.com/azure/virtual-machines/windows/sizes),例如Standard_A2 |
    | 公用 IP 位址 | 您的公用 IP 位址名稱 |
    | 虛擬機器名稱 | 新 VM 名稱 |
    | 虛擬網路名稱 | VM 使用的虛擬網路名稱 |
    | NIC 名稱 | 正在執行虛擬網路的網路介面卡名稱 |
    | VHD URL | 完整的 OS 磁碟 VHD URL |
    |  |  |

5. 提供這些值後,選擇 **「購買**」。。

Azure 將開始部署。 它在指定的儲存帳戶路徑中創建具有指定非託管 VHD 的新 VM。 您可以通過選擇門戶左側的**虛擬機**來跟蹤 Azure 門戶中的進度。 創建 VM 時,狀態將從"開始"更改為"正在運行"

#### <a name="deploy-using-azure-powershell"></a>使用 Azure PowerShell 進行部署

```powershell
    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot
```

### <a name="capture-the-vm-image"></a>擷取 VM 映像

使用與方法對應的以下說明:

* Azure PowerShell:[如何從 Azure VM 建立非託管 VM 映像](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)
* Azure CLI：[如何建立虛擬機器或 VHD 映像](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)
* API：[虛擬機器 - 擷取](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)

## <a name="configure-the-virtual-machine"></a>設定虛擬機器

本節介紹如何調整、更新和概括 Azure VM。 這些步驟對於準備要部署在 Azure 應用商店中的 VM 是必需的。

### <a name="sizing-the-vhds"></a>調整 VHD 大小

如果選擇了預先設定作業系統的 VM 之一(以及可選的附加服務),則已選擇標準 Azure VM 大小。 建議使用預先設定的作業系統啟動您的解決方案。 但是,如果要手動安裝操作系統,則必須在 VM 映射中調整主 VHD 的大小:

* 對於 Windows,作業系統 VHD 應創建為 127-128 GB 固定格式 VHD。
* 對於 Linux,此 VHD 應創建為 30-50 GB 固定格式 VHD。

如果物理大小小於 127-128 GB,則 VHD 應可擴展(稀疏/動態)。 提供的基本 Windows 和 SQL Server 映像已經滿足這些要求,因此不要更改 VHD 的格式或大小。

資料磁碟的大小可高達 1 TB。 在決定大小時,請記住,在部署時,客戶無法調整映射中的 VHD 大小。 應將資料磁碟 VHD 建立為固定格式的 VHD。 它們也應該是可擴展的(稀疏/動態)。 初始資料磁碟可為空白，也可以含有資料。

### <a name="install-the-most-current-updates"></a>安裝最新的更新

操作系統 VM 的基本映像必須包含截至其發佈日期的最新更新。 在發佈您創建的作業系統 VHD 之前,請確保使用所有最新的安全和維護修補程式更新作業系統和所有已安裝的服務。

對於 Windows 伺服器,運行 **「檢查更新」** 命令。

若為 Linux 發行版本，通常會透過命令列工具或圖形化公用程式來下載和安裝更新。 例如，Ubuntu Linux 提供了 [apt-get](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) 命令和[更新管理員](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html)工具，可用於更新作業系統。

### <a name="perform-additional-security-checks"></a>執行額外的安全性檢查

在 Azure 應用商店中維護解決方案映像的高度安全性。 以下文章提供安全設定和過程的清單,以説明您[:Azure 應用商店映像的安全建議](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images)。 其中有些是 Linux 型映像的專屬建議，但多數建議皆適用於任何 VM 映像。

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>執行自訂的設定和排程工作

如果需要其他配置,請使用在啟動時運行的計畫任務,在部署 VM 後對其進行任何最終更改。 亦請考慮下列建議：

* 如果它是一次運行任務,則任務應在成功完成後刪除自身。
* 配置不應依賴於 C 或 D 以外的驅動器,因為始終保證只有這兩個驅動器存在(驅動器 C 是作業系統磁碟,驅動器 D 是臨時本地磁碟)。

如需 Linux 自訂項目的詳細資訊，請參閱[適用於 Linux 的虛擬機器擴充功能和功能](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux)。

## <a name="generalize-the-image"></a>一般化映像

Azure Marketplace 中的所有映像通常都必須能夠重複使用。 為此,必須對作業系統 VHD 進行通用化,該操作從 VM 中刪除所有特定於實例的標識符和軟體驅動程式。

### <a name="windows"></a>Windows

Windoes 作業系統磁碟是使用 [sysprep 工具](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)來進行一般化。 如果後續要更新或重新設定作業系統，則必須重新執行 sysprep。

> [!WARNING]
> 由於更新可能會自動運行,因此在運行 sysprep 後,請關閉 VM,直到其部署。 此關機將避免後續更新對作業系統或已安裝的服務進行特定於實例的更改。 有關運行 sysprep 的詳細資訊,請參閱[概括 VHD 的步驟](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep)。

### <a name="linux"></a>Linux

以下過程將 Linux VM 概括為一個 Vm,並將其重新部署為單獨的 VM。 有關詳細資訊,請參閱[如何建立虛擬機器或 VHD 的影像](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)。 當您到達"從捕獲的圖像創建 VM"部分時,可以停止。

1. **移除 Azure Linux 代理程式**

    1. 使用 SSH 用戶端連線到 Linux VM。
    2. 在 SSH 視窗中, 輸入`sudo waagent -deprovision+user`以下指令: 。
    3. **類型 Y**繼續(您可以將 **-force**參數添加到上一個命令以避免確認步驟)。
    d. 命令完成後,鍵入**Exit**以關閉 SSH 用戶端。

2. **停止虛擬機器**

    1. 在 Azure 門戶中,選擇資源組 (RG) 並取消分配 VM。
    2. 您的 VHD 現已通用化,您可以使用此 VHD 創建新 VM。

## <a name="next-steps"></a>後續步驟

如果您在建立以 Azure 為基礎的新 VHD 時遇到困難，請參閱 [VHD 建立期間的常見問題](https://aka.ms/AzureVM_VHDCreationFAQ)。

否則就是：

* [驗證 VM 映像](https://aks.ms/CertifyVMimage)說明如何測試和提交 VM 映射以進行 Azure 應用商店認證,包括獲取*Azure 認證工具的認證測試工具*的位置以及如何使用它來驗證 VM 映射。
