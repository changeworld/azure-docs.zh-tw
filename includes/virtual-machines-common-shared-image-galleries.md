---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 05/06/2019
ms.author: akjosh
ms.custom: include file
ms.openlocfilehash: a477114bda7d138a6860d21f2fad75e27d968833
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80116925"
---
共用映射庫是一項服務，可説明您圍繞託管映射構建結構和組織。 共用圖像庫提供：

- 映射的託管全域複製。
- 圖像的版本和分組，便於管理。
- 支援可用性區域的區域具有區域冗余存儲 （ZRS） 帳戶的高可用性映射。 ZRS 具有更好的抵禦區域故障的彈性。
- 使用 RBAC 在訂閱之間，甚至在活動目錄 （AD） 租戶之間共用。
- 使用每個區域中的映射副本擴展部署。

使用共用映像庫，即可在組織內對於不同的使用者、服務主體或 AD 群組共用您的映像。 共用映像可以複寫至多個區域，以更快速調整您的部署。

根據映像的建立方式，受控映像可能是完整 VM (包括任何連結的資料磁碟) 或僅含 OS 磁碟的複本。 當您從映像建立 VM 時，映像中 VHD 的複本可用來建立新 VM 的磁碟。 受控映像會保留在儲存體中，而且可以一再用來建立新的 VM。

如果您有大量需要維護的託管映射，並希望在整個公司中提供這些映射，則可以使用共用映射庫作為存儲庫，以便輕鬆共用映射。 

共用映像庫具有多個資源類型：

