---
title: 使用共用資源庫共用 VM 映像
description: 瞭解如何使用共用映射資源庫，在您的組織中共用 Linux VM 映射。
author: axayjo
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 10/14/2020
ms.author: akjosh
ms.reviewer: cynthn
ms.openlocfilehash: 2731a7f263db223c258ddfc434bc84f59632d1f5
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98223358"
---
# <a name="shared-image-galleries-overview"></a>共用映像資源庫概觀

共用映像庫服務可協助您圍繞映像來建置結構和組織。 共用映像資源庫提供：

- 映像的全域複寫。
- 對映像進行版本控制和分組，以便管理。
- 在支援可用性區域的區域中，具有區域備援儲存體 (ZRS) 帳戶的高可用性映像。 ZRS 針對區域性失敗提供更佳的復原能力。
- 進階儲存體支援 (Premium_LRS)。
- 使用 RBAC 在訂用帳戶之間共用，甚至是在 Active Directory (AD) 租用戶之間共用。
- 使用每個區域中的映像複本來調整您的部署。

使用共用映像庫，即可在組織內對於不同的使用者、服務主體或 AD 群組共用您的映像。 共用映像可以複寫至多個區域，以更快速調整您的部署。

根據映像的建立方式，映像可能是完整 VM (包括任何連結的資料磁碟) 或僅含 OS 磁碟的複本。 當您從映像建立 VM 時，映像中 VHD 的複本可用來建立新 VM 的磁碟。 映像會保留在儲存體中，而且可以一再用來建立新的 VM。

如果您有大量需要維護的映像，而且想要提供給全公司使用時，便可使用共用映像庫作為存放庫。 

共用映像庫具有多個資源類型：

