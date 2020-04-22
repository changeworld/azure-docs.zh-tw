---
title: 使用門戶建立分享的 Azure Linux VM 映像
description: 瞭解如何使用 Azure 門戶創建和共用 Linux 虛擬機器映射。
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: article
ms.workload: infrastructure
ms.date: 11/06/2019
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 74c87d589f1c50551ac5685fe0fa126a82bffbde
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758443"
---
# <a name="create-an-azure-shared-image-gallery-using-the-portal"></a>使用門戶建立 Azure 分享映像庫

[共用映像資源庫](shared-image-galleries.md)可簡化跨組織共用自訂映像。 自訂映像類似 Marketplace 映像，但您要自行建立它們。 自訂映像可用於啟動部署工作，例如，預先載入應用程式、應用程式設定和其他 OS 設定。 

共用映像資源庫可讓您與 AAD 租用戶中區域內或跨區域組織中的其他人共用自訂 VM 映像。 選擇您要共用的映像、您要開放使用的區域，以及您要共用的對象。 您可以建立多個資源庫，讓您可以根據邏輯群組共用映像。 

資源庫是一種頂層資源，可提供完整的角色型存取控制 (RBAC)。 可建立映像版本，並且您可以選擇將每個映像版本複寫到不同的 Azure 區域集合。 資源庫僅適用於受控映像。

共用映像庫具有多個資源類型。 我們將在這篇文章中使用或建置這些資源類型：

| 資源 | 描述|
|----------|------------|
| **託管映像** | 可單獨使用或用於在圖像庫中創建**圖像版本**的基本映射。 託管映像是從[通用](shared-image-galleries.md#generalized-and-specialized-images)VM 創建的。 受控映像是一種特殊的 VHD 類型，可用來產生多個 VM，現在可以用來建立共用映像版本。 |
| **快照式** | 可用於製作**圖像版本的**VHD 的副本。 快照可以從[專用](shared-image-galleries.md#generalized-and-specialized-images)VM(尚未通用的 VM)中拍攝,然後單獨使用或與數據磁碟的快照一起使用,以創建專用映射版本。
| **影像庫** | 和 Azure Marketplace 一樣，**映像庫**是用於管理和共用映像的存放庫，但您可以控制哪些使用者能夠存取。 |
| **映像定義** | 圖像在庫中定義,並包含有關圖像的資訊以及組織內使用它的要求。 您可以包括映像是通用化還是專用化、操作系統、最小和最大記憶體要求以及發行說明等資訊。 這是映像類型的定義。 |
| **映像版本** | **映像版本**是在使用資源庫時用來建立 VM 的項目。 您可以視需要為環境準備多個映像版本。 和受控映像一樣，當您使用**映像版本**來建立 VM 時，系統會使用映像版本來建立 VM 的新磁碟。 映像版本可以使用多次。 |

<br>

> [!IMPORTANT]
> 專用圖像當前處於公共預覽中。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>
> **已知的預覽限制**VM 只能使用門戶或 API 從專用映射創建。 不支援 CLI 或 PowerShell 預覽。


## <a name="before-you-begin"></a>開始之前

要完成本文中的範例,您必須具有通用 VM 的現有託管映射或專用 VM 的快照。 可以依照[教學操作:使用 Azure PowerShell 建立 Azure VM 的自訂映像](tutorial-custom-images.md)以建立託管映像,或為專用 VM[建立快照](../windows/snapshot-copy-managed-disk.md)。 對於託管映射和快照,資料磁碟大小不能超過 1 TB。

逐步完成本文之後，請視需要取代資源群組和 VM 名稱。

 
[!INCLUDE [virtual-machines-common-shared-images-portal](../../../includes/virtual-machines-common-shared-images-portal.md)]

## <a name="create-vms"></a>建立 VM 

現在,您可以創建一個或多個新 VM。 此範例在*美國東部*資料中心的 *myResourceGroup* 中建立名為 *myVMfromImage* 的 VM。

1. 轉到圖像定義。 您可以使用資源篩選器顯示所有可用的圖像定義。
1. 在圖像定義的頁面上,從頁面頂部的功能表中選擇 **「創建 VM」。。**
1. 對於**資源組**,選擇 **"創建新"** 並鍵入*名稱的"我的資源組*"。
1. 在**虛擬機器名稱**中,鍵入*myVM*。
1. 在 [區域]**** 中，選取 [美國東部]**。
1. 對於**可用性選項**,保留不需要*的基礎結構冗餘*的預設值。
1. 如果從影像定義的頁面開始,**則圖像**的值將自動填`latest`充 圖像版本。
1. 您**可以在大小**,從可用大小清單中選擇 VM 大小,然後選擇**選擇**。
1. 在**管理員帳戶**下,如果來源 VM 已通用,請輸入**您的使用者名稱**與**SSH 公開金鑰**。 如果源 VM 是專用的,則這些選項將灰顯,因為使用了源 VM 中的資訊。
1. 如果要允許遠端存取 VM,請在 **「公共入站埠**」下,選擇 **「允許選定的連接埠**」,然後從下拉清單中選擇**SSH (22)。** 如果不想允許遠端訪問 VM,請為**公共入站埠**保留 **「無**選擇」。
1. 完成後,選擇頁面底部的 **「審閱 + 創建**」按鈕。
1. VM 通過驗證後,選擇在頁面底部**創建**以啟動部署。


## <a name="clean-up-resources"></a>清除資源

若不再需要，您可以刪除資源群組、虛擬機器和所有相關資源。 若要這樣做，請選取虛擬機器的資源群組，選取 [刪除]  ，然後確認要刪除的資源群組名稱。

如果要刪除單個資源,則需要以相反的順序刪除它們。 例如,要刪除圖像定義,您需要刪除從該映射創建的所有圖像版本。

## <a name="next-steps"></a>後續步驟

您也可以使用範本建立共用映像庫資源。 有數個 Azure 快速入門範本可以使用： 

- [建立共用映像庫](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [在共用映像資源庫中建立映像定義](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [在共用映像資源庫中建立映像版本](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [從映像版本建立 VM](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

如需共用映像資源庫的詳細資訊，請參閱[概觀](shared-image-galleries.md)。 若遇到任何問題，請參閱[針對共用映像資源庫問題進行疑難排解](troubleshooting-shared-images.md)。

