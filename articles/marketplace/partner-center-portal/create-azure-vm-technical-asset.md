---
title: 建立您的 Azure 虛擬機器技術資產
description: 瞭解如何建立及設定虛擬機器（VM）供應專案的技術資產以進行 Azure Marketplace。
author: dannyevers
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: 4d2d33f9d83132147b5b257ffcd6d659f272b8ec
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81730726"
---
# <a name="create-your-azure-virtual-machine-technical-assets"></a>建立您的 Azure 虛擬機器技術資產

> [!IMPORTANT]
> 我們正在將您 Azure 虛擬機器供應專案的管理從 Cloud Partner 入口網站移至合作夥伴中心。 在您的供應專案遷移之前，請遵循[建立虛擬機器供應專案的技術資產](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-technical-assets)中的指示，以供 Cloud Partner 入口網站管理您的優惠。

本文說明如何建立和設定適用于 Azure Marketplace 之虛擬機器（VM）供應專案的技術資產。 VM 包含兩個元件：作業系統虛擬硬碟（VHD）和選擇性相關聯的資料磁片 Vhd：

* **作業系統 VHD** –包含與您的供應專案一起部署的作業系統和解決方案。 準備 VHD 的程式會因為它是以 Linux 為基礎、以 Windows 為基礎或以自訂為基礎的 VM 而有所不同。
* **資料磁片 vhd** -VM 的專用、持續性儲存體。 請勿使用作業系統 VHD （例如 C：磁片磁碟機）來儲存持續性資訊。

VM 映射包含一個作業系統磁片和多達16個數據磁片。 針對每個資料磁片使用一個 VHD，即使磁片是空的也一樣。

> [!NOTE]
> 無論您使用哪一種作業系統，請只新增解決方案所需的最小資料磁片數目。 客戶無法在部署時移除屬於映射一部分的磁片，但它們一律可在部署期間或之後新增磁片。

> [!IMPORTANT]
> 方案中的每個 VM 映射都必須具有相同數目的資料磁片。

## <a name="fundamental-technical-knowledge"></a>基本技術知識

設計、建立及測試這些資產需要一些時間，而且需要 Azure 平臺和用來建立供應專案之技術的技術知識。 除了您的解決方案網域以外，您的工程小組也應具備下列 Microsoft 技術的知識：