| 資源 | 描述|
|----------|------------|
| **映像來源** | 這是資源，可用來在映像庫中建立 **映像版本**。 映像來源可以是現有的 Azure VM，其為[一般化或特殊化](#generalized-and-specialized-images)、受控映像、快照集、VHD 或另一個映像庫中的映像版本。 |
| **映像庫** | 和 Azure Marketplace 一樣，**映像庫** 是用於管理和共用映像的存放庫，但您可以控制哪些使用者能夠存取。 |
| **映像定義** | 映像定義會在資源庫內建立，並帶有映像資訊以及在內部使用時所需滿足的需求。 這包括映像是 Windows 還是 Linux、版本資訊以及最小和最大的記憶體需求。 這是映像類型的定義。 |
| **映像版本** | **映像版本** 是在使用資源庫時用來建立 VM 的項目。 您可以視需要為環境準備多個映像版本。 和受控映像一樣，當您使用 **映像版本** 來建立 VM 時，系統會使用映像版本來建立 VM 的新磁碟。 映像版本可以使用多次。 |

<br>

![圖形：顯示如何在資源庫中備有多個映像版本](./media/shared-image-galleries/shared-image-gallery.png)

## <a name="image-definitions"></a>映像定義

映像定義是映像版本的邏輯群組。 映像定義包含建立映像的原因、目標作業系統，以及使用映像的其他相關資訊。 映像定義就像是關於建立特定映像的所有詳細資料計劃。 您不會從映像定義部署 VM，而是從定義所建立的映像版本。

每個映像定義都有三個參數，組合使用：**發行者**、**供應項目** 和 **SKU**。 這些項目是用來尋找特定的映像定義。 不同的映像版本之間可以擁有一或兩個相同的值，但不能三個值都相同。  例如，以下是三個映像定義和其值：

|映像定義|發行者|供應項目|SKU|
|---|---|---|---|
|myImage1|Contoso|財務|後端|
|myImage2|Contoso|財務|前端|
|myImage3|測試|財務|前端|

這三個映像定義都擁有唯一的值組。 格式類似於您目前可以如何在 Azure PowerShell 中針對 [Azure Marketplace 映像](./windows/cli-ps-findimage.md)指定發行者、供應項目和 SKU，以取得最新版本的 Marketplace 映像。 每個映像定義都需要有由這些值組成的唯一值組。

下列參數會決定其可以包含哪些類型的映像版本：

- 作業系統狀態 - 您可以將 OS 狀態設定為[一般化或特殊化](#generalized-and-specialized-images)。 此為必要欄位。
- 作業系統 - 可以是 Windows 或 Linux。 此為必要欄位。
-   Hyper-V 世代 - 指定映像是從第 1 代還是[第 2 代](generation-2.md) Hyper-V VHD 建立。 預設值為第 1 代。


以下是可在映像定義上設定的其他參數，讓您可以更輕鬆地追蹤您的資源：

- 描述 - 使用描述以提供有關映像定義為何存在的詳細資訊。 例如，您的前端伺服器可能會有預先安裝應用程式的映像定義。
- Eula - 可用來指向映像定義特有的使用者授權合約。
- 隱私權聲明和版本資訊 - 將版本資訊和隱私權聲明儲存在 Azure 儲存體中，並提供 URI 供您作為映像定義的一部分來存取這些聲明。
- 生命週期結束日期 - 將生命週期結束日期附加至映像定義，以便使用自動化來刪除舊的映像定義。
- 標記 - 您可以在建立映像定義時新增標記。 如需標記的詳細資訊，請參閱[使用標記來組織您的資源](../azure-resource-manager/management/tag-resources.md)
- vCPU 和記憶體最小值和最大值建議 - 如果您的映像有 vCPU 和記憶體建議，您可以將該資訊附加至映像定義。
- 不允許的磁碟類型 - 您可以提供 VM 儲存體需求的相關資訊。 例如，如果映像不適合標準 HDD 磁碟，您可以將其新增至不允許清單。
- Marketplace 映像的購買方案資訊 - `-PurchasePlanPublisher`、`-PurchasePlanName`和 `-PurchasePlanProduct`。 如需購買方案資訊的詳細資訊，請參閱[在 Azure Marketplace 中尋找映像](./windows/cli-ps-findimage.md)和[在建立映像時提供 Azure Marketplace 購買方案資訊](marketplace-images.md)。


## <a name="image-versions"></a>映像版本

**映像版本** 是您用來建立 VM 的版本。 您可以視需要為環境準備多個映像版本。 當您使用 **映像版本** 來建立 VM 時，系統會使用映像版本來建立 VM 的新磁碟。 映像版本可以使用多次。

映像版本的屬性如下：

- 版本號碼。 這會用作映像版本的名稱。 其格式一律如下：MajorVersion.MinorVersion.Patch。 若您指定在建立 VM 時使用 **最新**，系統會依序根據最高的 MajorVersion、MinorVersion、Patch 來選擇最新的映像。 
- 來源。 來源可以是 VM、受控磁碟、快照集、受控映像或其他映像版本。 
- 從最新中排除。 您可以保留某個版本，以免用作最新的映像版本。 
- 生命週期結束日期。 在此日期之後，就無法從這個映像建立 VM。


## <a name="generalized-and-specialized-images"></a>一般化和特殊化映像

共用映像庫支援兩種作業系統狀態。 映像通常需要用來建立映像的 VM 已一般化，然後才會接受映像。 一般化是從 VM 中移除電腦和使用者特定資訊的程序。 針對 Windows，會使用 Sysprep 工具。 針對 Linux，您可以使用 [waagent](https://github.com/Azure/WALinuxAgent) `-deprovision` 或 `-deprovision+user` 參數。

特殊化 VM 尚未經過移除電腦特定資訊和帳戶的程序。 此外，從特殊化映像建立的 VM 沒有相關聯的 `osProfile`。 這表示除了一些優點以外，特殊化映像也會有一些限制。

- 從特殊化映像建立的 VM 和擴展集，可以更快速地啟動並執行。 因為是從已通過第一次開機的來源所建立，所以從這些映像建立的 VM 開機更快速。
- 可以用來登入 VM 的帳戶也可用於使用從該 VM 建立特殊化映像所建立的任何 VM。
- VM 將會擁有從中取得映像的 VM **電腦名稱**。 您應該變更電腦名稱以避免發生衝突。
- `osProfile` 是使用 `secrets` 將一些敏感性資訊傳遞至 VM 的方式。 這可能會導致使用 KeyVault、WinRM 和其他使用 `osProfile`中 `secrets` 功能的問題。 在某些情況下，您可以使用受管理的服務識別 (MSI) 來解決這些限制。

## <a name="regional-support"></a>區域支援

所有公用區域都可以是目標區域，但是若要複寫至澳大利亞中部和澳大利亞中部 2，您需要將訂用帳戶新增至允許清單。 若要要求將訂用帳戶新增至允許清單，請移至： https://azure.microsoft.com/global-infrastructure/australia/contact/

## <a name="limits"></a>限制 

使用共用映射庫部署資源時，每個訂用帳戶都有限制：
- 每個區域中每個訂用帳戶 100 個共用映像庫
- 每個區域中每個訂用帳戶 1000 個映像定義
- 每個區域中每個訂用帳戶 10000 個映像版本
- 每個區域中每個訂用帳戶 10 個映像版本複本
- 連結至映像的任何磁碟大小都必須小於或等於 1TB

如需詳細資訊，請參閱[根據限制檢查資源使用量](../networking/check-usage-against-limits.md)，取得如何檢查目前使用量的範例。
 
## <a name="scaling"></a>調整大小
共用映像庫可讓您指定要讓 Azure 保留的映像複本數目。 這對於有多個 VM 的部署案例很有幫助，因為 VM 部署可以分散到不同複本，減少執行個體建立程序由於單一複本多載而遭到節流的機會。

使用共用映像庫，您現在可以在虛擬機器擴展集中部署最多 1000 個 VM 執行個體 (從 600 個開始使用受控映像)。 映像複本提供更佳的部署效能、可靠性和一致性。   您可以根據區域的規模需求，在每個目標區域中設定不同的複本計數。 由於每個複本都是您映像的深層複本，因此可協助您使用每個額外的複本，以線性方式調整您的部署。 雖然我們了解映像或區域各不相同，但是以下是如何在區域中使用複本的一般指導方針：

- 針對非虛擬機器擴展集部署-針對您同時建立的每20個 Vm，建議您保留一個複本。 例如，如果您在區域中使用相同映像同時建立 120 個 VM，建議您至少保留 6 個映像複本。 
- 針對虛擬機器擴展集部署-針對具有最多600實例的每個擴展集部署，建議您保留至少一個複本。 例如，如果您在單一區域中使用相同映像同時建立 5 個擴展集，每個擴展集都具有 600 個 VM 執行個體，建議您至少保留 5 個映像複本。 

由於映像大小、內容和 OS 類型等因素，我們一律建議您過度佈建複本數目。

![圖形：顯示如何調整映像](./media/shared-image-galleries/scaling.png)

## <a name="make-your-images-highly-available"></a>讓您的映像保持高可用性

[Azure 區域備援儲存體 (ZRS)](https://azure.microsoft.com/blog/azure-zone-redundant-storage-in-public-preview/) 可針對區域中的可用性區域失敗提供復原能力。 透過共用映像庫的正式發行，您可以選擇將映像儲存在區域 (具有可用性區域) 中的 ZRS 帳戶。 

您也可以針對每個目標區域選擇帳戶類型。 預設儲存體帳戶類型為 Standard_LRS，但是您可以為具有可用性區域的區域選擇 Standard_ZRS。 請在[這裡](../storage/common/storage-redundancy.md)檢查 ZRS 的區域可用性。

![顯示 ZRS 的圖形](./media/shared-image-galleries/zrs.png)

## <a name="replication"></a>複寫
共用映像庫也可讓您自動地將映像複寫到其他 Azure 區域。 每個共用映像版本皆可複寫到不同區域，實際情形取決於何者對組織有利。 舉例來說，一律將最新映像複寫到多個區域，而讓所有較舊的版本只留在 1 個區域中。 這有助於節省共用映像版本的儲存體成本。 

作為共用映像版本複寫目的地的區域可在共用映像版本建立之後更新。 複寫到不同區域所需的時間取決於所複製的資料量，以及作為版本複寫目的地的區域數目。 在某些情況下，這可能需要幾小時的時間。 複寫進行期間，您可以檢視每個區域的複寫狀態。 某個區域完成映像複寫後，您就可以在該區域使用該映像版本部署 VM 或擴展集。

![圖形：顯示如何複寫映像](./media/shared-image-galleries/replication.png)

## <a name="access"></a>存取

由於共用映像庫、映像定義和映像版本都是資源，所以您可以使用內建的原生 Azure RBAC 控制來共用。 使用 RBAC 時，您可以將這些資源共用給其他使用者、服務主體和群組。 您甚至可以與在其建立所在租用戶外部的個人共用存取權。 一旦使用者擁有共用映像版本的存取權，他們就可以部署 VM 或虛擬機器擴展集。  以下共用矩陣可協助您了解使用者有權存取的項目：

| 與使用者共用     | 共用映像庫 | 映像定義 | 映像版本 |
|----------------------|----------------------|--------------|----------------------|
| 共用映像庫 | 是                  | 是          | 是                  |
| 映像定義     | 否                   | 是          | 是                  |

建議您在資源庫層級共用，以獲得最佳體驗。 我們不建議您共用個別的映像版本。 如需 RBAC 的詳細資訊，請參閱[使用 RBAC 管理對 Azure 資源的存取](../role-based-access-control/role-assignments-portal.md)。

您也可以使用多租用戶應用程式註冊，大規模地在租用戶之間共用映像。 如需有關跨租使用者共用映射的詳細資訊，請參閱使用 [Azure CLI](./linux/share-images-across-tenants.md) 或 [PowerShell](./windows/share-images-across-tenants.md)的「跨 Azure 租使用者共用資源庫 VM 映射」。

## <a name="billing"></a>計費
使用共用映像庫服務不會有額外費用。 您只會支付下列資源的費用：
-   儲存每個複本的儲存成本。 儲存體成本是以快照集的形式計費，並以映像版本的已佔用大小、複本數目，以及複寫版本的區域數目為基礎。 
-   第一個映像版本從來源區域複寫至要複寫區域的網路輸出費用。 後續複本會在區域內處理，因此不會產生額外費用。 

例如，假設您有一個 127 GB OS 磁碟的映像，只佔用10 GB 的儲存空間，還有一個空的 32 GB 資料磁碟。 每個映像的已佔用大小只會是 10 GB。 映像會複寫至 3 個區域，而每個區域都有兩個複本。 總共會有六個快照集，每個都使用 10 GB。 系統會根據 10 GB 的所占大小，向您收取每個快照集的儲存成本。 您需支付第一個複本複製到其他兩個區域的網路輸出費用。 如需有關每個區域中快照集定價的詳細資訊，請參閱 [受控磁碟定價](https://azure.microsoft.com/pricing/details/managed-disks/)。 如需網路輸出詳細資訊，請參閱 [頻寬定價](https://azure.microsoft.com/pricing/details/bandwidth/)。


## <a name="updating-resources"></a>更新資源

建立之後，您就可以對映像資源庫資源進行一些變更。 這些項目限制為：
 
共用映像庫：
- 描述

映像定義：
- 建議的 vCPU
- 建議的記憶體
- 描述
- 生命週期結束日期

映像版本：
- 區域複本計數
- 目標區域
- 從最新項目中排除
- 生命週期結束日期

## <a name="sdk-support"></a>SDK 支援

下列 SDK 支援建立共用映像資源庫：

- [.NET](/dotnet/api/overview/azure/virtualmachines/management?view=azure-dotnet)
- [Java](/java/azure/?view=azure-java-stable)
- [Node.js](/javascript/api/@azure/arm-compute)
- [Python](/python/api/overview/azure/virtualmachines?view=azure-python)
- [Go](/azure/go/)

## <a name="templates"></a>範本

您可以使用範本建立共用映像庫資源。 有數個 Azure 快速入門範本可以使用： 

- [建立共用映像資源庫](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [在共用映像資源庫中建立映像定義](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [在共用映像資源庫中建立映像版本](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [從映像版本建立 VM](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

## <a name="frequently-asked-questions"></a>常見問題集 

* [如何列出訂用帳戶之間的所有共用映像庫資源？](#how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions) 
* [是否可以將現有映像移至共用映像庫？](#can-i-move-my-existing-image-to-the-shared-image-gallery)
* [是否可以從特殊化磁碟建立映像版本？](#can-i-create-an-image-version-from-a-specialized-disk)
* [共用映像庫資源是否可以在建立後移至不同訂用帳戶？](#can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created)
* [是否可以跨雲端 (例如，Azure China 21Vianet、Azure 德國和 Azure Government 雲端) 複寫映像版本？](#can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud)
* [是否可以跨訂用帳戶複寫映像版本？](#can-i-replicate-my-image-versions-across-subscriptions)
* [是否可以跨 Azure AD 租用戶共用映像版本？](#can-i-share-image-versions-across-azure-ad-tenants)
* [跨目標區域複寫映像版本需要多久時間？](#how-long-does-it-take-to-replicate-image-versions-across-the-target-regions)
* [來源區域和目標區域有何差異？](#what-is-the-difference-between-source-region-and-target-region)
* [如何在建立映像版本時指定來源區域？](#how-do-i-specify-the-source-region-while-creating-the-image-version)
* [如何指定要在每個區域中建立的映像版本複本數目？](#how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region)
* [是否可以在用於映像定義和映像版本以外的位置建立共用映像庫？](#can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version)
* [使用共用映像庫需要哪些費用？](#what-are-the-charges-for-using-the-shared-image-gallery)
* [我應該使用哪些 API 版本來建立共用映像庫、映像定義和映像版本？](#what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version)
* [我應該使用哪些 API 版本來建立映像版本的共用 VM 或虛擬機器擴展集？](#what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version)
* [是否可以使用受控映像將建立的虛擬機器擴展集更新為使用共用映像庫映像？](#can-i-update-my-virtual-machine-scale-set-created-using-managed-image-to-use-shared-image-gallery-images)

### <a name="how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions"></a>如何列出訂用帳戶之間的所有共用映像庫資源？

若要針對您可在 Azure 入口網站上存取的訂用帳戶，列出其彼此之間的所有共用映像庫資源，請遵循下列步驟：

1. 開啟 [Azure 入口網站](https://portal.azure.com)。
1. 向下捲動頁面，然後選取 [所有資源]。
1. 選取所有要列出其內含全部資源的訂用帳戶。
1. 尋找類型為 [共用映像庫] 的資源。
  
若要列出您有權存取之所有訂用帳戶的共用映像庫資源，請在 Azure CLI 中使用下列命令：

```azurecli
   az account list -otsv --query "[].id" | xargs -n 1 az sig list --subscription
```

如需詳細資訊，請參閱使用 [Azure CLI](update-image-resources-cli.md) 或 [PowerShell](update-image-resources-powershell.md) **管理資源庫資源**。

### <a name="can-i-move-my-existing-image-to-the-shared-image-gallery"></a>是否可以將現有映像移至共用映像庫？
 
是。 根據您可能擁有的映像類型，案例共有 3 種。

 案例 1：如果您擁有受控映像，則可以透過該映像建立映像定義和映像版本。 如需詳細資訊，請參閱使用 [Azure CLI](image-version-managed-image-cli.md) 或 [PowerShell](image-version-managed-image-powershell.md) **從受控映像遷移至映像版本**。

 案例 2：如果您擁有非受控映像，則可以透過該映像建立受控映像，繼而建立映像定義和映像版本。 

 案例 3：如果您在本機檔案系統擁有 VHD，則需要上傳 VHD 至受控映像，然後就可以從中建立映像定義和映像版本。

- 如果是 Windows VM 的 VHD，請參閱[上傳 VHD](./windows/upload-generalized-managed.md)。
- 如果是 Linux VM 的 VHD，請參閱[上傳 VHD](./linux/upload-vhd.md#option-1-upload-a-vhd)

### <a name="can-i-create-an-image-version-from-a-specialized-disk"></a>是否可以從特製化磁碟建立映像版本？

是的，您可以使用 [CLI](vm-specialized-image-version-cli.md)、[PowerShell](vm-specialized-image-version-powershell.md) 或 API，從特殊化映像建立 VM。 

### <a name="can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created"></a>共用映像庫資源是否可以在建立後移至不同訂用帳戶？

否，無法將共用映像庫資源移至不同訂用帳戶。 您可以使用 [Azure CLI](image-version-another-gallery-cli.md) 或 [PowerShell](image-version-another-gallery-powershell.md)，將資源庫中的映像版本複寫到其他區域，或從另一個資源庫複製映像。

### <a name="can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud"></a>是否可以跨雲端 (例如，Azure China 21Vianet、Azure 德國和 Azure Government 雲端) 複寫映像版本？

否，無法跨雲端複寫映像版本。

### <a name="can-i-replicate-my-image-versions-across-subscriptions"></a>是否可以跨訂用帳戶複寫映像版本？

否，但可以在訂用帳戶中跨區域複寫映像版本，然後透過 RBAC 在其他訂用帳戶中使用。

### <a name="can-i-share-image-versions-across-azure-ad-tenants"></a>是否可以跨 Azure AD 租用戶共用映像版本？ 

是，您可以使用 RBAC 來與租用戶之間的個人共用。 但是若要大規模共用，請參閱使用 [PowerShell](./windows/share-images-across-tenants.md) 或 [CLI](./linux/share-images-across-tenants.md)「在 Azure 租用戶之間共用資源庫映像」。

### <a name="how-long-does-it-take-to-replicate-image-versions-across-the-target-regions"></a>跨目標區域複寫映像版本需要多久時間？

映像版本的複寫時間完全取決於映像大小以及作為其複寫目的地的區域數目。 不過，建議您最好讓映像小一點，並讓來源和目標區域近一點以獲得最佳結果。 您可以使用 -ReplicationStatus 旗標檢查複寫的狀態。

### <a name="what-is-the-difference-between-source-region-and-target-region"></a>來源區域和目標區域有何差異？

來源區域是要建立映像版本的區域，目標區域則是要儲存映像版本複本的區域。 每個映像版本只能有一個來源區域。 此外，在建立映像版本時，請務必將來源區域位置傳遞作為其中一個目標區域。

### <a name="how-do-i-specify-the-source-region-while-creating-the-image-version"></a>如何在建立映像版本時指定來源區域？

在建立映像版本時，您可以使用 **--location** 標籤 (在 CLI 中) 以及 **-Location** 標籤 (在 PowerShell 中) 來指定來源區域。 請確定用來作為基底映像以供建立映像版本的受控映像所在位置，與您想要在其中建立映像版本的位置相同。 此外，在建立映像版本時，請務必將來源區域位置傳遞作為其中一個目標區域。  

### <a name="how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region"></a>如何指定要在每個區域中建立的映像版本複本數目？

有兩種方式可供指定要在每個區域中建立的映像版本複本數目：
 
1. 區域複本計數，用以指定要在每個區域建立的複本數目。 
2. 一般複本計數，這是未指定區域複本計數時，每個區域計數的預設值。 

若要指定區域複本計數，請傳遞位置以及要在該區域建立的複本數目：“美國中南部=2”。 

如果未對每個位置指定區域複本計數，則複本的預設數目會是所指定的一般複本計數。 

若要在 CLI 中指定一般複本計數，請在 `az sig image-version create` 命令中使用 **--replica-count** 引數。

### <a name="can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version"></a>是否可以在用於映像定義和映像版本以外的位置建立共用映像庫？

是，可以的。 但建議您最好讓資源群組、共用映像庫、映像定義和映像版本位於相同位置。

### <a name="what-are-the-charges-for-using-the-shared-image-gallery"></a>使用共用映像庫需要哪些費用？

除了用於儲存映像版本的儲存體費用，以及用於將映像版本從來源區域複寫到目標區域的網路輸出費用外，使用共用映像庫服務不會再有任何費用。

### <a name="what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version"></a>我應該使用哪些 API 版本來建立共用映像庫、映像定義和映像版本？

若要處理共用映像庫、映像定義及映像版本，建議您使用 API 版本 2018-06-01。 區域備援儲存體 (ZRS) 需要 2019-03-01 版或更新版本。

### <a name="what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version"></a>我應該使用哪些 API 版本來建立映像版本的共用 VM 或虛擬機器擴展集？

對於使用映像版本來部署 VM 和虛擬機器擴展集，建議您使用 API 版本 2018-04-01 或更高版本。

### <a name="can-i-update-my-virtual-machine-scale-set-created-using-managed-image-to-use-shared-image-gallery-images"></a>是否可以使用受控映像將建立的虛擬機器擴展集更新為使用共用映像庫映像？

是，您可以將擴展集映像參考從受控映像更新為共用映像庫映像，只要映像之間的 OS 類型、Hyper-V 產生和資料磁碟版面配置相符即可。

## <a name="troubleshoot-shared-image-gallery-issues"></a>針對共用映射庫問題進行疑難排解
如果您在共用映射庫資源上執行任何作業時遇到問題，請參閱 [疑難排解指南](troubleshooting-shared-images.md)中的常見錯誤清單。

此外，您可以 `azure-virtual-machines-images` 在 [Q&A](/answers/topics/azure-virtual-machines-images.html)張貼和標記問題。

## <a name="next-steps"></a>後續步驟

瞭解如何使用 [Azure CLI](shared-images-cli.md) 或 [PowerShell](shared-images-powershell.md)部署共用映射。

