---
title: 使用入口網站建立 Azure 共用映射資源庫
description: 瞭解如何使用 Azure 入口網站來建立及共用虛擬機器映射。
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 11/06/2019
ms.author: cynthn
ms.openlocfilehash: 1560a67d73b712ba1f295992fce4f7f1a2ae75bd
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2020
ms.locfileid: "82100697"
---
# <a name="create-an-azure-shared-image-gallery-using-the-portal"></a>使用入口網站建立 Azure 共用映射資源庫

[共用映像資源庫](shared-image-galleries.md)可簡化跨組織共用自訂映像。 自訂映像類似 Marketplace 映像，但您要自行建立它們。 自訂映像可用於啟動部署工作，例如，預先載入應用程式、應用程式設定和其他 OS 設定。 

共用映像資源庫可讓您與 AAD 租用戶中區域內或跨區域組織中的其他人共用自訂 VM 映像。 選擇您要共用的映像、您要開放使用的區域，以及您要共用的對象。 您可以建立多個資源庫，讓您可以根據邏輯群組共用映像。 

資源庫是一種頂層資源，可提供完整的角色型存取控制 (RBAC)。 可建立映像版本，並且您可以選擇將每個映像版本複寫到不同的 Azure 區域集合。 資源庫僅適用於受控映像。

共用映像庫具有多個資源類型。 我們將在這篇文章中使用或建置這些資源類型：

| 資源 | 描述|
|----------|------------|
| **受控映射** | 可以單獨使用或用來在映射庫中建立**映射版本**的基本映射。 系統會從[一般化](shared-image-galleries.md#generalized-and-specialized-images)vm 建立受控映射。 受控映像是一種特殊的 VHD 類型，可用來產生多個 VM，現在可以用來建立共用映像版本。 |
| **快照式** | 可以用來建立**映射版本**的 VHD 複本。 您可以從[特製](shared-image-galleries.md#generalized-and-specialized-images)化 VM （尚未一般化的虛擬機器）取得快照集，然後單獨使用或搭配資料磁片的快照集，以建立特製化的映射版本。
| **映射庫** | 和 Azure Marketplace 一樣，**映像庫**是用於管理和共用映像的存放庫，但您可以控制哪些使用者能夠存取。 |
| **映像定義** | 映射會定義于資源庫中，並包含影像的相關資訊，以及在您的組織內使用它的需求。 您可以包含類似映射是一般化或特製化、作業系統、最小和最大記憶體需求，以及版本資訊等資訊。 這是映像類型的定義。 |
| **映像版本** | **映像版本**是在使用資源庫時用來建立 VM 的項目。 您可以視需要為環境準備多個映像版本。 和受控映像一樣，當您使用**映像版本**來建立 VM 時，系統會使用映像版本來建立 VM 的新磁碟。 映像版本可以使用多次。 |

<br>


> [!IMPORTANT]
> 特製化映射目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>
> **已知的預覽限制**只能使用入口網站或 API，從特製化映射建立 Vm。 不是預覽版的 CLI 或 PowerShell 支援。

## <a name="before-you-begin"></a>開始之前

若要完成本文中的範例，您必須擁有一般化 VM 的現有受控映射，或特製化 VM 的快照集。 您可以遵循[教學課程：使用 Azure PowerShell 建立 AZURE VM 的自訂映射](tutorial-custom-images.md)來建立受控映射，或建立特製化 VM 的[快照](snapshot-copy-managed-disk.md)集。 對於受控映射和快照集，資料磁片大小不能超過 1 TB。

逐步完成本文之後，請視需要取代資源群組和 VM 名稱。


[!INCLUDE [virtual-machines-common-shared-images-portal](../../../includes/virtual-machines-common-shared-images-portal.md)]
 
## <a name="create-vms"></a>建立 VM

現在您可以建立一或多個新的 Vm。 這個範例會在*美國東部*資料中心的*myResourceGroup*中建立名為*myVM*的 VM。

1. 移至您的映射定義。 您可以使用資源篩選器來顯示所有可用的映射定義。
1. 在映射定義的頁面上，從頁面頂端的功能表中選取 [**建立 VM** ]。
1. 針對 [**資源群組**]，選取 [**建立新**的]，然後在 [名稱] 中輸入*myResourceGroup* 。
1. 在 [**虛擬機器名稱**] 中，輸入*myVM*。
1. 在 [區域]**** 中，選取 [美國東部]**。
1. 針對 [**可用性選項**]，保留 [*不需要基礎結構冗余*] 的預設值。
1. 如果您從**Image**影像定義的頁面啟動， `latest`影像的值會自動填入映射版本。
1. 針對 [**大小**]，從可用的大小清單中選擇 VM 大小，然後選擇 [**選取**]。
1. 在 [**系統管理員帳戶**] 下，如果映射已一般化，您需要提供使用者名稱，例如*azureuser*和密碼。 密碼長度至少必須有 12 個字元，而且符合[定義的複雜度需求](faq.md#what-are-the-password-requirements-when-creating-a-vm)。 如果您的映射是特製化的，[使用者名稱] 和 [密碼] 欄位會呈現灰色，因為會使用來源 VM 的使用者名稱和密碼。
1. 如果您想要允許遠端存取 VM，請在 [**公用輸入埠**] 底下，選擇 [**允許選取的埠**]，然後從下拉式選單中選取 [ **RDP （3389）** ]。 如果您不想要允許對 VM 的遠端存取，請將 [**公用輸入埠**] 選取為 [**無**]。
1. 當您完成時，請選取頁面底部的 [**審查 + 建立**] 按鈕。
1. 在 VM 通過驗證之後，請選取頁面底部的 [**建立**] 以開始部署。


## <a name="clean-up-resources"></a>清除資源

若不再需要，您可以刪除資源群組、虛擬機器和所有相關資源。 若要這樣做，請選取虛擬機器的資源群組，選取 [刪除]  ，然後確認要刪除的資源群組名稱。

如果您想要刪除個別的資源，您必須以相反順序刪除它們。 例如，若要刪除映射定義，您必須刪除所有從該映射建立的映射版本。

## <a name="next-steps"></a>後續步驟

您也可以使用範本建立共用映像庫資源。 有數個 Azure 快速入門範本可以使用： 

- [建立共用映像庫](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [在共用映像資源庫中建立映像定義](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [在共用映像資源庫中建立映像版本](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [從映像版本建立 VM](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

如需共用映像資源庫的詳細資訊，請參閱[概觀](shared-image-galleries.md)。 若遇到任何問題，請參閱[針對共用映像資源庫問題進行疑難排解](troubleshooting-shared-images.md)。

