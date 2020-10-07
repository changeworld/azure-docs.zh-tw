---
title: Azure 中的 Windows VM 概觀
description: Azure 中的 Windows 虛擬機器概觀。
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 11/14/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 4e955a51906f160264fc4e81f263fe7677be91de
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "88589617"
---
# <a name="windows-virtual-machines-in-azure"></a>Azure 中的 Windows 虛擬機器

Azure 虛擬機器 (VM) 是由 Azure 所提供的[隨選且可調整的數種運算資源](/azure/architecture/guide/technology-choices/compute-decision-tree)類型之一。 一般而言，當您對於運算環境所需的控制權比其他選擇可提供的還要多時，則您會選擇 VM。 本文提供您在建立 VM 之前應該的事項、建立方式及管理方式的相關資訊。

Azure VM 讓您能夠有彈性地進行虛擬化，而不需購買並維護執行它的實體硬體。 不過，您仍然需要執行工作來維護 VM，例如設定、修補和安裝在 VM 上執行的軟體。

Azure 虛擬機器可用於許多用途。 部份範例如下：

* **開發和測試** – Azure VM 提供了一個快速且簡單的方法，用以建立為應用程式撰寫程式碼並進行測試時所需之特定組態的電腦。
* **雲端中的應用程式** – 因為您應用程式的需求可能會變動，在 Azure VM 上執行它在經濟上是合理的。 當您需要 VM 時便支付額外的 VM，而當您不需要時便關閉這些 VM。
* **擴充的資料中心** – 可輕鬆將 Azure 虛擬網路中的虛擬機器連線到您組織的網路。

您的應用程式所使用的 VM 數目可以擴大及擴增為符合您需求的任何內容。

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>我在建立 VM 之前需要先考慮什麼？
當您在 Azure 中建置應用程式基礎結構時，總是會有許多[設計考量](/azure/architecture/reference-architectures/n-tier/windows-vm)。 在您開始之前，仔細考量 VM 的這些層面很重要︰

* 應用程式資源的名稱
* 將儲存資源的位置
* VM 的大小
* 可建立的 VM 數目上限
* VM 上執行的作業系統
* VM 啟動後的設定
* VM 需要的相關資源

