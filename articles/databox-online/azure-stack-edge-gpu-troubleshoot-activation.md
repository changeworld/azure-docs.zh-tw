---
title: 使用 Azure 入口網站針對 Azure Stack Edge Pro 與 GPU 相關的啟用錯誤進行疑難排解 |Microsoft Docs
description: 說明如何針對 Azure Stack Edge Pro GPU 啟用和金鑰保存庫的相關問題進行疑難排解。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 10/08/2020
ms.author: alkohli
ms.openlocfilehash: e93a7fd7aec5463a3d77bd9d6bb17d7072097870
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96447632"
---
# <a name="troubleshoot-activation-issues-on-your-azure-stack-edge-pro-gpu-device"></a>針對 Azure Stack Edge Pro GPU 裝置上的啟用問題進行疑難排解 

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

本文說明如何針對 Azure Stack Edge Pro GPU 裝置上的啟用問題進行疑難排解。 


## <a name="activation-errors"></a>啟用錯誤

下表摘要說明裝置啟用和對應的建議解決方案的相關錯誤。

| 錯誤訊息| 建議的解決方式 |
|------------------------------------------------------|--------------------------------------|
| 如果在使用啟用金鑰啟用裝置之前刪除用於啟用的 Azure Key Vault，則會收到此錯誤。 <br> ![Key vault 錯誤1](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-1.png)  | 如果已刪除金鑰保存庫，您可以在保存庫處於清除保護期間時復原金鑰保存庫。 請依照 [復原金鑰保存庫](../key-vault/general/key-vault-recovery.md#list-recover-or-purge-soft-deleted-secrets-keys-and-certificates)中的步驟執行。 <br>如果清除保護持續時間已過，則無法復原金鑰保存庫。 連絡 Microsoft 支援服務以進行後續步驟。 |
| 如果在裝置啟動之後刪除了 Azure Key Vault，然後您嘗試執行任何涉及加密的作業，例如： **新增使用者**、 **新增共用**、 **設定計算**，則會收到此錯誤。 <br> ![金鑰保存庫錯誤2](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-2.png)    | 如果已刪除金鑰保存庫，您可以在保存庫處於清除保護期間時復原金鑰保存庫。 請依照復原金鑰保存庫中的步驟執行。 <br>如果清除保護持續時間已過，則無法復原金鑰保存庫。 連絡 Microsoft 支援服務以進行後續步驟。 |
| 如果 Azure Key Vault 中的通道完整性金鑰遭到刪除，然後再嘗試執行任何涉及加密的作業，例如： **新增使用者**、 **新增共用**、 **設定計算** -然後您將會收到此錯誤。 <br> ![金鑰保存庫錯誤3](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-3.png) | 如果金鑰保存庫中的通道完整性金鑰遭到刪除，但仍在清除期間內，請依照 [復原金鑰保存庫金鑰移除](/powershell/module/az.keyvault/undo-azkeyvaultkeyremoval)中的步驟執行。 <br>如果清除保護持續時間已過，且您已備份金鑰，您可以從備份還原，否則無法復原金鑰。 連絡 Microsoft 支援服務以進行後續步驟。 |
| 如果啟動金鑰產生因任何錯誤而失敗，則會收到此錯誤。 通知中有其他詳細資料。 <br> ![金鑰保存庫錯誤4](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-4.png)   | 請稍候幾分鐘，然後重試此作業。 如果問題持續發生，請連絡 Microsoft 支援服務。 |
| 如果使用者具有唯讀許可權，則不允許使用者產生啟用金鑰，且會出現此錯誤。 <br> ![金鑰保存庫錯誤5](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-5.png) | 這可能是因為您沒有正確的存取權，或未註冊  *KeyVault* 。<li>請確定您在用於 Azure Stack Edge 資源的資源群組層級上有擁有者或參與者存取權。</li><li>請確定已註冊 KeyVault 資源提供者。 若要註冊資源提供者，請移至用於 Azure Stack Edge 資源的訂用帳戶。 移至 [ **資源提供者**]、搜尋 *KeyVault* ，然後選取並 **註冊**。</li> |

## <a name="next-steps"></a>後續步驟

- 深入瞭解如何針對 [裝置問題進行疑難排解](azure-stack-edge-gpu-troubleshoot.md)。