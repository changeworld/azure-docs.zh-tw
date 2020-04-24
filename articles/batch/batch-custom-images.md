---
title: 從受控映射布建自訂集區
description: 從受控映射資源建立 Batch 集區，以使用應用程式的軟體和資料來布建計算節點。
ms.topic: article
ms.date: 09/16/2019
ms.openlocfilehash: 10e3932bc6006e1d91fbc7e4cf58a5d98c043520
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2020
ms.locfileid: "82117313"
---
# <a name="use-a-managed-image-to-create-a-pool-of-virtual-machines"></a>使用受控映射來建立虛擬機器的集區

若要為 Batch 集區的虛擬機器（Vm）建立自訂映射，您可以使用[共用映射庫](batch-sig-images.md)或*受控映射*資源。

> [!TIP]
> 在大部分情況下，您應該使用共用映射資源庫來建立自訂映射。 藉由使用共用映射資源庫，您可以更快速地布建集區、調整較大數量的 Vm，並在布建 Vm 時改善可靠性。 若要深入瞭解，請參閱[使用共用映射資源庫來建立自訂集](batch-sig-images.md)區。

## <a name="prerequisites"></a>先決條件

- **受控映像資源**。 若要使用自訂映像來建立虛擬機器集區，您必須在與 Batch 帳戶相同的 Azure 訂用帳戶和區域中，擁有或建立受控映像資源。 您應該從 VM 之 OS 磁碟 (以及視需要從其連結之資料磁碟) 的快照集建立該映像。 如需詳細資訊及準備受控映像的步驟，請參閱下一節。
  - 針對您建立的每個集區使用唯一的自訂映像。
  - 若要使用 Batch API 以映像建立集區，請指定映像的**資源識別碼**，其形式為 `/subscriptions/xxxx-xxxxxx-xxxxx-xxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage`。 若要使用入口網站，請使用映像的**名稱**。  
  - 受控映像資源應該在集區存留期內都存在，以供集區調升規模，並可在刪除集區之後移除。

- **Azure Active Directory （AAD）驗證**。 Batch 用戶端 API 必須使用 AAD 驗證。 Azure Batch 對於 AAD 的支援記載於[使用 Active Directory 驗證 Batch 服務解決方案](batch-aad-auth.md)中。

## <a name="prepare-a-custom-image"></a>準備自訂映像

在 Azure 中，您可以從下列方式準備受控映射：

- Azure VM 的 OS 和資料磁片的快照集
- 具有受控磁片的一般化 Azure VM
- 已上傳至雲端的一般化內部部署 VHD

若要使用自訂映像來可靠地調整 Batch 集區，建議您「只」** 使用第一個方法：使用 VM 磁碟的快照集。 請參閱下列步驟來準備 VM、建立快照集，然後從快照集建立映像。

### <a name="prepare-a-vm"></a>準備 VM

如果您要為映射建立新的 VM，請使用 Batch 所支援的第一方 Azure Marketplace 映射作為受控映射的基礎映射。 只有第一方映射可以用來作為基底映射。 若要取得 Azure Batch 所支援 Azure Marketplace 映射參考的完整清單，請參閱[列出節點代理程式 sku](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus)作業。

> [!NOTE]
> 您無法使用具有額外授權和購買條款的第三方映像作為您的基礎映像。 如需這些 Marketplace 映像的相關資訊，請參閱 [Linux](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
)或 [Windows](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) VM 的指導方針。

- 請確定已使用受控磁片建立 VM。 當您建立 VM 時，這是預設的儲存體設定。
- 不要在 VM 上安裝 Azure 延伸模組，例如「自訂指令碼」延伸模組。 如果映像包含預先安裝的延伸模組，則 Azure 在部署 Batch 集區時可能會遇到問題。
- 使用連接的資料磁片時，您必須在 VM 內掛接並格式化磁片，才能使用它們。
- 確定您提供的基本 OS 映像使用預設的暫存磁碟機。 Batch 節點代理程式目前需要有預設的暫存磁碟機。
- 一旦 VM 開始執行之後，請透過 RDP (適用於 Windows) 或 SSH (適用於 Linux) 向它連線。 安裝任何必要的軟體或複製所需的資料。  

### <a name="create-a-vm-snapshot"></a>建立 VM 快照集

快照集是完整的 VHD 唯讀複本。 若要建立 VM 之 OS 或資料磁碟的快照集，您可以使用 Azure 入口網站或命令列工具。 如需了解建立快照集的步驟與選項，請參閱適用於 [Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) 或 [Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md) VM 的指導方針。

### <a name="create-an-image-from-one-or-more-snapshots"></a>從一或多個快照集建立映像

若要從快照集建立受控映像，請使用 Azure 命令列工具，例如 [az image create](/cli/azure/image) 命令。 您可以指定 OS 磁碟快照集，以及視需要指定一或多個資料磁碟快照集，來建立映像。

## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>在 Azure 入口網站中從自訂映像建立集區

在您儲存自訂映像並知道其資源識別碼或名稱之後，請從該映像建立 Batch 集區。 下列步驟顯示如何從 Azure 入口網站建立集區。

> [!NOTE]
> 如果您要使用其中一個 Batch API 建立集區，請確定您用於 AAD 驗證的身分識別具備映像資源的權限。 請參閱[使用 Active Directory 驗證 Batch 服務解決方案](batch-aad-auth.md)。
>
> 受控映射的資源必須存在於集區的存留期。 如果基礎資源已刪除，則無法調整集區。

1. 在 Azure 入口網站中瀏覽至您的 Batch 帳戶。 此帳戶必須與包含自訂映像的資源群組位於相同的訂用帳戶和區域中。
2. 在左側的 [設定]**** 視窗中，選取 [集區]**** 功能表項目。
3. 在 [集區]**** 視窗中，選取 [新增]**** 命令。
4. 在 [新增集區]**** 視窗上，從 [映像類型]**** 下拉式清單選取 [自訂映像 (Linux/Windows)]****。 從 [自訂 VM 映像]**** 下拉式清單中，選取映像名稱 (資源識別碼的簡短形式)。
5. 依據您的自訂映像選取正確的**發行者/提供項目/SKU**。
6. 指定剩餘的必要設定，包括**節點大小**、**目標專用節點**和**低優先順序節點**，以及任何想要的選擇性設定。

    例如，針對 Microsoft Windows Server Datacenter 2016 自訂映像，[新增集區]**** 視窗隨即顯示，如下所示：

    ![從自訂 Windows 映像新增集區](media/batch-custom-images/add-pool-custom-image.png)
  
若要檢查現有的集區是否以自訂映像作為基礎，請參閱 [集區]**** 視窗的資源摘要區段中的 **Operating System** 屬性。 如果已從自訂映像建立集區，它就會設定為**自訂 VM 映像**。

與集區相關聯的所有自訂映像都會顯示在集區的 [屬性]**** 視窗中。

## <a name="considerations-for-large-pools"></a>大型集區的考量

如果您打算使用自訂映像來建立含有數百個或更多 VM 的集區，請務必依照先前的指導方針，使用從 VM 快照集建立的映像。

另請注意下列考慮：

- **大小限制** - 當您使用自訂映像時，Batch 會將集區大小限制在 2500 個專用計算節點，或 1000 個低優先順序節點。

  如果您使用同一個映像 (或以相同基礎快照集為基礎的多個映像) 來建立多個集區，則集區中的計算節點總數不可超過上述限制。 我們不建議將一個映像或其基礎快照集用於多個集區。

  如果您使用[輸入 NAT 集區](pool-endpoint-configuration.md)來設定集區，則可能會降低限制。

- **調整逾時** - 如果您的集區包含固定數目的節點 (不自動調整規模)，請將集區的 resizeTimeout 屬性值提高到 20-30 分鐘之類的值。 如果您的集區在逾時期間內未達到其目標大小，請執行另一個[調整大小作業](/rest/api/batchservice/pool/resize)。

  如果您規劃的集區含有超過 300 個計算節點，您可能需要多次調整集區大小，才能達到目標大小。
  
藉由使用[共用映射資源庫](batch-sig-images.md)，您可以使用自訂映射建立較大的集區，以及更多共用映射複本。 使用共用映射，集區達到穩定狀態所花費的時間，最快可達25%，且 VM 閒置延遲最高可達30%。

## <a name="considerations-for-using-packer"></a>使用 Packer 的考慮

使用 Packer 直接建立受控映射資源只能透過使用者訂用帳戶模式 Batch 帳戶來完成。 針對 Batch 服務模式帳戶，您必須先建立 VHD，然後將 VHD 匯入至受控映射資源。 根據您的集區配置模式（使用者訂用帳戶或 Batch 服務），建立受控映射資源的步驟會有所不同。

請確定用來建立受控映射的資源存在於任何參考自訂映射之集區的存留期內。 若未這麼做，可能會導致集區配置失敗和/或調整大小失敗。

如果移除影像或基礎資源，您可能會收到類似下列的錯誤： `There was an error encountered while performing the last resize on the pool. Please try resizing the pool again. Code: AllocationFailed`。 如果您收到此錯誤，請確定基礎資源尚未移除。

如需使用 Packer 建立 VM 的詳細資訊，請參閱使用[Packer 建立 Linux 映射](../virtual-machines/linux/build-image-with-packer.md)或[使用 Packer 建立 Windows 映像](../virtual-machines/windows/build-image-with-packer.md)。

## <a name="next-steps"></a>後續步驟

如需 Batch 的深入概觀，請參閱[使用 Batch 開發大規模的平行計算解決方案](batch-api-basics.md)。
