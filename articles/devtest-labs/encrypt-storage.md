---
title: 在 Azure DevTest Labs 中將實驗室所使用的 Azure 儲存體帳戶加密
description: 瞭解如何在 Azure DevTest Labs 中設定實驗室所使用的 Azure 儲存體加密
ms.topic: how-to
ms.date: 07/29/2020
ms.openlocfilehash: 3c1d1531084deeabbe9a8d261e93554a2c691eb6
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2020
ms.locfileid: "87433545"
---
# <a name="encrypt-azure-storage-used-by-a-lab-in-azure-devtest-labs"></a>加密實驗室在 Azure DevTest Labs 中使用的 Azure 儲存體
在 Azure DevTest Labs 中建立的每個實驗室都會使用相關聯的 Azure 儲存體帳戶來建立。 儲存體帳戶用於下列用途： 

- 儲存可以用來建立虛擬機器的[公式](devtest-lab-manage-formulas.md)檔。
- 儲存成品結果，其中包含套用成品所產生的部署和延伸模組記錄。 
- [上傳虛擬硬碟（Vhd）以在實驗室中建立自訂映射。](devtest-lab-create-template.md)
- 快取經常[使用的](add-artifact-vm.md)成品和[Azure Resource Manager 範本](devtest-lab-create-environment-from-arm.md)，以便在虛擬機器/環境建立期間進行更快速的抓取。

> [!NOTE]
> 上述資訊是實驗室運作的關鍵。 除非明確刪除，否則會儲存在實驗室（和實驗室資源）的生命週期內。 手動刪除這些資源可能會導致建立實驗室 Vm 和/或公式損毀以供未來使用的錯誤。 

## <a name="locate-the-storage-account-and-view-its-contents"></a>找出儲存體帳戶並查看其內容

1. 在實驗室的 [首頁] 頁面上，選取 [**總覽**] 頁面上的 [**資源群組**]。 您應該會看到包含實驗室之資源群組的 [**資源群組**] 頁面。 

    :::image type="content" source="./media/encrypt-storage/overview-resource-group-link.png" alt-text="在 [總覽] 頁面上選取 [資源群組]":::
1. 選取實驗室的 Azure 儲存體帳戶。 實驗室儲存體帳戶的命名慣例為： `a<labNameWithoutInvalidCharacters><4-digit number>` 。 例如，如果實驗室名稱為 `contosolab` ，則儲存體帳戶名稱可能是 `acontosolab7576` 。 

    :::image type="content" source="./media/encrypt-storage/select-storage-account.png" alt-text="在實驗室的資源群組中選取 [儲存體帳戶]":::
3. 在 [**儲存體帳戶**] 頁面上，選取左側功能表上的 [**儲存體總管（預覽）** ]，然後選取 [ **BLOB 容器**] 以尋找相關的實驗室相關內容。 

   :::image type="content" source="./media/encrypt-storage/storage-explorer.png" alt-text="儲存體總管 (預覽)" lightbox="./media/encrypt-storage/storage-explorer.png":::

## <a name="encrypt-the-lab-storage-account"></a>加密實驗室儲存體帳戶
當您的資料保存到雲端時，Azure 儲存體會自動將其加密。 Azure 儲存體加密可保護您的資料，並協助您符合組織的安全性和合規性承諾。 如需詳細資訊，請參閱[待用資料的 Azure 儲存體加密](../storage/common/storage-service-encryption.md)。

實驗室儲存體帳戶中的資料會使用**Microsoft 管理的金鑰**進行加密。 您可以依賴 Microsoft 管理的金鑰來加密您的資料，也可以使用您自己的金鑰來管理加密。 如果您選擇使用您自己的金鑰來管理實驗室儲存體帳戶的加密，您可以指定**客戶管理的金鑰**，以及用來加密/解密 Blob 儲存體和 Azure 檔案儲存體中資料的 Azure Key Vault。 如需有關客戶管理金鑰的詳細資訊，請參閱搭配[使用客戶管理的金鑰與 Azure Key Vault 來管理 Azure 儲存體加密](../storage/common/encryption-customer-managed-keys.md)。

若要瞭解如何設定客戶管理的金鑰以進行 Azure 儲存體加密，請參閱下列文章： 

- [Azure 入口網站](../storage/common/storage-encryption-keys-portal.md)
- [Azure PowerShell](../storage/common/storage-encryption-keys-powershell.md)
- [Azure CLI](../storage/common/storage-encryption-keys-cli.md)


## <a name="manage-the-azure-blob-storage-life-cycle"></a>管理 Azure Blob 儲存體生命週期
如前所述，儲存在實驗室儲存體帳戶中的資訊，對於實驗室的運作非常重要，而不會發生任何錯誤。 除非明確刪除，否則這項資料會繼續保留在實驗室的儲存體帳戶中，或特定實驗室虛擬機器的生命週期（視資料類型而定）。

### <a name="uploaded-vhds"></a>已上傳的 Vhd
這些 Vhd 是用來建立自訂映射。 移除這些專案將使其無法再從這些 Vhd 建立自訂映射。

### <a name="artifacts-cache"></a>構件快取
這些快取會在套用任何時間成品時重新建立。 系統會使用個別參考的存放庫中的最新內容來重新整理這些更新。 因此，如果您刪除此資訊來儲存儲存體相關的費用，將會暫時降低。

### <a name="azure-resource-manager-template-cache"></a>Azure Resource Manager 範本快取
每當以 Azure Resource Manager 為基礎的範本存放庫連線並在實驗室中啟動時，就會重新建立這些快取。 系統會使用個別參考的存放庫中的最新內容來重新整理這些更新。 因此，如果您刪除此資訊來儲存儲存體相關的費用，將會暫時降低。

### <a name="formulas"></a>公式
這些檔可用來支援選項，以從現有的 Vm 建立公式，以及從公式建立 Vm。 在執行下列作業時，刪除這些公式檔可能會導致錯誤：

- 從現有的實驗室 VM 建立公式
- 建立或更新公式 
- 從公式建立 VM。

### <a name="artifact-results"></a>成品結果
套用成品時，個別成品結果的大小可能會隨著時間而增加，視實驗室 Vm 上執行的成品數目和類型而定。 因此，身為實驗室擁有者，您可能會想要控制這類檔的生命週期。 如需詳細資訊，請參閱[管理 Azure Blob 儲存體生命週期](../storage/blobs/storage-lifecycle-management-concepts.md)。

> [!IMPORTANT]
> 我們建議您執行此步驟，以降低與 Azure 儲存體帳戶相關聯的費用。 

例如，下列規則可用來針對成品結果設定90天的到期規則。 它可確保較舊的成品結果會定期從儲存體帳戶回收。

```json
{
  "rules": [
    {
      "name": "expirationRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "artifacts/results" ]
        },
        "actions": {
          "baseBlob": {
            "delete": { "daysAfterModificationGreaterThan": 90 }
          },
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

## <a name="next-steps"></a>後續步驟
若要瞭解如何設定客戶管理的金鑰以進行 Azure 儲存體加密，請參閱下列文章： 

- [Azure 入口網站](../storage/common/storage-encryption-keys-portal.md)
- [Azure PowerShell](../storage/common/storage-encryption-keys-powershell.md)
- [Azure CLI](../storage/common/storage-encryption-keys-cli.md)


