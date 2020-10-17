---
title: 在 Azure DevTest Labs 中加密實驗室所使用的 Azure 儲存體帳戶
description: 瞭解如何在 Azure DevTest Labs 中設定實驗室所使用的 Azure 儲存體加密
ms.topic: how-to
ms.date: 07/29/2020
ms.openlocfilehash: dcede89fb23c532742e41121688bcb51a5a73833
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2020
ms.locfileid: "92149315"
---
# <a name="encrypt-azure-storage-used-by-a-lab-in-azure-devtest-labs"></a>加密 Azure DevTest Labs 中的實驗室所使用的 Azure 儲存體
在 Azure DevTest Labs 中建立的每個實驗室都會使用相關聯的 Azure 儲存體帳戶建立。 儲存體帳戶用於下列用途： 

- 儲存可以用來建立虛擬機器的 [公式](devtest-lab-manage-formulas.md) 檔。
- 儲存構件結果，其中包含從套用成品產生的部署和延伸模組記錄。 
- [上傳虛擬硬碟 (Vhd) 在實驗室中建立自訂映射。](devtest-lab-create-template.md)
- 快取經常 [使用的](add-artifact-vm.md) 成品和 [Azure Resource Manager 範本](devtest-lab-create-environment-from-arm.md) ，以加快虛擬機器/環境建立期間的抓取速度。

> [!NOTE]
> 上述資訊對實驗室的運作而言很重要。 除非明確刪除，否則它會儲存在實驗室 (和實驗室資源) 的存留期內。 手動刪除這些資源可能會導致建立實驗室 Vm 和/或公式損毀以供未來使用的錯誤。 

## <a name="locate-the-storage-account-and-view-its-contents"></a>找出儲存體帳戶並查看其內容

1. 在實驗室的首頁上，選取 [**總覽**] 頁面上的**資源群組**。 您應該會看到包含實驗室之資源群組的 [ **資源群組** ] 頁面。 

    :::image type="content" source="./media/encrypt-storage/overview-resource-group-link.png" alt-text="選取 [總覽] 頁面上的 [資源群組]":::
1. 選取實驗室的 Azure 儲存體帳戶。 實驗室儲存體帳戶的命名慣例為： `a<labNameWithoutInvalidCharacters><4-digit number>` 。 例如，如果實驗室名稱是 `contosolab` ，則儲存體帳戶名稱可能是 `acontosolab7576` 。 

    :::image type="content" source="./media/encrypt-storage/select-storage-account.png" alt-text="選取 [總覽] 頁面上的 [資源群組]":::
3. 在 [ **儲存體帳戶** ] 頁面上，選取左側功能表上的 [ **儲存體總管 (預覽) ** ]，然後選取 [ **BLOB 容器** ] 以尋找相關的實驗室相關內容。 

   :::image type="content" source="./media/encrypt-storage/storage-explorer.png" alt-text="選取 [總覽] 頁面上的 [資源群組]" lightbox="./media/encrypt-storage/storage-explorer.png":::

## <a name="encrypt-the-lab-storage-account"></a>加密實驗室儲存體帳戶
Azure 儲存體將資料保存到雲端時，會自動加密資料。 Azure 儲存體加密可保護您的資料，並協助您符合組織的安全性和合規性承諾。 如需詳細資訊，請參閱[待用資料的 Azure 儲存體加密](../storage/common/storage-service-encryption.md)。

實驗室儲存體帳戶中的資料會使用 **Microsoft 管理的金鑰**進行加密。 您可以依賴 Microsoft 管理的金鑰來加密您的資料，也可以使用您自己的金鑰管理加密。 如果您選擇使用自己的金鑰來管理實驗室儲存體帳戶的加密，您可以使用 Azure Key Vault 來指定 **客戶管理的金鑰** ，以用來加密/解密 Blob 儲存體和 Azure 檔案儲存體中的資料。 如需客戶管理金鑰的詳細資訊，請參閱 [使用客戶管理的金鑰搭配 Azure Key Vault 管理 Azure 儲存體加密](../storage/common/customer-managed-keys-overview.md)。

若要瞭解如何為 Azure 儲存體加密設定客戶管理的金鑰，請參閱下列文章： 

- [Azure 入口網站](../storage/common/customer-managed-keys-configure-key-vault.md)
- [Azure PowerShell](../storage/common/customer-managed-keys-configure-key-vault.md)
- [Azure CLI](../storage/common/customer-managed-keys-configure-key-vault.md)


## <a name="manage-the-azure-blob-storage-life-cycle"></a>管理 Azure Blob 儲存體生命週期
如前文所述，儲存在實驗室儲存體帳戶中的資訊對實驗室的運作至關重要，不會發生任何錯誤。 除非明確刪除，否則此資料會繼續保留在實驗室的儲存體帳戶中，以供實驗室生命週期或特定實驗室虛擬機器的存留期（視資料類型而定）。

### <a name="uploaded-vhds"></a>上傳的 Vhd
這些 Vhd 會用來建立自訂映射。 移除它們將使其無法再從這些 Vhd 建立自訂映射。

### <a name="artifacts-cache"></a>構件快取
這些快取會在任何時間套用時重新建立。 系統會以個別參考存放庫中的最新內容來重新整理它們。 因此，如果您刪除此資訊以節省儲存體相關的費用，則會暫時降低。

### <a name="azure-resource-manager-template-cache"></a>Azure Resource Manager 範本快取
這些快取會在 Azure Resource Manager 範本存放庫連線並在實驗室中啟動時重新建立。 系統會以個別參考存放庫中的最新內容來重新整理它們。 因此，如果您刪除此資訊以節省儲存體相關的費用，則會暫時降低。

### <a name="formulas"></a>公式
這些檔可用來支援從現有的 Vm 建立公式，以及從公式建立 Vm 的選項。 在執行下列作業時，刪除這些公式檔可能會導致錯誤：

- 從現有的實驗室 VM 建立公式
- 建立或更新公式 
- 從公式建立 VM。

### <a name="artifact-results"></a>成品結果
套用成品時，視實驗室 Vm 上執行的成品數目和類型而定，個別構件結果的大小可能會隨時間增加。 作為實驗室擁有者，您可能會想要控制這類檔的生命週期。 如需詳細資訊，請參閱[管理 Azure Blob 儲存體生命週期](../storage/blobs/storage-lifecycle-management-concepts.md)。

> [!IMPORTANT]
> 建議您執行此步驟，以降低與 Azure 儲存體帳戶相關聯的費用。 

例如，下列規則是用來針對成品結果明確設定90天的到期規則。 它可確保定期從儲存體帳戶回收較舊的成品結果。

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
若要瞭解如何為 Azure 儲存體加密設定客戶管理的金鑰，請參閱下列文章： 

- [Azure 入口網站](../storage/common/customer-managed-keys-configure-key-vault.md)
- [Azure PowerShell](../storage/common/customer-managed-keys-configure-key-vault.md)
- [Azure CLI](../storage/common/customer-managed-keys-configure-key-vault.md)