| 資源 | 描述|
|----------|------------|
| **託管映射** | 可單獨使用或用於在圖像庫中創建**圖像版本**的基本映射。 託管映射是從[通用](#generalized-and-specialized-images)VM 創建的。 受控映像是一種特殊的 VHD 類型，可用來產生多個 VM，現在可以用來建立共用映像版本。 |
| **快照** | 可用於製作**圖像版本的**VHD 的副本。 快照可以從[專用](#generalized-and-specialized-images)VM（尚未通用的 VM）中拍攝，然後單獨使用或與資料磁片的快照一起使用，以創建專用映射版本。
| **圖像庫** | 和 Azure Marketplace 一樣，**映像庫**是用於管理和共用映像的存放庫，但您可以控制哪些使用者能夠存取。 |
| **映像定義** | 圖像在庫中定義，並包含有關圖像的資訊以及組織內使用它的要求。 您可以包括映射是通用化還是專用化、作業系統、最小和最大記憶體要求以及版本資訊等資訊。 這是映像類型的定義。 |
| **映像版本** | **映像版本**是在使用資源庫時用來建立 VM 的項目。 您可以視需要為環境準備多個映像版本。 和受控映像一樣，當您使用**映像版本**來建立 VM 時，系統會使用映像版本來建立 VM 的新磁碟。 映像版本可以使用多次。 |

<br>

![圖形：顯示如何在資源庫中備有多個映像版本](./media/shared-image-galleries/shared-image-gallery.png)

## <a name="image-definitions"></a>圖像定義

圖像定義是圖像版本的邏輯分組。 圖像定義包含有關創建映射的原因、映射的作業系統以及有關使用映射的資訊。 圖像定義類似于有關創建特定圖像的所有詳細資訊的計畫。 您不會從映射定義部署 VM，而是從從定義創建的圖像版本部署 VM。

每個圖像定義有三個參數，它們組合使用 -**發行者**、**優惠**和**SKU**。 這些用於查找特定的圖像定義。 不同的映像版本之間可以擁有一或兩個相同的值，但不能三個值都相同。  例如，以下是三個映像定義和其值：

|映像定義|發行者|供應項目|SKU|
|---|---|---|---|
|myImage1|Contoso|財務|後端|
|myImage2|Contoso|財務|前端|
|myImage3|測試|財務|前端|

這三個映像定義都擁有唯一的值組。 該格式類似于當前在 Azure PowerShell 中為[Azure 應用商店映射](../articles/virtual-machines/windows/cli-ps-findimage.md)指定發行者、產品/服務和 SKU 以獲取應用商店映射的最新版本的方式。 每個圖像定義都需要有一組唯一的這些值。

以下是可以在圖像定義上設置的其他參數，以便您可以更輕鬆地跟蹤資源：

* 作業系統狀態 - 您可以將作業系統狀態設置為[通用或專用](#generalized-and-specialized-images)。
* 作業系統 - 可以是 Windows 或 Linux。
* 說明 - 使用說明提供有關圖像定義存在原因的更多詳細資訊。 例如，對於預先安裝應用程式的前端伺服器，可能具有映射定義。
* Eula - 可用於指向特定于映射定義的使用者授權合約。
* 隱私權聲明和發佈說明 - 在 Azure 存儲中存儲發佈說明和隱私權聲明，並提供 URI 以作為映射定義的一部分訪問它們。
* 壽命終止日期 - 將使用壽命終止日期附加到圖像定義，以便能夠使用自動化刪除舊圖像定義。
* 標記 - 您可以在創建圖像定義時委任標記。 有關標記的詳細資訊，請參閱[使用標記來組織資源](../articles/azure-resource-manager/management/tag-resources.md)
* 最小和最大 vCPU 和記憶體建議 - 如果映射具有 vCPU 和記憶體建議，則可以將該資訊附加到圖像定義。
* 不允許的磁片類型 - 您可以提供有關 VM 的存儲需求的資訊。 例如，如果映射不適合標準 HDD 磁片，則可以將它們添加到不允許清單中。

## <a name="generalized-and-specialized-images"></a>通用和專用圖像

共用映射庫支援兩種作業系統狀態。 通常，映射要求在拍攝映射之前對用於創建映射的 VM 進行通用化。 通用化是從 VM 中刪除電腦和使用者特定資訊的過程。 對於 Windows，也使用 Sysprep。 對於 Linux，您可以使用[waagent](https://github.com/Azure/WALinuxAgent) `-deprovision` `-deprovision+user`或參數。

專用 VM 尚未通過刪除電腦特定資訊和帳戶的過程。 此外，從專用映射創建的 VM 沒有與其關聯的`osProfile`VM。 這意味著專用映射將具有一些限制。

- 可用於登錄到 VM 的帳戶也可用於使用從該 VM 創建的專用映射創建的任何 VM。
- VM 將具有映射從中獲取的 VM**的電腦名稱稱**。 應更改電腦名稱稱以避免衝突。
- 是`osProfile`使用`secrets`傳遞給 VM 的一些敏感資訊的方式。 這可能會導致使用 KeyVault、WinRM 和 在 中使用`secrets`的其他功能`osProfile`的問題。 在某些情況下，您可以使用託管服務標識 （MSI） 來解決這些限制。

> [!IMPORTANT]
> 專用圖像當前處於公共預覽中。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>
> **已知的預覽限制**VM 只能使用門戶或 API 從專用映射創建。 不支援 CLI 或 PowerShell 預覽。


## <a name="regional-support"></a>區域支援

源區域列在下表中。 所有公共區域都可以是目的地區域，但要複製到澳大利亞中部和澳大利亞中部 2，您需要將訂閱列入白名單。 若欲要求加入允許清單，請移至：https://azure.microsoft.com/global-infrastructure/australia/contact/


| 源區域        |                   |                    |                    |
| --------------------- | ----------------- | ------------------ | ------------------ |
| 澳大利亞中部     | 中國東部        | 印度南部        | 西歐        |
| 澳大利亞中部 2   | 中國東部 2      | 東南亞     | 英國南部           |
| 澳大利亞東部        | 中國北部       | 日本東部         | 英國西部            |
| 澳大利亞東南部   | 中國北部 2     | 日本西部         | US DoD 中部     |
| 巴西南部          | 東亞         | 南韓中部      | US DoD 東部        |
| 加拿大中部        | 美國東部           | 南韓南部        | US Gov 亞利桑那州     |
| 加拿大東部           | 美國東部 2         | 美國中北部   | US Gov 德克薩斯州       |
| 印度中部         | 美國東部 2 EUAP    | 北歐       | US Gov 維吉尼亞州    |
| 美國中部            | 法國中部    | 美國中南部   | 印度西部         |
| 美國中部 EUAP       | 法國南部      | 美國中西部    | 美國西部            |
|                       |                   |                    | 美國西部 2          |



## <a name="limits"></a>限制 

使用共用映射庫部署資源（每個訂閱）有限制：
- 每個區域每個訂閱 100 個共用圖像庫
- 每個區域每個訂閱 1，000 個圖像定義
- 每個區域每個訂閱 10，000 個映射版本
- 連接到映射的任何磁片的大小必須小於或等於 1TB

有關詳細資訊，請參閱[根據限制檢查資源使用方式](https://docs.microsoft.com/azure/networking/check-usage-against-limits)，瞭解有關如何檢查當前使用方式的示例。
 
## <a name="scaling"></a>調整大小
共用映像庫可讓您指定要讓 Azure 保留的映像複本數目。 這對於有多個 VM 的部署案例很有幫助，因為 VM 部署可以分散到不同複本，減少執行個體建立程序由於單一複本多載而遭到節流的機會。

使用共用映射庫，您現在可以在虛擬機器縮放集中部署多達 1，000 個 VM 實例（從 600 個具有託管映射的實例增加到 600 個）。 映射副本提供更好的部署性能、可靠性和一致性。 您可以根據區域的規模需求在每個目的地區域中設置不同的副本計數。 由於每個副本都是映射的深層副本，這有助於使用每個額外的副本線性擴展部署。 雖然我們知道沒有兩個圖像或區域是相同的，但我們關於如何在區域中使用副本的一般準則如下：

- 對於非虛擬機器擴展集 （VMSS） 部署 - 對於同時創建的 20 個 VM，我們建議您保留一個副本。 例如，如果您在區域中同時使用同一映射創建 120 個 VM，我們建議您保留至少 6 個映射副本。 
- 對於虛擬機器規模集 （VMSS） 部署 - 對於最多 600 個實例的每個規模集部署，我們建議您至少保留一個副本。 例如，如果要同時創建 5 個比例集，每個比例集在單個區域中使用同一映射，每個比例集有 600 個 VM 實例，我們建議您保留至少 5 個映射副本。 

由於圖像大小、內容和作業系統類型等因素，我們始終建議您過度預配副本的數量。

![圖形：顯示如何調整映像](./media/shared-image-galleries/scaling.png)

## <a name="make-your-images-highly-available"></a>使圖像高度可用

[Azure 區域冗余存儲 （ZRS）](https://azure.microsoft.com/blog/azure-zone-redundant-storage-in-public-preview/)提供針對該區域可用性區域故障的恢復能力。 使用共用映射庫的一般可用性，您可以選擇將圖像存儲在具有可用區域的區域的 ZRS 帳戶中。 

您還可以為每個目的地區域選擇帳戶類型。 預設存儲帳戶類型為 Standard_LRS，但您可以選擇具有可用區域的區域Standard_ZRS。 [在此處](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs)查看 ZRS 的區域可用性。

![顯示 ZRS 的圖形](./media/shared-image-galleries/zrs.png)

## <a name="replication"></a>複寫
共用映像庫也可讓您自動地將映像複寫到其他 Azure 區域。 每個共用映像版本皆可複寫到不同區域，實際情形取決於何者對組織有利。 舉例來說，一律將最新映像複寫到多個區域，而讓所有較舊的版本只留在 1 個區域中。 這有助於節省共用映像版本的儲存體成本。 

作為共用映像版本複寫目的地的區域可在共用映像版本建立之後更新。 複寫到不同區域所需的時間取決於所複製的資料量，以及作為版本複寫目的地的區域數目。 在某些情況下，這可能需要幾小時的時間。 複寫進行期間，您可以檢視每個區域的複寫狀態。 在區域中完成映射複製後，可以使用該區域中的映射版本部署 VM 或縮放集。

![圖形：顯示如何複寫映像](./media/shared-image-galleries/replication.png)

## <a name="access"></a>存取

由於共用映射庫、映射定義和映射版本都是資源，因此可以使用內置本機 Azure RBAC 控制項共用它們。 使用 RBAC，您可以將這些資源分享給其他使用者、服務主體和組。 您甚至可以共用對在租戶中創建的租戶外部的個人的存取權限。 使用者訪問共用映射版本後，可以部署 VM 或虛擬機器縮放集。  以下共用矩陣可協助您了解使用者有權存取的項目：

| 與使用者共用     | 共用映像庫 | 映像定義 | 映像版本 |
|----------------------|----------------------|--------------|----------------------|
| 共用映像庫 | 是                  | 是          | 是                  |
| 映像定義     | 否                   | 是          | 是                  |

我們建議在庫級別共用，以獲得最佳體驗。 我們不建議共用單個映射版本。 有關 RBAC 的詳細資訊，請參閱[使用 RBAC 管理對 Azure 資源的訪問](../articles/role-based-access-control/role-assignments-portal.md)。

圖像也可以大規模共用，甚至跨租戶使用多租戶應用註冊。 有關跨租戶共用映射的詳細資訊，請參閱跨[Azure 租戶共用圖庫 VM 映射](../articles/virtual-machines/linux/share-images-across-tenants.md)。

## <a name="billing"></a>計費
使用共用映像庫服務不會有額外費用。 您只會支付下列資源的費用：
- 儲存共用映像版本的儲存體成本。 成本取決於映射版本的副本數以及版本複製到的區域數。 例如，如果您有 2 個映射，並且兩個映射都複製到 3 個區域，則將根據 6 個託管磁片的大小向您收費。 如需詳細資訊，請參閱[受控磁碟定價](https://azure.microsoft.com/pricing/details/managed-disks/)。
- 將第一個映射版本從源區域複製到複製區域的網路出口費用。 後續副本在區域內處理，因此無需支付額外費用。 

## <a name="updating-resources"></a>更新資源

創建後，您可以對圖像庫資源進行一些更改。 這些限制為：
 
共用映像庫：
- 描述

映像定義：
- 建議的 vCPU
- 推薦記憶體
- 描述
- 生命週期結束日期

映像版本：
- 區域複本計數
- 目標區域
- 從最新
- 生命週期結束日期

## <a name="sdk-support"></a>SDK 支援

下列 SDK 支援建立共用映像資源庫：

- [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/virtualmachines/management?view=azure-dotnet)
- [JAVA](https://docs.microsoft.com/java/azure/?view=azure-java-stable)
- [Node.js](https://docs.microsoft.com/javascript/api/@azure/arm-compute)
- [Python](https://docs.microsoft.com/python/api/overview/azure/virtualmachines?view=azure-python)
- [移至](https://docs.microsoft.com/azure/go/)

## <a name="templates"></a>範本

您可以使用範本建立共用映像庫資源。 有數個 Azure 快速入門範本可以使用： 

- [建立共用映像庫](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [在共用映像資源庫中建立映像定義](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [在共用映像資源庫中建立映像版本](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [從映像版本建立 VM](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

## <a name="frequently-asked-questions"></a>常見問題集 

* [如何列出訂用帳戶之間的所有共用映像庫資源？](#how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions) 
* [是否可以將現有映像移至共用映像庫？](#can-i-move-my-existing-image-to-the-shared-image-gallery)
* [是否可以從特製化磁碟建立映像版本？](#can-i-create-an-image-version-from-a-specialized-disk)
* [創建共用圖像庫資源後，是否可以將其移動到其他訂閱？](#can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created)
* [是否可以跨雲（如 Azure China 21Vianet 或 Azure 德國版或 Azure 政府雲）複製映射版本？](#can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud)
* [是否可以跨訂用帳戶複寫映像版本？](#can-i-replicate-my-image-versions-across-subscriptions)
* [是否可以跨 Azure AD 租用戶共用映像版本？](#can-i-share-image-versions-across-azure-ad-tenants)
* [跨目標區域複寫映像版本需要多久時間？](#how-long-does-it-take-to-replicate-image-versions-across-the-target-regions)
* [來源區域和目標區域有何差異？](#what-is-the-difference-between-source-region-and-target-region)
* [如何在建立映像版本時指定來源區域？](#how-do-i-specify-the-source-region-while-creating-the-image-version)
* [如何指定要在每個區域中建立的映像版本複本數目？](#how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region)
* [是否可以在與圖像定義和圖像版本的位置不同的位置創建共用圖像庫？](#can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version)
* [使用共用映像庫需要哪些費用？](#what-are-the-charges-for-using-the-shared-image-gallery)
* [我應該使用什麼 API 版本來創建共用圖像庫和圖像定義和圖像版本？](#what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version)
* [我應該使用什麼 API 版本來創建映射版本中的共用 VM 或虛擬機器縮放集？](#what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version)

### <a name="how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions"></a>如何列出訂用帳戶之間的所有共用映像庫資源？

要列出 Azure 門戶上有權訪問的所有共用映射庫資源，請按照以下步驟操作：

1. 打開[Azure 門戶](https://portal.azure.com)。
1. 移至 [所有資源]****。
1. 選取所有要列出其內含全部資源的訂用帳戶。
1. 尋找類型為 [私用資源庫]**** 的資源。
 
   若要查看映像定義和映像版本，您還應該選取 [顯示隱藏的類型]****。
 
   若要列出您有權存取之所有訂用帳戶的共用映像庫資源，請在 Azure CLI 中使用下列命令：

   ```azurecli
   az account list -otsv --query "[].id" | xargs -n 1 az sig list --subscription
   ```

### <a name="can-i-move-my-existing-image-to-the-shared-image-gallery"></a>是否可以將現有映像移至共用映像庫？
 
是。 根據您可能擁有的映像類型，案例共有 3 種。

 方案 1：如果託管映射與 SIG 處於同一訂閱中，則可以從該映射定義和映射版本創建映射定義和映射版本。

 方案 2：如果與 SIG 的訂閱中具有非託管映射，則可以從該映射創建託管映射，然後從該映射定義和映射版本創建映射。 

 方案 3：如果本地檔案系統中具有 VHD，則需要將 VHD 上載到託管映射，則可以從該映射定義和映射版本創建。

- 如果 VHD 是 Windows VM，請參閱[上載 VHD](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed)。
- 如果是 Linux VM 的 VHD，請參閱[上傳 VHD](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd#option-1-upload-a-vhd)

### <a name="can-i-create-an-image-version-from-a-specialized-disk"></a>是否可以從特製化磁碟建立映像版本？

是，支援專用磁片，因為映射處於預覽狀態。 您只能使用門戶[（Windows](../articles/virtual-machines/linux/shared-images-portal.md)或[Linux](../articles/virtual-machines/linux/shared-images-portal.md)） 和 API 從專用映射創建 VM。 預覽版不支援 PowerShell。

### <a name="can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created"></a>創建共用圖像庫資源後，是否可以將其移動到其他訂閱？

否，無法將共用映像庫資源移至不同訂用帳戶。 不過如有需要，可將資源庫中的映像版本複寫到其他區域。

### <a name="can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud"></a>是否可以跨雲（如 Azure China 21Vianet 或 Azure 德國版或 Azure 政府雲）複製映射版本？

否，無法跨雲端複寫映像版本。

### <a name="can-i-replicate-my-image-versions-across-subscriptions"></a>是否可以跨訂用帳戶複寫映像版本？

否，但可以在訂用帳戶中跨區域複寫映像版本，然後透過 RBAC 在其他訂用帳戶中使用。

### <a name="can-i-share-image-versions-across-azure-ad-tenants"></a>是否可以跨 Azure AD 租用戶共用映像版本？ 

可以，您可以使用 RBAC 跨租戶向個人共用。 但是，要大規模共用，請參閱使用[PowerShell](../articles/virtual-machines/windows/share-images-across-tenants.md)或[CLI](../articles/virtual-machines/linux/share-images-across-tenants.md)的"跨 Azure 租戶共用庫圖像"。

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

要指定區域副本計數，請傳遞位置以及要在該地區創建的副本數："中南部 US=2"。 

如果未對每個位置指定區域複本計數，則複本的預設數目會是所指定的一般複本計數。 

若要在 CLI 中指定一般複本計數，請在 `az sig image-version create` 命令中使用 **--replica-count** 引數。

### <a name="can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version"></a>是否可以在與圖像定義和圖像版本的位置不同的位置創建共用圖像庫？

是，可以的。 但是，作為最佳實踐，我們鼓勵您將資源組、共用圖像庫、圖像定義和圖像版本保留在同一位置。

### <a name="what-are-the-charges-for-using-the-shared-image-gallery"></a>使用共用映像庫需要哪些費用？

除了用於儲存映像版本的儲存體費用，以及用於將映像版本從來源區域複寫到目標區域的網路輸出費用外，使用共用映像庫服務不會再有任何費用。

### <a name="what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version"></a>我應該使用什麼 API 版本來創建共用圖像庫和圖像定義和圖像版本？

若要處理共用映像庫、映像定義及映像版本，建議您使用 API 版本 2018-06-01。 區域冗余存儲 （ZRS） 需要版本 2019-03-01 或更高版本。

### <a name="what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version"></a>我應該使用什麼 API 版本來創建映射版本中的共用 VM 或虛擬機器縮放集？

對於使用映像版本來部署 VM 和虛擬機器擴展集，建議您使用 API 版本 2018-04-01 或更高版本。
