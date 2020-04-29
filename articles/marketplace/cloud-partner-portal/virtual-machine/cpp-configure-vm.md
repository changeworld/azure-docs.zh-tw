---
title: 設定 Azure Marketplace 的 Microsoft Azure 託管 VM
description: 說明如何針對 Azure 上裝載的虛擬機器，執行調整大小、更新和一般化等作業。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: dsindona
ms.openlocfilehash: 4cb247a3e64f8d44cc64010dde40963f4e9a1993
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82146087"
---
# <a name="configure-the-azure-hosted-vm"></a>設定 Azure 裝載的虛擬機器

> [!IMPORTANT]
> 從2020年4月13日開始，我們會開始將您的 Azure 虛擬機器供應專案移至合作夥伴中心。 在遷移之後，您將在合作夥伴中心建立和管理您的供應專案。 請依照[建立 Azure 虛擬機器技術資產](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer)中的指示來管理您遷移的供應專案。

本文說明如何針對 Azure 上裝載的虛擬機器 (VM)，執行調整大小、更新和一般化等作業。  在準備以 Azure Marketplace 部署虛擬機器時，必須完成以下步驟。

## <a name="sizing-the-vhds"></a>調整 VHD 大小

<!--TD: Check if the following assertion is true. I didn't understand the original content. -->
如果您已選取其中一個預先設定作業系統的 Vm （以及選擇性的其他服務），則您已挑選標準的 Azure VM 大小，如[虛擬機器 sku](./cpp-skus-tab.md)索引標籤中所述。 建議您使用預先設定的 OS 來啟動您的解決方案，這是建議的方法。  不過，如果您要以手動方式安裝作業系統，則必須在 VM 映像中調整主要 VHD 的大小：

- 若為 Windows，應將作業系統 VHD 建立為 127-128 GB 固定格式的 VHD。 
- 入圍 Linux，應將此 VHD 建立為 30-50 GB 固定格式的 VHD。

如果實體大小不到 127-128 GB，此 VHD 應為疏鬆 VHD。 所提供的基底 Windows 和 SQL Server 映像皆符合這些需求；所以請勿變更所取得之 VHD 的格式或大小。 

資料磁碟的大小可高達 1 TB。 決定磁碟大小時，請記得客戶無法於部署時在映像中調整 VHD 大小。 應將資料磁碟 VHD 建立為固定格式的 VHD。 也可以是疏鬆 VHD。 初始資料磁碟可為空白，也可以含有資料。


## <a name="install-the-most-current-updates"></a>安裝最新的更新

作業系統虛擬機器的基礎映像，包含截至發佈日期的最新更新。 發佈已建立的作業系統 VHD 之前，請確認已使用所有最新的安全性和維護修補程式，來更新作業系統和所有已安裝的服務。

若為 Windows Server 2016，請執行 **Check for Updates** 命令。  或是若為舊版的 Windows，請查看[如何透過 Windows Update 取得更新](https://support.microsoft.com/help/3067639/how-to-get-an-update-through-windows-update)。  Windows Update 會自動安裝最新的重大與重要安全性更新。

若為 Linux 發行版本，通常會透過命令列工具或圖形化公用程式來下載和安裝更新。  例如，Ubuntu Linux 提供了 [apt-get](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) 命令和[更新管理員](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html)工具，可用於更新作業系統。


## <a name="perform-additional-security-checks"></a>執行額外的安全性檢查

請讓 Azure Marketplace 中的解決方案映像維持高等級的安全性。  下列文章提供了關於安全性設定和程序的檢查清單，可協助您達成此目標：[Azure Marketplace 映像的安全性建議](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images)。  其中有些是 Linux 型映像的專屬建議，但多數建議皆適用於任何 VM 映像。 


## <a name="perform-custom-configuration-and-scheduled-tasks"></a>執行自訂的設定和排程工作

如果需要進行其他設定，建議做法是在部署虛擬機器後，使用啟動時執行的已排程工作，進行最終的虛擬機器變更。  亦請考慮下列建議：
- 若為一次性執行的工作，建議讓此工作在順利完成後自行刪除。
- 所有設定都不該依靠磁碟機 C 或 D 以外的磁碟機，因為 C 和 D 是唯二保證隨時都存在的磁碟機。 磁碟機 C 是作業系統磁碟，而磁碟機 D 是暫存本機磁碟。

如需 Linux 自訂項目的詳細資訊，請參閱[適用於 Linux 的虛擬機器擴充功能和功能](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux)。


## <a name="generalize-the-image"></a>一般化映像

Azure Marketplace 中的所有映像通常都必須能夠重複使用。 若要達到重複使用的目標，必須將作業系統 VHD *一般化*，此作業會移除虛擬機器中所有的執行個體特定識別碼和軟體驅動程式。

### <a name="windows"></a>Windows

Windoes 作業系統磁碟是使用 [sysprep 工具](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)來進行一般化。 如果後續要更新或重新設定作業系統，則必須重新執行 sysprep。 

> [!WARNING]
>  由於系統可能會自動執行更新，因此一旦執行了 sysprep，請在執行部署前關閉虛擬機器。  關閉動作可避免後續的更新對 VHD 作業系統或是已安裝的服務進行執行個體特定的變更。

如需執行 sysprep 的詳細資訊，請參閱[將 VHD 一般化的步驟](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep)

### <a name="linux"></a>Linux

下列兩個步驟的程式會一般化 Linux VM，並將其重新部署為個別的 VM。 這兩個步驟只是處理常式的基本工作。 如需這兩個步驟的詳細資訊，以及它們必須完成的原因，請參閱[如何建立虛擬機器或 VHD 的映射](../../../virtual-machines/linux/capture-image.md)。 為了針對您的 Azure Marketplace 供應專案建立 VHD，您可以在到達「從已捕獲的映射建立 VM」一節時停止。

#### <a name="remove-the-azure-linux-agent"></a>移除 Azure Linux 代理程式
1.  使用 SSH 用戶端連線到 Linux VM。
2.  在 SSH 視窗中，輸入下列命令： <br/>
    `sudo waagent -deprovision+user`
3.  輸入 `y` 以繼續。 (您可新增 `-force` 參數至上一個命令，如此即無須執行此確認步驟。)
4.  在命令完成之後，輸入`exit` 關閉 SSH 用戶端。

<!-- TD: I need to add meat and/or references to the following steps -->
#### <a name="capture-the-image"></a>擷取映像
1.  移至 Azure 入口網站，選取您的資源群組 (RG) 並解除配置虛擬機器。
2.  您的 VHD 現已一般化，且您可使用此 VHD 來建立新的虛擬機器。


## <a name="create-one-or-more-copies"></a>建立一或多個副本

在需要進行備份、測試、自訂容錯或負載平衡時，建立虛擬機器副本常常相當有用，可藉此提供解決方案的不同設定等。 如需如何複製和下載主要 VHD 以建立未受管複製品的相關資訊，請參閱：

- Linux 虛擬機器：[從 Azure 下載 Linux VHD](../../../virtual-machines/linux/download-vhd.md)
- Windows 虛擬機器：[從 Azure 下載 Windows VHD](../../../virtual-machines/windows/download-vhd.md)


## <a name="next-steps"></a>後續步驟

在您的 VM 一般化之後，已解除配置，而且您已建立 VM 的映射，您就可以開始[從虛擬硬碟部署虛擬機器](./cpp-deploy-vm-vhd.md)。
