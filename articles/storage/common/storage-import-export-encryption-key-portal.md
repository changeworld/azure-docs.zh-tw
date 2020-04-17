---
title: 使用 Azure 閘戶匯入/匯出服務設定客戶管理的金鑰
description: 瞭解如何使用 Azure 門戶使用 Azure 匯入/匯出服務的 Azure 密鑰保管庫配置客戶管理金鑰。 客戶管理的金鑰使您能夠創建、旋轉、禁用和撤銷存取控制。
services: storage
author: alkohli
ms.service: storage
ms.topic: how-to
ms.date: 03/12/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: ddcb47bfe8ba2b77efd8ff0aed52f1412107f0c5
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81456493"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-importexport-service"></a>在 Azure 金鑰保存庫中使用客戶管理的金鑰以匯入/匯出服務

Azure 匯入/匯出保護用於透過加密金鑰鎖定驅動器的 BitLocker 金鑰。 默認情況下,BitLocker 金鑰使用 Microsoft 管理的密鑰進行加密。 為了對加密金鑰進行其他控制,您還可以提供客戶管理的密鑰。

客戶管理的金鑰必須建立並存儲在 Azure 密鑰保管庫中。 有關 Azure 金鑰保管庫的詳細資訊,請參閱[什麼是 Azure 密鑰保管庫?](../../key-vault/general/overview.md)