### <a name="locations"></a>位置
Azure 中所建立的所有資源分散在世界各地的多個[地理區域](https://azure.microsoft.com/regions/)。 通常，當您建立 VM 時，區域稱為**位置**。 針對 VM，位置會指定虛擬硬碟所儲存的位置。

下表顯示一些您可以取得可用位置清單的方式。

| 方法 | 描述 |
| --- | --- |
| Azure 入口網站 |當您建立 VM 時，請從清單中選取位置。 |
| Azure PowerShell |使用 [Get-AzLocation](/powershell/module/az.resources/get-azlocation) 命令。 |
| REST API |使用[列出位置](/rest/api/resources/subscriptions)作業。 |
| Azure CLI |使用 [az account list-locations](/cli/azure/account?view=azure-cli-latest) 作業。 |

### <a name="singapore-data-residency"></a>新加坡資料落地

在 Azure 中，將客戶資料儲存在單一區域中的功能目前僅適用於亞太地區的東南亞區域 (新加坡)。 至於其他所有區域，客戶資料會儲存在地區中。 如需詳細資訊，請參閱[信任中心](https://azuredatacentermap.azurewebsites.net/)。

## <a name="availability"></a>可用性
Azure 已宣布推出業界領先的單一執行個體虛擬機器 99.9% 服務等級協定 (前提是您部署的所有磁碟都是進階儲存體的 VM)。  為了讓您的部署符合標準的 99.95% VM 服務等級協定資格，您還必須在可用性設定組內部署兩個或更多執行工作負載的 VM。 可用性設定組可確保您的 VM 會分散在 Azure 資料中心內的多個容錯網域，以及部署至具有不同維護期間的主機。 完整 [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) 說明保證的 Azure 整體可用性。


## <a name="vm-size"></a>VM 大小
您使用的 VM [大小](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)是由所您要執行的工作負載所決定。 您所選的大小會決定例如處理電源、記憶體和儲存體容量等因素。 Azure 提供了各種不同的大小，以支援許多類型的用法。

Azure 可依據 VM 的大小和作業系統，以[每小時](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)價格方式收費。 針對不足一小時的部分，Azure 只會收取已使用分鐘數的費用。 儲存體是個別定價與收費。

## <a name="vm-limits"></a>VM 限制
您的訂用帳戶都有預設[配額限制](../../azure-resource-manager/management/azure-subscription-service-limits.md)，而此限制會在您要部署多個 VM 以供專案使用時造成影響。 每一訂用帳戶目前的限制是每一區域 20 個 VM。 只要[提出支援票證來要求增加](../../azure-portal/supportability/resource-manager-core-quotas-request.md)，即可提高配額限制

### <a name="operating-system-disks-and-images"></a>作業系統磁碟和映像
虛擬機器是使用[虛擬硬碟 (VHD)](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 來儲存其作業系統 (OS) 和資料。 VHD 也能夠使用於您可以選擇用來安裝 OS 的映像。 

Azure 提供許多 [Marketplace 映像](https://azuremarketplace.microsoft.com/marketplace/apps?filters=virtual-machine-images%3Bwindows&page=1)來與不同版本和類型的 Windows Server 作業系統搭配使用。 Marketplace 映像是依映像發行者、供應項目、SKU 和版本 (版本通常會指定為最新版本) 來識別。 僅支援 64 位元作業系統。 如需所支援客體作業系統、角色和功能的詳細資訊，請參閱 [Microsoft Azure 虛擬機器的 Microsoft 伺服器軟體支援](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)。

下表顯示您可找到映像資訊的一些方法。

| 方法 | 描述 |
| --- | --- |
| Azure 入口網站 |當您選取要使用的影像時，會自動為您指定值。 |
| Azure PowerShell |[Get-AzVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) -Location *location*<BR>[Get-AzVMImageOffer](/powershell/module/az.compute/get-azvmimageoffer) -Location *location* -Publisher *publisherName*<BR>[Get-AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku) -Location *location* -Publisher *publisherName* -Offer *offerName* |
| REST API |[列出映像發行者](/rest/api/compute/platformimages/platformimages-list-publishers)<BR>[列出映像供應項目](/rest/api/compute/platformimages/platformimages-list-publisher-offers)<BR>[列出映像 SKU](/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus) |
| Azure CLI |[az vm image list-publishers](/cli/azure/vm/image?view=azure-cli-latest) --location *location*<BR>[az vm image list-offers](/cli/azure/vm/image?view=azure-cli-latest) --location *location* --publisher *publisherName*<BR>[az vm image list-skus](/cli/azure/vm?view=azure-cli-latest) --location *location* --publisher *publisherName* --offer *offerName*|

您可以選擇[上傳並使用您自己的映像](upload-generalized-managed.md)，當您這麼做時，不會使用發行者名稱、供應項目和 SKU。

### <a name="extensions"></a>延伸模組
VM [擴充](../extensions/features-windows.md?toc=/azure/virtual-machines/windows/toc.json)會透過 post 部署設定及自動化工作為您的 VM 提供其他功能。

可以使用擴充功能來完成這些常見工作︰

* **執行自訂指令碼** – [自訂指令碼延伸模組](../extensions/custom-script-windows.md?toc=/azure/virtual-machines/windows/toc.json)可藉由當佈建 VM 時執行您的指令碼來協助您在 VM 上設定工作負載。
* **部署和管理組態** – [PowerShell 期望狀態組態 (DSC) 延伸模組](../extensions/dsc-overview.md?toc=/azure/virtual-machines/windows/toc.json)可協助您設定 VM 上的 DSC 來管理組態和環境。
* **收集診斷資料** – [Azure 診斷擴充功能](../extensions/diagnostics-template.md?toc=/azure/virtual-machines/windows/toc.json)可協助您設定 VM 來收集診斷資料，用來監視您應用程式的健全狀況。

### <a name="related-resources"></a>相關資源
此資料表中的資源可供 VM 使用，且建立 VM 時必須存在或建立。

| 資源 | 必要 | 描述 |
| --- | --- | --- |
| [資源群組](../../azure-resource-manager/management/overview.md) |是 |VM 必須包含在資源群組中。 |
| [儲存體帳戶](../../storage/common/storage-account-create.md) |是 |VM 需要儲存體帳戶儲存其虛擬硬碟。 |
| [虛擬網路](../../virtual-network/virtual-networks-overview.md) |是 |VM 必須是虛擬網路的成員。 |
| [公用 IP 位址](../../virtual-network/public-ip-addresses.md) |否 |可以有公用 IP 位址指派給 VM，以從遠端存取它。 |
| [網路介面](../../virtual-network/virtual-network-network-interface.md) |是 |VM 需要網路介面以在網路中進行通訊。 |
| [資料磁碟](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |否 |VM 可以包含資料磁碟來擴充儲存體功能。 |

## <a name="next-steps"></a>後續步驟

建立您的第一個 VM！

- [入口網站](quick-create-portal.md)
- [PowerShell](quick-create-powershell.md)
- [Azure CLI](quick-create-cli.md)
