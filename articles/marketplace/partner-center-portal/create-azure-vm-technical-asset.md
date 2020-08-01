---
title: 建立 Azure 虛擬機器技術資產
description: 了解如何針對 Azure Marketplace 為虛擬機器 (VM) 供應項目建立及設定技術資產。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 04/13/2020
ms.openlocfilehash: afc012329d0d9e337dfca93a88615ba7c28f1754
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87460363"
---
# <a name="create-your-azure-virtual-machine-technical-assets"></a>建立 Azure 虛擬機器技術資產

本文描述如何針對 Azure Marketplace 為虛擬機器 (VM) 供應項目建立及設定技術資產。 VM 包含兩個元件：作業系統的虛擬硬碟 (VHD)，以及選擇性的相關聯資料磁碟 VHD：

* **作業系統 VHD** – 包含隨供應項目一起部署的作業系統和解決方案。 取決於 VM 為 Linux 架構、Windows 架構或是自訂架構，準備 VHD 的程序亦有差異。
* **資料磁碟 VHD** - VM 的專用持續性儲存體。 請勿使用作業系統 VHD (例如 C: 磁碟機) 來儲存永續性資訊。

VM 映像包含一個作業系統磁碟以及多逹 16 個資料磁碟。 針對每個資料磁碟使用一個 VHD，即使磁碟是空的也一樣。

> [!NOTE]
> 無論使用哪一種作業系統，只要新增解決方案所需的最少數目資料磁碟。 客戶無法在部署時移除屬於映像一部分的磁碟，但可隨時在部署期間或之後新增磁碟。

> [!IMPORTANT]
> 方案中每個 VM 映像都必須具有相同數目的資料磁碟。

## <a name="fundamental-technical-knowledge"></a>基本技術知識

設計、建置和測試這些資產需要時間，且需要具備關於 Azure 平台與建置供應項目所用技術的技術知識。 除了解決方案領域外，工程小組還應具備下列 Microsoft 技術的知識：

