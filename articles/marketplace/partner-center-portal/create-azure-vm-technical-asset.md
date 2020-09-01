---
title: 建立 Azure 虛擬機器技術資產
description: 了解如何針對 Azure Marketplace 為虛擬機器 (VM) 供應項目建立及設定技術資產。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 08/14/2020
ms.openlocfilehash: 496cd4aeb96ca1849e950331658014d91dc6d6ba
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/30/2020
ms.locfileid: "89143862"
---
# <a name="create-azure-virtual-machine-technical-assets"></a>建立 Azure 虛擬機器技術資產

將虛擬機器 (VM) 映射發佈到 Azure Marketplace 時，Azure 小組會驗證 VM 映射，以確保其 bootability、安全性和 Azure 相容性。 如果有任何高品質的測試失敗，則發佈將會失敗，並顯示包含錯誤和可能 [改正步驟](https://docs.microsoft.com/azure/marketplace/partner-center-portal/vm-certification-issues-solutions)的訊息。

本文描述如何針對 Azure Marketplace 為虛擬機器 (VM) 供應項目建立及設定技術資產。 VM 包含兩個元件：作業系統的虛擬硬碟 (VHD)，以及選擇性的相關聯資料磁碟 VHD：

1. **作業系統 VHD** – 包含隨供應項目一起部署的作業系統和解決方案。 準備 VHD 的程式會因其為以 Linux 為基礎、以 Windows 為基礎的虛擬機器，還是以自訂為基礎的 VM 而有所不同。

2. **資料磁片 vhd** –適用于 VM 的專用、持續性儲存體。 請勿使用作業系統 VHD (例如 C: 磁碟機) 來儲存永續性資訊。

VM 映像包含一個作業系統磁碟以及多逹 16 個資料磁碟。 針對每個資料磁碟使用一個 VHD，即使磁碟是空的也一樣。

> [!NOTE]
> 無論使用哪一種作業系統，只要新增解決方案所需的最少數目資料磁碟。 客戶無法在部署時移除屬於映像一部分的磁碟，但可隨時在部署期間或之後新增磁碟。

> [!IMPORTANT]
> 方案中每個 VM 映像都必須具有相同數目的資料磁碟。

## <a name="fundamental-technical-knowledge"></a>基本技術知識

設計、建置和測試這些資產需要時間，且需要具備關於 Azure 平台與建置供應項目所用技術的技術知識。 除了解決方案領域外，工程小組還應具備下列 Microsoft 技術的知識：

- 對於 [Azure 服務](https://azure.microsoft.com/services/)的基本了解
- 如何[設計和架構 Azure 應用程式](https://azure.microsoft.com/solutions/architecture/)
- 具備 [Azure 虛擬機器](https://azure.microsoft.com/services/virtual-machines/)、[Azure 儲存體](https://azure.microsoft.com/services/?filter=storage)和 [Azure 網路](https://azure.microsoft.com/services/?filter=networking)的運用知識
- 具備 [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) 的運用知識
- 具備 [JSON](https://www.json.org/) 的運用知識

### <a name="optional-suggested-tools"></a>選用的建議工具

請考慮使用下列其中一種指令碼環境來協助管理 VM 和 VHD：

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
- [Azure CLI](https://code.visualstudio.com/)

此外，也請考慮將下列工具新增至開發環境：

- [Azure 儲存體總管](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
- [Visual Studio Code](https://code.visualstudio.com/)

## <a name="create-a-vm-image-using-an-approved-base"></a>使用已核准的基礎映像建立 VM 映像

若要使用您在自己的內部部署中建立的映射來建立虛擬機器技術資產，請參閱下方 [的核准基底建立 VM 映射](#create-a-vm-image-using-an-approved-base) 。

本節描述使用已核准基礎映像的不同層面，例如如何使用遠端桌面通訊協定 (RDP)、選擇 VM 的大小、安裝最新的 Windows 更新，以及將 VHD 映像一般化。

遵循本文中的指導方針，使用 Azure 來建立包含預先設定且背書之作業系統的 VM。 若此主題內容與解決方案不相容，您也可以使用已核准的作業系統來建立並設定內部部署 VM。 接著您可以依照[準備 Windows VHD 或 VHDX 以上傳至 Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image) 中所述，設定並準備上傳。

### <a name="select-an-approved-base-image"></a>選取核准的基底映射

選取 Windows 作業系統或 Linux 作為基礎映像。

VM 映像的作業系統 VHD 必須以獲得 Azure 核准的基底映像為基礎，包含 Windows Server 或 SQL Server。

當您提交以更新重新發佈映射的要求時，元件編號驗證測試案例可能會失敗。 在該實例中，您的映射不會經過核准。

當您使用屬於另一個發行者的基底映射，而且已更新映射時，就會發生此錯誤。 在此情況下，將不會允許您發佈映射。

若要修正此問題，請從 Azure Marketplace 取出您最新的映射，並對該映射進行變更。 請參閱下列內容，以查看您可以在其中搜尋映射的已核准基礎映射：

#### <a name="windows"></a>Windows

- Windows Server ([2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)、 [2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)、 [2012 r2 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)、 [2012 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)、 [2008 R2 SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)) 
- SQL Server 2019 ([Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview)、 [Standard](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview)、 [Web](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview)) 
- SQL Server 2014 ([Enterprise](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance)、 [Standard](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance)、 [Web](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance)) 
- SQL Server 2012 SP2 ([Enterprise](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance)、 [Standard](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance)、 [Web](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance)) 

#### <a name="linux"></a>Linux

Azure 會提供一組已核准的 Linux 散發套件。 如需目前的清單，請參閱[經 Azure 背書的 Linux 發佈](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)。

### <a name="create-vm-on-the-azure-portal"></a>在 Azure 入口網站上建立 VM

請遵循下列步驟，在 [Azure 入口網站](https://ms.portal.azure.com/)上建立基底 VM 映射：

1. 使用 Microsoft 帳戶 (其與想要用於發佈 VM 供應項目的 Azure 訂用帳戶建立關聯) 登入 [Azure入口網站](https://ms.portal.azure.com/)。
2. 建立新的資源群組，並提供您的**資源群組名稱**、**訂用帳戶**以及**資源群組位置**。 如需詳細資料，請參閱 [管理資源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal) (機器翻譯)。

    :::image type="content" source="media/vm/create-resource-group.png" alt-text="顯示建立資源群組的開始。":::

3. 選取左側導覽中的 [ **虛擬機器** ]，以顯示虛擬機器詳細資料頁面。
4. 選取 [+ 新增] 以開啟 [建立虛擬機器體驗]。
5. 從下拉式清單中選取影像，或選取 **[流覽所有公用和私用映射]** ，以搜尋或流覽所有可用的虛擬機器映射。 範例：

    :::image type="content" source="media/vm/create-resource-group-example.png" alt-text="顯示 VM 映射範例。":::

6. 選取要使用下列建議部署的 VM 大小：
    1. 如果打算開發 VHD 內部部署，則大小不會造成影響。 請考慮使用其中一個較小的 VM。
    2. 如果您打算在 Azure 中開發映像，請考慮使用其中一個建議的 VM 大小做為選取的映像。

    :::image type="content" source="media/vm/create-virtual-machine.png" alt-text="顯示選取的 VM 大小。":::

7. 在 [磁碟] 區段中，展開 [進階] 區段，並將 [使用受控磁碟] 選項設定為 [否]。

    :::image type="content" source="media/vm/use-managed-disks.png" alt-text="顯示使用受控磁片的選項。":::

8. 提供建立 VM 所需的其他詳細資料。
9. 選取 [檢閱 + 建立] 以檢閱選擇。 當您看到 [驗證成功] 訊息時，請選取 [建立]。

Azure 會開始佈建所指定的虛擬機器。 您可選取左側的 [虛擬機器] 索引標籤來追蹤其進度。 建立虛擬機器後，狀態會變更為 [執行中]。

### <a name="create-a-generation-2-vm-on-the-azure-portal"></a>在 Azure 入口網站上建立第2代 VM

在 Azure 入口網站中建立第2代 (Gen2) VM。

1. 登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com/)。
2. 選取 [建立資源]。
3. 從左邊的 Azure Marketplace 選取 [ **查看全部** ]。
4. 選取支援 Gen2 的映射。
5. 選取 [建立]。
6. 在 [進階] 索引標籤的 [VM 世代] 區段底下，選取 [Gen 2] 選項。
7. 在 [基本] 索引標籤的 [執行個體詳細資料] 底下，移至 [大小] 並開啟 [選取 VM 大小] 刀鋒視窗。
8. 選取 [支援的 Gen 2 VM](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2#generation-2-vm-sizes) 和大小的建議大小。
9. 請瀏覽 [Azure 入口網站建立流程](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)以完成建立 VM。

    :::image type="content" source="media/vm/vm-generation.png" alt-text="顯示選取 VM 世代的選項。":::

## <a name="connect-to-your-azure-vm"></a>連線至您的 Azure VM

本節說明如何連線並登入您在 Azure 上建立的 VM。 成功連線後，使用 VM 的方式即等同您已在本機登入其主機伺服器。

### <a name="connect-to-a-windows-based-vm"></a>連線至 Windows 型 VM

使用遠端桌面用戶端連線至裝載於 Azure 上的 Windows 架構 VM。 大部分的 Windows 版本原本就支援遠端桌面通訊協定 (RDP)。 如需其他作業系統的用戶端資訊，請參閱[遠端桌面用戶端](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)。

本文將詳細說明如何使用內建的 Windows RDP 支援連接到您的 VM： [如何連線及登入執行 Windows 的 Azure 虛擬機器](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon)。

> [!TIP]
> 您可能會在過程中收到安全性警告。 例如，「.rdp 檔案來自未知的發行者」或「使用者的認證無法驗證」之類的警告。 您可以放心忽略這些警告。

### <a name="connect-to-a-linux-based-vm"></a>連線至 Linux 型 VM

若要連線至 Linux 架構 VM，則需要使用安全殼層通訊協定 (SSH) 用戶端。 下列步驟會使用免費的 [PuTTY](https://www.ssh.com/ssh/putty/) SHH 終端機。

1. 移至 [Azure 入口網站](https://ms.portal.azure.com/)。
2. 搜尋並選取 [虛擬機器]。
3. 選取想要連線的目標 VM。
4. 啟動 VM (如果尚未執行)。
5. 選取 VM 的名稱，以開啟其 [概觀] 頁面。
6. 請注意您 VM 的公用 IP 位址和 DNS 名稱 (如果未設定這些值，您必須 [建立網路介面](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface)。
7. 開啟 PuTTY 應用程式。
8. 在 [PuTTY 設定] 對話方塊中，輸入您 VM 的 IP 位址或 DNS 名稱。

    :::image type="content" source="media/vm/putty-configuration.png" alt-text="說明 PuTTY 終端機設定，並反白顯示主機名稱和埠欄位。":::

9. 選取 [開啟] 以開啟 PuTTY 終端機。
10. 系統提示時，請輸入 Linux VM 帳戶的帳戶名稱和密碼。

## <a name="configure-the-virtual-machine"></a>設定虛擬機器

本節描述如何針對 Azure VM 進行調整大小、更新及一般化。 若要準備在 Azure Marketplace 部署虛擬機器，則必須完成以下步驟。

### <a name="sizing-the-vhds"></a>調整 VHD 大小

下列規則適用于 OS 磁片大小的限制。 當您提交任何要求時，請確定作業系統磁片大小是在 Linux 或 Windows 的限制內。

| OS | 建議的 VHD 大小 |
| --- | --- |
| Linux | 30至 1023 GB |
| Windows | 30至 250 GB |

當 Vm 允許存取基礎作業系統時，請確定 VHD 大小夠大，足以容納 VHD。 因為磁片無法在沒有停機的情況下展開，所以請使用介於30到 50 GB 之間的磁片大小 &nbsp; 。

| VHD 大小 | 實際佔用的大小 | 解決方案 |
| --- | --- | --- |
| >500 TB | n/a | 請洽詢支援小組以取得例外狀況核准。 |
| 250-500 TB | 不同于 blob 大小的 >200 GB | 請洽詢支援小組以取得例外狀況核准。 |

### <a name="install-the-most-current-updates"></a>安裝最新的更新

作業系統 VM 的基礎映像，必須包含截至其發佈日期的最新更新。 發佈已建立的作業系統 VHD 之前，請確認已使用所有最新安全性和維護修補程式來更新 OS 和所有已安裝的服務。

- 針對 Windows Server，請執行 [檢查更新] 命令。
- 若為 Linux 發行版本，通常會透過命令列工具或圖形化公用程式來下載和安裝更新。 例如，Ubuntu Linux 提供了 [apt-get](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) 命令和[更新管理員](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html)工具，可用於更新作業系統。

#### <a name="perform-additional-security-checks"></a>執行額外的安全性檢查

讓 Azure Marketplace 中解決方案映像維持高等級的安全性。 如需安全性設定和程式的檢查清單，請參閱 [Azure Marketplace 映射的安全性建議](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images)。 其中有些是 Linux 型映像的專屬建議，但多數建議皆適用於任何 VM 映像。

#### <a name="perform-custom-configuration-and-scheduled-tasks"></a>執行自訂的設定和排程工作

如果您需要額外的設定，請使用在啟動時執行的排程工作，在 VM 部署完成後進行最後的變更。 並請考慮下列項目：

- 若為執行一次的工作，則此工作應該在順利完成後自行刪除。
- 設定不應依賴 C 或 D 以外的磁碟機，因為只有這兩個磁碟機一律保證會存在 (磁碟機 C 是作業系統磁碟，而磁碟機 D 則是暫存本機磁碟)。

如需 Linux 自訂項目的詳細資訊，請參閱[適用於 Linux 的虛擬機器擴充功能和功能](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux)。

## <a name="generalize-the-image"></a>一般化映像

Azure Marketplace 中的所有映像通常都必須能夠重複使用。 若要達到此目標，則必須將作業系統 VHD 一般化，此作業會移除 VM 中所有的執行個體特定識別碼和軟體驅動程式。

### <a name="for-windows"></a>若為 Windows

Windows 作業系統磁片會以 [sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) 工具一般化。 如果您稍後更新或重新設定 OS，則必須再次執行 sysprep。

> [!WARNING]
> 執行 sysprep 之後，請將 VM 關閉直到部署完成，因為更新可能會自動執行。 關閉動作可避免後續更新對作業系統或已安裝的服務進行執行個體特定變更。 如需執行 sysprep 的詳細資訊，請參閱[將 VHD 一般化的步驟](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep)。

### <a name="for-linux"></a>若為 Linux

下列程序會將 Linux VM 一般化，並重新部署為個別的 VM。 如需詳細資料，請參閱[如何建立虛擬機器或 VHD 的映像](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image) (機器翻譯)。 當您到達稱為「從已捕獲映射建立 VM」的區段時，您可以停止。

1. 移除 Azure Linux 代理程式

    1. 使用 SSH 用戶端連接到您的 Linux VM
    2. 在 SSH 視窗中，輸入下列命令：`sudo waagent –deprovision+user`。
    3. 鍵入 Y 繼續進行 (可新增 -force 參數至上一個命令，即無須執行此確認步驟)。
    4. 在命令完成之後，鍵入 Exit 以關閉 SSH 用戶端。

2. 停止虛擬機器

    1. 在 Azure 入口網站，選取資源群組 (RG) 並解除配置 VM。
    2. VHD 現已一般化，且可使用此 VHD 來建立新的 VM。

## <a name="next-steps"></a>後續步驟

- 如果您在建立以 Azure 為基礎的新 VHD 時遇到困難，請參閱 [VHD 建立期間的常見問題](common-issues-during-vhd-creation.md)。
- 如果不是，則 [測試從 VHD 部署的虛擬機器 (vm) ](azure-vm-image-certification.md) 會說明如何測試和提交 vm 映射以進行 Azure Marketplace 認證，包括哪裡可以取得適用于 Azure 認證的認證測試控管，以及如何使用它來認證您的 vm 映射。