本文演示如何在[Azure 門戶](https://portal.azure.com/)中使用客戶管理的密鑰以及導入/匯出服務。

## <a name="prerequisites"></a>Prerequisites

在您開始前，請確定：

1. 您已將以下說明建立匯入工作或匯出作業:

    - [建立執行到 blob 建立匯入工作](storage-import-export-data-to-blobs.md)。
    - [建立檔案建立到工作](storage-import-export-data-to-files.md)。
    - [以 Blob 建立匯出工作](storage-import-export-data-from-blobs.md)

2. 您有一個現有的 Azure 密鑰保管庫,其中有一個密鑰,可用於保護 BitLocker 密鑰。 若要瞭解如何使用 Azure 門戶建立金鑰保管庫,請參閱[快速入門:使用 Azure 門戶從 Azure 金鑰保管庫設定和檢索機密](../../key-vault/secrets/quick-create-portal.md)。

    - **軟刪除**和 **「不清除**」設定在現有的密鑰保管庫上。 默認情況下,這些屬性未啟用。 要啟用這些屬性,請參閱以下文章中標題為 **「啟用軟刪除**與**開啟清除保護**」的部分:

        - [如何使用軟刪除與PowerShell。](../../key-vault/general/soft-delete-powershell.md)
        - [如何使用軟刪除與CLI。](../../key-vault/general/soft-delete-cli.md)
    - 現有金鑰保管庫應具有 2048 大小或以上的 RSA 密鑰。 關於金鑰的詳細資訊,請參考[「關於 Azure 金鑰保管庫金鑰、機密和憑證」](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)的**金鑰保管庫金鑰**。
    - 密鑰保管庫必須與數據的存儲帳戶位於同一區域。  
    - 如果沒有現有的 Azure 密鑰保管庫,也可以按照以下部分所述內聯創建它。

## <a name="enable-keys"></a>開啟金鑰

為導入/匯出服務配置客戶管理的密鑰是可選的。 默認情況下,匯入/匯出服務使用 Microsoft 託管金鑰來保護 BitLocker 密鑰。 要在 Azure 門戶開啟客戶管理的金鑰,請按照以下步驟操作:

1. 轉到「**導入作業的概述」** 邊欄選項卡。
2. 在右邊窗格中,**選擇「選擇 BitLocker 金鑰的加密方式**」 。

    ![選擇加密選項](./media/storage-import-export-encryption-key-portal/encryption-key-1.png)

3. 在**加密**邊列選項卡中,您可以查看和複製設備 BitLocker 金鑰。 加密**類型**下,您可以選擇如何保護 BitLocker 金鑰。 默認情況下,使用 Microsoft 託管金鑰。

    ![檢視位鎖鍵](./media/storage-import-export-encryption-key-portal/encryption-key-2.png)

4. 您可以選擇指定客戶託管金鑰。 選擇客戶託管金鑰後,**選擇金鑰保管庫與金鑰**。

    ![選擇客戶託管金鑰](./media/storage-import-export-encryption-key-portal/encryption-key-3.png)

5. 在**Azure 金鑰保管庫的「選擇鍵**」選項卡中,將自動填充訂閱。 對於**金鑰保管庫**,您可以從下拉清單中選擇現有的密鑰保管庫。

    ![選擇或建立 Azure 金鑰保存庫](./media/storage-import-export-encryption-key-portal/encryption-key-4.png)

6. 您還可以選擇 **「新建**」以創建新的密鑰保管庫。 在 **「創建金鑰保管庫」邊欄選項卡**中,輸入資源群組和密鑰保管庫名稱。 接受所有其他預設值。 選取 [檢閱 + 建立]  。

    ![建立新的 Azure 金鑰保存庫](./media/storage-import-export-encryption-key-portal/encryption-key-5.png)

7. 檢視與密鑰保管庫關聯的資訊,然後選擇 **"創建**"。 等待幾分鐘,完成金鑰保管庫創建。

    ![建立 Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-6.png)

8. 在**Azure 金鑰保管庫中選擇金鑰**中,可以在現有金鑰保管庫中選擇金鑰。

9. 如果建立新密鑰保管庫,請選擇 **"新建**"以建立金鑰。 RSA 密鑰大小可以是 2048 或更高。

    ![在 Azure 金鑰保存新金鑰](./media/storage-import-export-encryption-key-portal/encryption-key-7.png)

    如果在創建金鑰保管庫時未啟用軟刪除和清除保護,則密鑰保管庫將更新為啟用軟刪除和清除保護。

10. 提供金鑰的名稱,接受其他預設值,然後選擇 **"創建**"。

    ![建立新鍵](./media/storage-import-export-encryption-key-portal/encryption-key-8.png)

11. 選擇**版本 ",** 然後**選擇"** 將通知您金鑰在金鑰保管庫中創建。

    ![在金鑰保存式庫中建立的新金鑰](./media/storage-import-export-encryption-key-portal/encryption-key-9.png)

在 **「加密」** 邊欄選項卡中,您可以看到為客戶託管金鑰選擇的密鑰保管庫和密鑰。

## <a name="disable-keys"></a>關閉金鑰

您只能在導入/匯出作業的任何階段禁用 Microsoft 託管密鑰並移動到客戶託管密鑰。 但是,創建客戶託管金鑰後,無法禁用該密鑰。

## <a name="troubleshoot-customer-managed-key-errors"></a>排除客戶託管的關鍵錯誤

如果您收到與客戶託管金鑰相關的任何錯誤,請使用下表進行故障排除:

| 錯誤碼     |詳細資料     | 追 討?    |
|----------------|------------|-----------------|
| CmkErrorAccess被吊銷 | 應用了客戶託管金鑰,但密鑰訪問當前已吊銷。 關於詳細資訊,請參考如何[啟用金鑰存取](https://docs.microsoft.com/rest/api/keyvault/vaults/updateaccesspolicy)。                                                      | 是,檢查是否: <ol><li>密鑰保管庫在訪問策略中仍具有 MSI。</li><li>訪問策略提供獲取、包裝、取消包裝的許可權。</li><li>如果金鑰保存庫在防火牆後面的 vNet 中,請檢查是否開啟了**允許 Microsoft 受信任服務**。</li></ol>                                                                                            |
| CmkError 已關閉      | 應用了客戶託管金鑰,但該密鑰已禁用。 關於詳細資訊,請參考如何[啟用金鑰](https://docs.microsoft.com/rest/api/keyvault/vaults/createorupdate)。                                                                             | 是,透過啟用金鑰版本     |
| CmkError 找不到      | 應用了客戶託管金鑰,但找不到該密鑰。 <br>如果在保留期後刪除和清除密鑰,則無法恢復該密鑰。 如果備份了密鑰,則可以還原密鑰以解決此問題。 | 否,密鑰已被刪除,並在保留期後被清除。 <br>是,只有當客戶有密鑰備份並還原密鑰時。  |
| CmkErrorVault 找不到 | 應用了客戶託管金鑰,但找不到與密鑰關聯的密鑰保管庫。<br>如果刪除了密鑰保管庫,則無法恢復客戶託管密鑰。  如果將密鑰保管庫遷移到其他租戶,請參閱在[訂閱移動後更改密鑰保管庫租戶 ID。](https://docs.microsoft.com/azure/key-vault/key-vault-subscription-move-fix) |   否,如果客戶已刪除密鑰保管庫。<br> 是,如果密鑰保管庫進行了租戶遷移,則執行以下操作之一: <ol><li>將密鑰保管庫移回舊租戶。</li><li>設定識別 = 無,然後傳回識別 = 系統分配,這會刪除並重新建立識別</li></ol>|

## <a name="next-steps"></a>後續步驟

- [什麼是 Azure 金鑰保存嗎](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