* 對於 [Azure 服務](https://azure.microsoft.com/services/)的基本了解
* 如何[設計和架構 Azure 應用程式](https://azure.microsoft.com/solutions/architecture/)
* 具備 [Azure 虛擬機器](https://azure.microsoft.com/services/virtual-machines/)、[Azure 儲存體](https://azure.microsoft.com/services/?filter=storage)和 [Azure 網路](https://azure.microsoft.com/services/?filter=networking)的運用知識
* 具備 [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) 的運用知識
* 具備 [JSON](https://www.json.org/) 的運用知識

## <a name="suggested-tools--optional"></a>建議的工具–選擇性

請考慮使用下列其中一種腳本環境，協助管理 Vm 和 Vhd：

* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
* [Azure CLI](https://code.visualstudio.com/)

此外，請考慮將下列工具新增至您的開發環境：

* [Azure 儲存體總管](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Visual Studio Code](https://code.visualstudio.com/)
  * 延伸模組：[Azure Resource Manager 工具](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  * 延伸模組：[Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  * 延伸模組：[Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

請參閱[Azure 開發人員工具](https://azure.microsoft.com/product-categories/developer-tools/)頁面中的可用工具，如果您使用 Visual Studio， [Visual Studio Marketplace](https://marketplace.visualstudio.com/)。

## <a name="create-a-vm-image-using-an-approved-base"></a>使用已核准的基底建立 VM 映射

> [!NOTE]
> 若要使用您在自己的內部部署中建立的映射來建立虛擬機器技術資產，請移至[使用您自己的映射建立 VM](#create-a-vm-using-your-own-image)。

本節說明使用已核准基底的各種層面，例如使用遠端桌面通訊協定（RDP）、選取 VM 的大小、安裝最新的 Windows 更新，以及將 VHD 映射一般化。

下列各節主要著重于以 windows 為基礎的 Vhd。 如需建立以 Linux 為基礎的 Vhd 的詳細資訊，請參閱[Azure 背書的 linux 發行](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)版。

> [!WARNING]
> 遵循本主題中的指導方針，使用 Azure 建立包含預先設定之背書作業系統的 VM。 如果這與您的解決方案不相容，則可以使用已核准的作業系統來建立及設定內部部署 VM。 接著您可以依照[準備 Windows VHD 或 VHDX 以上傳至 Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image) 中所述，設定並準備上傳。

### <a name="select-an-approved-base"></a>選擇已核准的基礎映像

選取 Windows 作業系統或 Linux 作為基底。

#### <a name="windows"></a>Windows

以 Windows 為基礎的 VM 映射的作業系統 VHD 必須以包含 Windows Server 或 SQL Server 的 Azure 核准基底映射為基礎。 若要開始，請從 Azure 入口網站中的下列其中一個映射建立 VM：

* Windows Server ([2016](https://www.microsoft.com/evalcenter/evaluate-windows-server-2016)、[2012 R2 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)、[2012 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)、[2008 R2 SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview))
* [SQL Server 2014](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) （Enterprise、Standard、Web）
* [SQL Server 2012 SP2](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) （Enterprise、Standard、Web）

> [!NOTE]
> 如果您使用目前的 Azure 入口網站或 Azure PowerShell，則在2014年9月8日發佈的 Windows Server 映射會經過核准。

#### <a name="linux"></a>Linux

Azure 提供了一系列已核准的 Linux 散發套件。 如需目前的清單，請參閱[經 Azure 背書的 Linux 發佈](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)。

### <a name="create-vm-in-the-azure-portal"></a>在 Microsoft Azure 入口網站網站中建立 VM

請遵循下列步驟，在[Azure 入口網站](https://ms.portal.azure.com/)中建立基底 VM 映射：

1. 使用與您想要用來發佈虛擬機器供應專案的 Azure 訂用帳戶相關聯的 Microsoft 帳戶來登入[Azure 入口網站](https://ms.portal.azure.com/)。
2. 建立新的資源群組，並提供您的**資源群組名稱**、**訂用帳戶**以及**資源群組位置**。 如需詳細資訊，請參閱[管理資源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)。
3. 選取左側的 [**虛擬機器**] 以顯示 [虛擬機器詳細資料] 頁面。
4. 選取 [ **+ 新增**] 以開啟 [**建立虛擬機器] 體驗**。
5. 從下拉式清單中選取映射，或按一下 **[流覽所有公用和私用映射**]，以搜尋或流覽所有可用的虛擬機器映射。
6. 選取要使用下列建議部署的 VM 大小：
    * 如果您打算開發 VHD 內部部署，大小並不重要。 請考慮使用其中一個較小的 VM。
    * 如果您打算在 Azure 中開發映像，請考慮使用其中一個建議的 VM 大小做為選取的映像。

7. 在 [**磁片**] 區段中，展開 [ **Advanced** ] 區段，並將 [**使用受控磁片**] 選項設為 [**否**]。
8. 提供建立 VM 所需的其他詳細資料。
9. 選取 [**審查 + 建立**] 以審查您的選擇。 當您看到 [驗證成功]  訊息時，請選取 [建立]  。

Azure 會開始布建您指定的虛擬機器。 您可以選取左側的 [**虛擬機器**] 索引標籤來追蹤其進度。 建立之後，狀態會變更為 [**執行中]。**

如果您在建立以 Azure 為基礎的新 VHD 時遇到困難，請參閱[VHD 建立期間的常見問題（faq）](https://docs.microsoft.com/azure/marketplace/partner-center-portal/common-issues-during-vhd-creation)。

### <a name="connect-to-your-azure-vm"></a>連線至您的 Azure VM

本節說明如何連線到您在 Azure 上建立的 VM 並登入。 成功連線之後，您可以使用 VM，就像您在本機登入其主機伺服器一樣。

#### <a name="connect-to-a-windows-based-vm"></a>連線至 Windows 型 VM

使用遠端桌面用戶端連線到裝載于 Azure 上的 Windows VM。 大部分的 Windows 版本原本就支援遠端桌面通訊協定 (RDP)。 對於其他作業系統，您可以在[遠端桌面用戶端](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)中找到用戶端的詳細資訊。

本文詳細說明如何使用內建的 Windows RDP 支援來連線到您的 VM：[如何連線及登入執行 Windows 的 Azure 虛擬機器](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon)。

> [!TIP]
> 在此程式期間，您可能會收到安全性警告。 例如，「.rdp 檔案來自未知的發行者」或「無法驗證您的使用者認證」之類的警告。 您可以放心忽略這些警告。

#### <a name="connect-to-a-linux-based-vm"></a>連線至 Linux 型 VM

若要連線至以 Linux 為基礎的 VM，您需要一個安全的 shell 通訊協定（SSH）用戶端。 下列步驟會使用免費的[PuTTY](https://www.ssh.com/ssh/putty/) shh 私密金鑰終端機。

1. 移至 [Azure 入口網站](https://ms.portal.azure.com/)。
2. 搜尋並選取 [虛擬機器]  。
3. 選取您想要連接的 VM。
4. 啟動 VM （如果尚未執行）。
5. 選取 VM 的 [名稱]，以開啟其 **[總覽**] 頁面。
6. 請注意 VM 的公用 IP 位址和 DNS 名稱（如果未設定這些值，您必須[建立網路介面](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface)）。
7. 開啟 PuTTY 應用程式。
8. 在 [PuTTY 設定] 對話方塊中，輸入您 VM 的 IP 位址或 DNS 名稱。

    :::image type="content" source="media/avm-putty.png" alt-text="說明 PuTTY 終端機設定。[主機名稱] 或 [IP 位址] 和 [埠] 方塊會反白顯示。":::

9. 選取 [**開啟**] 以開啟 PuTTY 終端機。
10. 出現提示時，輸入您的 Linux VM 帳戶的帳戶名稱和密碼。

如果您有連線問題，請參閱 SSH 用戶端的檔。 例如，第[10 章：一般錯誤訊息](https://www.ssh.com/ssh/putty/putty-manuals)。

如需詳細資訊，包括如何將桌面新增至布建的 Linux VM，請參閱[安裝和設定遠端桌面以連線至 Azure 中的 LINUX vm](https://docs.microsoft.com/azure/virtual-machines/linux/use-remote-desktop)。

## <a name="create-a-vm-using-your-own-image"></a>使用您自己的映射建立 VM

本節說明如何建立和部署使用者提供的虛擬機器（VM）映射。 若要這麼做，您可以從 Azure 部署的虛擬硬碟（VHD）提供作業系統和資料磁片 VHD 映射。

> [!NOTE]
> 若要選擇性地使用已核准的基底映射，請遵循[使用已核准的基底建立 VM 映射](#create-a-vm-image-using-an-approved-base)中的指示。

1. 將您的映射上傳至 Azure 儲存體帳戶。
2. 部署 VM 映射。
3. 捕獲 VM 映射。

### <a name="upload-your-images-to-an-azure-storage-account"></a>將您的映射上傳至 Azure 儲存體帳戶

1. 登入[Azure 入口網站](https://portal.azure.com/)。
2. 將您的一般化作業系統 VHD 和資料磁片 Vhd 上傳至您的 Azure 儲存體帳戶。

### <a name="deploy-your-image"></a>部署您的映射

請使用 Azure 入口網站或 Azure PowerShell 建立映射。

#### <a name="deploy-using-the-azure-portal"></a>使用 Azure 入口網站進行部署

1. 在 [首頁] 頁面上，選取 [**建立資源**]，搜尋「範本部署」，然後選取 [**建立**]。
2. **在編輯器中選擇 [建立您自己的範本**]。

    :::image type="content" source="media/avm-custom-deployment.png" alt-text="說明自訂部署頁面。":::

3. 將此[JSON 範本](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-deploy-json-template)貼入編輯器中，然後選取 [**儲存**]。
4. 為顯示的**自訂部署**屬性頁面提供參數值。

    | 參數 | 描述 |
    | ------------ | ------------- |
    | 儲存體帳戶名稱 | 資料格2的內容 |
    | 輸入儲存體容器名稱 | 一般化 VHD 所在的儲存體帳戶名稱 |
    | 公用 IP 的 DNS 名稱 | 公用 IP DNS 名稱。 在部署供應專案之後，定義 Azure 入口網站中公用 IP 位址的 DNS 名稱。 |
    | 管理員使用者名稱 | 新 VM 系統管理員帳戶的使用者名稱 |
    | 管理員密碼 | 新 VM 管理員帳戶密碼 |
    | OS 類型 | VM 作業系統： Windows 或 Linux |
    | 訂用帳戶識別碼 | 讓選取的訂用帳戶取得安全性建議 |
    | 位置 | 部署的地理位置 |
    | VM 大小 | [AZURE VM 大小](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)，例如 Standard_A2 |
    | 公用 IP 位址 | 您的公用 IP 位址名稱 |
    | 虛擬機器名稱 | 新 VM 名稱 |
    | 虛擬網路名稱 | VM 使用的虛擬網路名稱 |
    | NIC 名稱 | 正在執行虛擬網路的網路介面卡名稱 |
    | VHD URL | 完整的 OS 磁碟 VHD URL |
    |  |  |

5. 提供這些值之後，請選取 [**購買**]。

Azure 將會開始部署。 它會在指定的儲存體帳戶路徑中，使用指定的非受控 VHD 來建立新的 VM。 您可以選取入口網站左側的 [**虛擬機器**]，來追蹤 Azure 入口網站中的進度。 建立 VM 時，狀態會從 [開始] 變更為 [執行中]。

#### <a name="deploy-using-azure-powershell"></a>使用 Azure PowerShell 進行部署

```powershell
    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot
```

### <a name="capture-the-vm-image"></a>擷取 VM 映像

使用與您的方法對應的下列指示：

* Azure PowerShell：[如何從 AZURE VM 建立非受控 VM 映射](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)
* Azure CLI：[如何建立虛擬機器或 VHD 映像](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)
* API：[虛擬機器 - 擷取](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)

## <a name="configure-the-virtual-machine"></a>設定虛擬機器

本節說明如何針對 Azure VM 進行大小、更新和一般化。 您必須執行這些步驟，才能準備將 VM 部署在 Azure Marketplace 上。

### <a name="sizing-the-vhds"></a>調整 VHD 大小

如果您選取了其中一個使用作業系統預先設定的 Vm （以及選擇性的其他服務），您就已經挑選了標準的 Azure VM 大小。 建議使用預先設定的作業系統啟動您的解決方案。 不過，如果您要手動安裝 OS，您必須在 VM 映射中調整主要 VHD 的大小：

* 針對 Windows，應將作業系統 VHD 建立為127– 128 GB 固定格式的 VHD。
* 針對 Linux，此 VHD 應建立為30– 50 GB 固定格式 VHD。

如果實體大小小於127– 128 GB，則 VHD 應該是可擴充的（sparse/動態）。 所提供的基底 Windows 和 SQL Server 映射已經符合這些需求，因此請勿變更 VHD 的格式或大小。

資料磁碟的大小可高達 1 TB。 在決定大小時，請記住，客戶無法在部署時調整映射中的 Vhd 大小。 應將資料磁碟 VHD 建立為固定格式的 VHD。 它們也應該是可擴充的（sparse/動態）。 初始資料磁碟可為空白，也可以含有資料。

### <a name="install-the-most-current-updates"></a>安裝最新的更新

作業系統 Vm 的基本映射必須包含最新的更新，直到其發行日期為止。 發佈您所建立的作業系統 VHD 之前，請確定您已使用所有最新的安全性和維護修補程式來更新 OS 和所有已安裝的服務。

若為 Windows Server，請執行 [**檢查更新**] 命令。

若為 Linux 發行版本，通常會透過命令列工具或圖形化公用程式來下載和安裝更新。 例如，Ubuntu Linux 提供了 [apt-get](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) 命令和[更新管理員](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html)工具，可用於更新作業系統。

### <a name="perform-additional-security-checks"></a>執行額外的安全性檢查

在 Azure Marketplace 中，為您的解決方案映射維護高層級的安全性。 下列文章提供安全性設定和程式的檢查清單，以協助您： [Azure Marketplace 映射的安全性建議](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images)。 其中有些是 Linux 型映像的專屬建議，但多數建議皆適用於任何 VM 映像。

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>執行自訂的設定和排程工作

如果需要其他設定，請使用在啟動時執行的排程工作，在部署之後對 VM 進行最後的變更。 亦請考慮下列建議：

* 如果這是執行一次的工作，工作應該會在成功完成後予以刪除。
* 設定不應依賴 C 或 D 以外的磁片磁碟機，因為只有這兩個磁片磁碟機一定會存在（磁片磁碟機 C 是作業系統磁片，而磁片磁碟機 D 是暫存本機磁片）。

如需 Linux 自訂項目的詳細資訊，請參閱[適用於 Linux 的虛擬機器擴充功能和功能](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux)。

## <a name="generalize-the-image"></a>一般化映像

Azure Marketplace 中的所有映像通常都必須能夠重複使用。 若要達到此目的，必須將作業系統 VHD 一般化，這是一種作業，可從 VM 中移除所有實例特定的識別碼和軟體驅動程式。

### <a name="windows"></a>Windows

Windoes 作業系統磁碟是使用 [sysprep 工具](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)來進行一般化。 如果後續要更新或重新設定作業系統，則必須重新執行 sysprep。

> [!WARNING]
> 由於更新可能會自動執行，因此在您執行 sysprep 之後，請關閉 VM，直到其部署完成為止。 這種關機會避免後續的更新對作業系統或已安裝的服務進行實例特定的變更。 如需執行 sysprep 的詳細資訊，請參閱[將 VHD 一般化的步驟](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep)。

### <a name="linux"></a>Linux

下列進程會一般化 Linux VM，並將其重新部署為個別的 VM。 如需詳細資訊，請參閱[如何建立虛擬機器或 VHD 的映射](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)。 當您到達「從已捕獲的映射建立 VM」一節時，您可以停止。

1. **移除 Azure Linux 代理程式**

    1. 使用 SSH 用戶端連線到 Linux VM。
    2. 在 SSH 視窗中，輸入下列命令： `sudo waagent -deprovision+user`。
    3. 輸入**Y**繼續進行（您可以將 **-force**參數新增至先前的命令，以避免確認步驟）。
    d. 在命令完成之後，輸入**Exit**關閉 SSH 用戶端。

2. **停止虛擬機器**

    1. 在 [Azure 入口網站中，選取您的資源群組（RG）並取消配置 VM。
    2. 您的 VHD 現已一般化，您可以使用此 VHD 來建立新的 VM。

## <a name="next-steps"></a>後續步驟

如果您在建立以 Azure 為基礎的新 VHD 時遇到困難，請參閱 [VHD 建立期間的常見問題](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-vhd-creation-issues)。

否則就是：

* [認證您的 vm 映射](https://docs.microsoft.com/azure/marketplace/partner-center-portal/get-sas-uri)說明如何測試和提交 vm 映射以進行 Azure Marketplace 認證，包括可在何處取得*Azure 認證工具的認證測試控管*，以及如何使用它來認證您的 VM 映射。
