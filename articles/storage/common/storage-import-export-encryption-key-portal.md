---
title: 使用 Azure 門戶為導入/匯出服務配置客戶管理的金鑰
description: 瞭解如何使用 Azure 門戶使用 Azure 導入/匯出服務的 Azure 金鑰保存庫配置客戶管理金鑰。 客戶管理的金鑰使您能夠創建、旋轉、禁用和撤銷存取控制。
services: storage
author: alkohli
ms.service: storage
ms.topic: how-to
ms.date: 03/12/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: ca1327a547e8550e47ff37e4ba100fcbd2b7a79f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282455"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-importexport-service"></a>在 Azure 金鑰保存庫中使用客戶管理的金鑰以進行導入/匯出服務

Azure 導入/匯出保護用於通過加密金鑰鎖定磁碟機的 BitLocker 金鑰。 預設情況下，BitLocker 金鑰使用 Microsoft 管理的金鑰進行加密。 為了對加密金鑰進行其他控制，您還可以提供客戶管理的金鑰。

客戶管理的金鑰必須創建並存儲在 Azure 金鑰保存庫中。 有關 Azure 金鑰保存庫的詳細資訊，請參閱[什麼是 Azure 金鑰保存庫？](../../key-vault/key-vault-overview.md)

本文演示如何在[Azure 門戶](https://portal.azure.com/)中使用客戶管理的金鑰以及導入/匯出服務。

## <a name="prerequisites"></a>Prerequisites

在您開始前，請確定：

1. 您已按照以下說明創建了導入作業或匯出作業：

    - [為 blob 創建導入作業](storage-import-export-data-to-blobs.md)。
    - [為檔創建導入作業](storage-import-export-data-to-files.md)。
    - [為 Blob 創建匯出作業](storage-import-export-data-from-blobs.md)

2. 您有一個現有的 Azure 金鑰保存庫，其中有一個金鑰，可用於保護 BitLocker 金鑰。 若要瞭解如何使用 Azure 門戶創建金鑰保存庫，請參閱[快速入門：使用 Azure 門戶從 Azure 金鑰保存庫設置和檢索機密](../../key-vault/quick-create-portal.md)。

    - **虛刪除**和 **"不清除**"設置在現有的金鑰保存庫上。 預設情況下，這些屬性未啟用。 要啟用這些屬性，請參閱以下文章中標題為 **"啟用虛刪除**和**啟用清除保護**"的部分：

        - [如何使用虛刪除與PowerShell。](../../key-vault/key-vault-soft-delete-powershell.md)
        - [如何使用虛刪除與CLI。](../../key-vault/key-vault-soft-delete-cli.md)
    - 現有金鑰保存庫應具有 2048 大小或以上的 RSA 金鑰。 有關金鑰的詳細資訊，請參閱["關於 Azure 金鑰保存庫金鑰、機密和證書"](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)中的**金鑰保存庫金鑰**。
    - 金鑰保存庫必須與資料的存儲帳戶位於同一區域。  
    - 如果沒有現有的 Azure 金鑰保存庫，也可以按照以下部分所述內聯創建它。

## <a name="enable-keys"></a>啟用金鑰

為導入/匯出服務配置客戶管理的金鑰是可選的。 預設情況下，導入/匯出服務使用 Microsoft 託管金鑰來保護 BitLocker 金鑰。 要在 Azure 門戶中啟用客戶管理的金鑰，請按照以下步驟操作：

1. 轉到"**導入作業的概述"** 邊欄選項卡。
2. 在右側窗格中，**選擇"選擇 BitLocker 金鑰的加密方式**"。

    ![選擇加密選項](./media/storage-import-export-encryption-key-portal/encryption-key-1.png)

3. 在**加密**邊欄選項卡中，您可以查看和複製設備 BitLocker 金鑰。 在**加密類型**下，您可以選擇如何保護 BitLocker 金鑰。 預設情況下，使用 Microsoft 託管金鑰。

    ![查看位鎖鍵](./media/storage-import-export-encryption-key-portal/encryption-key-2.png)

4. 您可以選擇指定客戶託管金鑰。 選擇客戶託管金鑰後，**選擇金鑰保存庫和金鑰**。

    ![選擇客戶託管金鑰](./media/storage-import-export-encryption-key-portal/encryption-key-3.png)

5. 在**Azure 金鑰保存庫的"選擇鍵**"選項卡中，將自動填滿訂閱。 對於**金鑰保存庫**，您可以從下拉清單中選擇現有的金鑰保存庫。

    ![選擇或創建 Azure 金鑰保存庫](./media/storage-import-export-encryption-key-portal/encryption-key-4.png)

6. 您還可以選擇 **"新建**"以創建新的金鑰保存庫。 在 **"創建金鑰保存庫"邊欄選項卡**中，輸入資源組和金鑰保存庫名稱。 接受所有其他預設值。 選擇 **"審閱 " 創建**。

    ![創建新的 Azure 金鑰保存庫](./media/storage-import-export-encryption-key-portal/encryption-key-5.png)

7. 查看與金鑰保存庫關聯的資訊，然後選擇 **"創建**"。 等待幾分鐘，完成金鑰保存庫創建。

    ![建立 Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-6.png)

8. 在**Azure 金鑰保存庫中選擇金鑰**中，可以在現有金鑰保存庫中選擇金鑰。

9. 如果創建新金鑰保存庫，請選擇 **"新建**"以創建金鑰。 RSA 金鑰大小可以是 2048 或更高。

    ![在 Azure 金鑰保存庫中創建新金鑰](./media/storage-import-export-encryption-key-portal/encryption-key-7.png)

    如果在創建金鑰保存庫時未啟用虛刪除和清除保護，則金鑰保存庫將更新為啟用虛刪除和清除保護。

10. 提供金鑰的名稱，接受其他預設值，然後選擇 **"創建**"。

    ![創建新鍵](./media/storage-import-export-encryption-key-portal/encryption-key-8.png)

11. 選擇 **"版本**"，然後**選擇"** 將通知您金鑰在金鑰保存庫中創建。

    ![在金鑰保存庫中創建的新金鑰](./media/storage-import-export-encryption-key-portal/encryption-key-9.png)

在 **"加密"** 邊欄選項卡中，您可以看到為客戶託管金鑰選擇的金鑰保存庫和金鑰。

## <a name="disable-keys"></a>禁用金鑰

您只能在導入/匯出作業的任何階段禁用 Microsoft 託管金鑰並移動到客戶託管金鑰。 但是，創建客戶託管金鑰後，無法禁用該金鑰。

## <a name="troubleshoot-customer-managed-key-errors"></a>排除客戶託管的嚴重錯誤

如果您收到與客戶託管金鑰相關的任何錯誤，請使用下表進行故障排除：

| 錯誤碼     |詳細資料     | 追 討？    |
|----------------|------------|-----------------|
| CmkErrorAccess被吊銷 | 應用了客戶託管金鑰，但金鑰訪問當前已吊銷。 有關詳細資訊，請參閱如何[啟用金鑰訪問](https://docs.microsoft.com/rest/api/keyvault/vaults/updateaccesspolicy)。                                                      | 是，檢查是否： <ol><li>金鑰保存庫在訪問策略中仍具有 MSI。</li><li>訪問策略提供獲取、包裝、取消包裝的許可權。</li><li>如果金鑰保存庫位於防火牆後面的 vNet 中，請檢查是否啟用了**允許 Microsoft 受信任服務**。</li></ol>                                                                                            |
| CmkError 已禁用      | 應用了客戶託管金鑰，但該金鑰已禁用。 有關詳細資訊，請參閱如何[啟用金鑰](https://docs.microsoft.com/rest/api/keyvault/vaults/createorupdate)。                                                                             | 是，通過啟用金鑰版本     |
| CmkError 未找到      | 應用了客戶託管金鑰，但找不到該金鑰。 <br>如果在保留期後刪除和清除金鑰，則無法恢復該金鑰。 如果備份了金鑰，則可以還原金鑰以解決此問題。 | 否，金鑰已被刪除，並在保留期後被清除。 <br>是，只有當客戶有金鑰備份並還原金鑰時。  |
| CmkErrorVault 未找到 | 應用了客戶託管金鑰，但找不到與金鑰關聯的金鑰保存庫。<br>如果刪除了金鑰保存庫，則無法恢復客戶託管金鑰。  如果將金鑰保存庫遷移到其他租戶，請參閱在[訂閱移動後更改金鑰保存庫租戶 ID。](https://docs.microsoft.com/azure/key-vault/key-vault-subscription-move-fix) |   否，如果客戶已刪除金鑰保存庫。<br> 是，如果金鑰保存庫進行了租戶遷移，則執行以下操作之一： <ol><li>將金鑰保存庫移回舊租戶。</li><li>設置標識 = 無，然後返回標識 = 系統分配，這將刪除並重新創建標識</li></ol><br>注意：租戶遷移案例是基於有限理解，需要測試和確認實際行為，可以稍後修改。 |

## <a name="next-steps"></a>後續步驟

- [什麼是 Azure 金鑰保存庫](https://docs.microsoft.com/azure/key-vault/key-vault-overview)？