* 對於 [Azure 服務](https://azure.microsoft.com/services/)的基本了解
* 如何[設計和架構 Azure 應用程式](https://azure.microsoft.com/solutions/architecture/)
* 具備 [Azure 虛擬機器](https://azure.microsoft.com/services/virtual-machines/)、[Azure 儲存體](https://azure.microsoft.com/services/?filter=storage)和 [Azure 網路](https://azure.microsoft.com/services/?filter=networking)的運用知識
* 具備 [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) 的運用知識
* 具備 [JSON](https://www.json.org/) 的運用知識

## <a name="suggested-tools--optional"></a>建議的工具 – 選擇性

請考慮使用下列其中一種指令碼環境來協助管理 VM 和 VHD：

* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/)
* [Azure CLI](https://code.visualstudio.com/)

此外，也請考慮將下列工具新增至開發環境：

* [Azure 儲存體總管](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Visual Studio Code](https://code.visualstudio.com/)
  * 擴充功能：[Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) \(英文\)
  * 擴充功能：[Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify) \(英文\)
  * 擴充功能：[Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

檢閱 [Azure 開發人員工具](https://azure.microsoft.com/product-categories/developer-tools/)頁面中的可用的工具，若正在使用 Visual Studio，請檢閱 [Visual Studio Marketplace](https://marketplace.visualstudio.com/)。

## <a name="create-a-vm-image-using-an-approved-base"></a>使用已核准的基礎映像建立 VM 映像

> [!NOTE]
> 若要使用您在自己內部部署中建置的映像來建立虛擬機器技術資產，請前往[使用自己的映像建立 VM](#create-a-vm-using-your-own-image)。

本節描述使用已核准基礎映像的不同層面，例如如何使用遠端桌面通訊協定 (RDP)、選擇 VM 的大小、安裝最新的 Windows 更新，以及將 VHD 映像一般化。

下列各節主要著重在 Windows 架構 VHD。 如需建立 Linux 架構 VHD 的詳細資訊，請參閱 [Azure 背書的 Linux 散發套件](../../virtual-machines/linux/endorsed-distros.md) (機器翻譯)。

> [!WARNING]
> 請遵循本主題中的指引，以使用 Azure 來建立包含經預先設定和背書作業系統的 VM。 若此主題內容與解決方案不相容，您也可以使用已核准的作業系統來建立並設定內部部署 VM。 接著您可以依照[準備 Windows VHD 或 VHDX 以上傳至 Azure](../../virtual-machines/windows/prepare-for-upload-vhd-image.md) 中所述，設定並準備上傳。

### <a name="select-an-approved-base"></a>選擇已核准的基礎映像

選取 Windows 作業系統或 Linux 作為基礎映像。

#### <a name="windows"></a>Windows

Windows 架構 VM 映像的作業系統 VHD 必須以獲得 Azure 核准的基底映像為基礎，其包含 Windows Server 或 SQL Server。 若要開始，請從 Azure 入口網站中的下列映像選擇一個來建立 VM：

* Windows Server ([2016](https://www.microsoft.com/evalcenter/evaluate-windows-server-2016)、[2012 R2 Datacenter](https://www.microsoft.com/cloud-platform/windows-server-pricing)、[2012 Datacenter](https://www.microsoft.com/cloud-platform/windows-server-pricing)、[2008 R2 SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview))
* [Windows 10 IoT 企業版](/windows/iot-core/windows-iot-enterprise)
* [SQL Server 2014](../../virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance.md) (Enterprise, Standard, Web)
* [SQL Server 2012 SP2](../../virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance.md) (Enterprise, Standard, Web)

> [!NOTE]
> 如果您使用的是最新 Azure 入口網站或 Azure PowerShell，則 2014 年 9 月 8 日之後發行的 Windows Server 映像都會獲得核准。

#### <a name="linux"></a>Linux

Azure 會提供一組已核准的 Linux 散發套件。 如需目前的清單，請參閱[經 Azure 背書的 Linux 發佈](../../virtual-machines/linux/endorsed-distros.md)。

### <a name="create-vm-in-the-azure-portal"></a>在 Microsoft Azure 入口網站網站中建立 VM

遵循下列步驟在 [Azure 入口網站](https://ms.portal.azure.com/)中建立基礎 VM 映像：

1. 使用 Microsoft 帳戶 (其與想要用於發佈 VM 供應項目的 Azure 訂用帳戶建立關聯) 登入 [Azure入口網站](https://ms.portal.azure.com/)。
2. 建立新的資源群組，並提供您的**資源群組名稱**、**訂用帳戶**以及**資源群組位置**。 如需詳細資料，請參閱 [管理資源](../../azure-resource-manager/resource-group-portal.md) (機器翻譯)。
3. 選取左側的 [虛擬機器]，以顯示虛擬機器詳細資料頁面。
4. 選取 [+ 新增] 以開啟 [建立虛擬機器體驗]。
5. 從下拉式清單中選取映像，或按一下 [瀏覽所有公用和私人映像]，以搜尋或瀏覽所有可用的虛擬機器映像。
6. 選取要使用下列建議部署的 VM 大小：
    * 如果打算開發 VHD 內部部署，則大小不會造成影響。 請考慮使用其中一個較小的 VM。
    * 如果您打算在 Azure 中開發映像，請考慮使用其中一個建議的 VM 大小做為選取的映像。

7. 在 [磁碟] 區段中，展開 [進階] 區段，並將 [使用受控磁碟] 選項設定為 [否]。
8. 提供建立 VM 所需的其他詳細資料。
9. 選取 [檢閱 + 建立] 以檢閱選擇。 當您看到 [驗證成功] 訊息時，請選取 [建立]。

Azure 會開始佈建所指定的虛擬機器。 您可選取左側的 [虛擬機器] 索引標籤來追蹤其進度。 建立虛擬機器後，狀態會變更為 [執行中]。

如果在建立以 Azure 為基礎的新 VHD 時遇到困難，請參閱 [VHD 建立期間的常見問題 (FAQ)](common-issues-during-vhd-creation.md) (機器翻譯)。

### <a name="connect-to-your-azure-vm"></a>連線至您的 Azure VM

本節說明如何連線至在 Azure 建立的 VM 並登入其中。 成功連線後，使用 VM 的方式即等同您已在本機登入其主機伺服器。

#### <a name="connect-to-a-windows-based-vm"></a>連線至 Windows 型 VM

使用遠端桌面用戶端連線至裝載於 Azure 上的 Windows 架構 VM。 大部分的 Windows 版本原本就支援遠端桌面通訊協定 (RDP)。 如需其他作業系統的用戶端資訊，請參閱[遠端桌面用戶端](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)。

本文詳細說明如何使用內建的 Windows RDP 支援來連線至 VM：[如何連接和登入執行 Windows 的 Azure 虛擬機器](../../virtual-machines/windows/connect-logon.md) (機器翻譯)。

> [!TIP]
> 您可能會在過程中收到安全性警告。 例如，「.rdp 檔案來自未知的發行者」或「使用者的認證無法驗證」之類的警告。 您可以放心忽略這些警告。

#### <a name="connect-to-a-linux-based-vm"></a>連線至 Linux 型 VM

若要連線至 Linux 架構 VM，則需要使用安全殼層通訊協定 (SSH) 用戶端。 下列步驟會使用免費的 [PuTTY](https://www.ssh.com/ssh/putty/) SHH 終端機。

1. 移至 [Azure 入口網站](https://ms.portal.azure.com/)。
2. 搜尋並選取 [虛擬機器]。
3. 選取想要連線的目標 VM。
4. 啟動 VM (如果尚未執行)。
5. 選取 VM 的名稱，以開啟其 [概觀] 頁面。
6. 請注意 VM 的公用 IP 位址和 DNS 名稱（如果未設定這些值，您必須[建立網路介面](../../virtual-network/virtual-network-network-interface.md#create-a-network-interface)））。
7. 開啟 PuTTY 應用程式。
8. 在 [PuTTY 設定] 對話方塊中，輸入您 VM 的 IP 位址或 DNS 名稱。

    :::image type="content" source="media/avm-putty.png" alt-text="說明 PuTTY 終端機設定。[主機名稱] 或 [IP 位址] 和 [連接埠] 方塊已醒目提示。":::

9. 選取 [開啟] 以開啟 PuTTY 終端機。
10. 系統提示時，請輸入 Linux VM 帳戶的帳戶名稱和密碼。

如果有連線問題，請參閱 SSH 用戶端的文件。 例如，[Chapter 10:Common error messages](https://www.ssh.com/ssh/putty/putty-manuals) (第 10 章：常見的錯誤訊息)。

如需詳細資料，包括如何將桌面新增至佈建的 Linux VM，請參閱[在 Azure 中安裝和設定遠端桌面以連線至 Linux VM](../../virtual-machines/linux/use-remote-desktop.md) (機器翻譯)。

## <a name="create-a-vm-using-your-own-image"></a>使用自己的映像建立 VM

本節描述如何建立及部署使用者提供的虛擬機器 (VM) 映像。 若要這麼做，您可從 Azure 部署的虛擬硬碟 (VHD) 提供作業系統和資料磁碟 VHD 映像。

> [!NOTE]
> 若要選擇性地使用已核准的基礎映像，請遵循[使用已核准的基礎映像建立 VM 映像](#create-a-vm-image-using-an-approved-base)中指示。

1. 將映像上傳至 Azure 儲存體帳戶。
2. 部署 VM 映像。
3. 擷取 VM 映像。

### <a name="upload-your-images-to-an-azure-storage-account"></a>將映像上傳至 Azure 儲存體帳戶

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 將一般化作業系統 VHD 和資料磁碟 VHD 上傳至 Azure 儲存體帳戶。

### <a name="deploy-your-image"></a>部署映像

使用 Azure 入口網站或 Azure PowerShell 來建立映像。

#### <a name="deploy-using-the-azure-portal"></a>使用 Azure 入口網站進行部署

1. 在 [首頁] 頁面上，選取 [建立資源]，搜尋「範本部署」，然後選取 [建立]。
2. 選擇 [在編輯器中建置自己的範本]。

    :::image type="content" source="media/avm-custom-deployment.png" alt-text="說明 [自訂部署] 頁面。":::

3. 將此 [JSON 範本](../partner-center-portal/azure-vm-image-certification.md)貼上至編輯器，然後選取 [儲存]。
4. 為顯示的**自訂部署**屬性頁面提供參數值。

    | 參數 | 描述 |
    | ------------ | ------------- |
    | 儲存體帳戶名稱 | 資料格 2 中的內容 |
    | 輸入儲存體容器名稱 | 一般化 VHD 所在的儲存體帳戶名稱 |
    | 公用 IP 的 DNS 名稱 | 公用 IP 的 DNS 名稱。 部署供應項目後，在 Azure 入口網站中定義公用 IP 位址的 DNS 名稱。 |
    | 管理員使用者名稱 | 新 VM 系統管理員帳戶的使用者名稱 |
    | 管理員密碼 | 新 VM 管理員帳戶密碼 |
    | OS 類型 | VM 作業系統：Windows 或 Linux |
    | 訂用帳戶識別碼 | 讓選取的訂用帳戶取得安全性建議 |
    | Location | 部署的地理位置 |
    | VM 大小 | [Azure VM 大小](../../virtual-machines/windows/sizes.md) (機器翻譯)，例如 Standard_A2 |
    | 公用 IP 位址 | 您的公用 IP 位址名稱 |
    | 虛擬機器名稱 | 新 VM 名稱 |
    | 虛擬網路名稱 | VM 使用的虛擬網路名稱 |
    | NIC 名稱 | 正在執行虛擬網路的網路介面卡名稱 |
    | VHD URL | 完整的 OS 磁碟 VHD URL |
    |  |  |

5. 在提供這些值之後，請選取 [購買]。

Azure 將會開始部署。 其會在指定的儲存體帳戶路徑中，使用指定的非受控 VHD 來建立新 VM。 您可在 Azure 入口網站中選取左側的 [虛擬機器] 來追蹤進度。 建立 VM 後，狀態會從 [正在啟動] 變更為 [正在執行]。

#### <a name="deploy-using-azure-powershell"></a>使用 Azure PowerShell 進行部署

```powershell
    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot
```

### <a name="capture-the-vm-image"></a>擷取 VM 映像

使用與方法對應的下列指示：

* Azure PowerShell：[如何從 Azure VM 建立非受控 VM 映像](../../virtual-machines/windows/capture-image-resource.md)
* Azure CLI：[如何建立虛擬機器或 VHD 的映像](../../virtual-machines/linux/capture-image.md)
* API：[虛擬機器 - 擷取](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)

## <a name="configure-the-virtual-machine"></a>設定虛擬機器

本節描述如何針對 Azure VM 進行調整大小、更新及一般化。 若要準備在 Azure Marketplace 部署虛擬機器，則必須完成以下步驟。

### <a name="sizing-the-vhds"></a>調整 VHD 大小

如果選取了預先以作業系統 (以及選擇性的其他服務) 設定的某個 VM，即等同已選擇了標準的 Azure VM 大小。 建議使用預先設定的作業系統啟動您的解決方案。 不過，如果要以手動方式安裝 OS，則必須在 VM 映像中調整主要 VHD 的大小：

* 針對 Windows，應將作業系統 VHD 建立為 127-128 GB 固定格式的 VHD。
* 針對 Linux，應將此 VHD 建立為 30-50 GB 固定格式的 VHD。

如果實體大小不到 127-128 GB，則此 VHD 應該是可擴充的 (疏鬆/動態)。 所提供的基底 Windows 和 SQL Server 映像皆符合這些需求，所以請勿變更 VHD 的格式或大小。

資料磁碟的大小可高達 1 TB。 決定大小時，請記得客戶無法於部署時在映像中調整 VHD 大小。 應將資料磁碟 VHD 建立為固定格式的 VHD。 這些 VHD 也應該是可擴充的 (疏鬆/動態)。 初始資料磁碟可為空白，也可以含有資料。

### <a name="install-the-most-current-updates"></a>安裝最新的更新

作業系統 VM 的基礎映像，必須包含截至其發佈日期的最新更新。 發佈已建立的作業系統 VHD 之前，請確認已使用所有最新安全性和維護修補程式來更新 OS 和所有已安裝的服務。

針對 Windows Server，請執行 [檢查更新] 命令。

若為 Linux 發行版本，通常會透過命令列工具或圖形化公用程式來下載和安裝更新。 例如，Ubuntu Linux 提供了 [apt-get](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) 命令和[更新管理員](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html)工具，可用於更新作業系統。

### <a name="perform-additional-security-checks"></a>執行額外的安全性檢查

讓 Azure Marketplace 中解決方案映像維持高等級的安全性。 下列文章提供關於安全性設定和程序的檢查清單，其可協助達成下列目標：[Azure Marketplace 映像的安全性建議](../../security/security-recommendations-azure-marketplace-images.md)。 其中有些是 Linux 型映像的專屬建議，但多數建議皆適用於任何 VM 映像。

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>執行自訂的設定和排程工作

如果需要進行其他設定，請在部署 VM 後，使用在啟動時執行的已排程工作來進行最終的 VM 變更。 亦請考慮下列建議：

* 若為執行一次的工作，則此工作應該在順利完成後自行刪除。
* 設定不應依賴 C 或 D 以外的磁碟機，因為只有這兩個磁碟機一律保證會存在 (磁碟機 C 是作業系統磁碟，而磁碟機 D 則是暫存本機磁碟)。

如需 Linux 自訂項目的詳細資訊，請參閱[適用於 Linux 的虛擬機器擴充功能和功能](../../virtual-machines/extensions/features-linux.md)。

## <a name="generalize-the-image"></a>一般化映像

Azure Marketplace 中的所有映像通常都必須能夠重複使用。 若要達到此目標，則必須將作業系統 VHD 一般化，此作業會移除 VM 中所有的執行個體特定識別碼和軟體驅動程式。

### <a name="windows"></a>Windows

Windoes 作業系統磁碟是使用 [sysprep 工具](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)來進行一般化。 如果後續要更新或重新設定作業系統，則必須重新執行 sysprep。

> [!WARNING]
> 由於系統可能會自動執行更新，因此執行 sysprep 之後，請在執行部署前關閉 VM。 關閉動作可避免後續更新對作業系統或已安裝的服務進行執行個體特定變更。 如需執行 sysprep 的詳細資訊，請參閱[將 VHD 一般化的步驟](../../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep)。

### <a name="linux"></a>Linux

下列程序會將 Linux VM 一般化，並重新部署為個別的 VM。 如需詳細資料，請參閱[如何建立虛擬機器或 VHD 的映像](../../virtual-machines/linux/capture-image.md) (機器翻譯)。 當到達＜從擷取的映像建立 VM＞一節時即可停止。

1. **移除 Azure Linux 代理程式**

    1. 使用 SSH 用戶端連線到 Linux VM。
    2. 在 SSH 視窗中，輸入下列命令：`sudo waagent -deprovision+user`。
    3. 鍵入 **Y** 繼續進行 (可新增 **-force** 參數至上一個命令，即無須執行此確認步驟)。
    d. 在命令完成之後，鍵入 **Exit** 以關閉 SSH 用戶端。

2. **停止虛擬機器**

    1. 在 Azure 入口網站，選取資源群組 (RG) 並解除配置 VM。
    2. VHD 現已一般化，且可使用此 VHD 來建立新的 VM。

## <a name="next-steps"></a>後續步驟

如果您在建立以 Azure 為基礎的新 VHD 時遇到困難，請參閱 [VHD 建立期間的常見問題](common-issues-during-vhd-creation.md)。

否則：

* [從 VHD 部署的測試虛擬機器（VM）](azure-vm-image-certification.md)說明如何測試和提交 VM 映射以進行 Azure Marketplace 認證，包括可在何處取得*Azure 認證工具的認證測試控管*，以及如何使用它來認證您的 VM 映射。
