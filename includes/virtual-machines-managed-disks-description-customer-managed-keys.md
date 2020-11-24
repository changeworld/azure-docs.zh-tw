---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: bf79fe8766d36fe59595be43250341d49b5460c8
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95559520"
---
您可以選擇使用自己的金鑰來管理每個受控磁碟層級的加密。 使用客戶管理的金鑰對受控磁碟進行伺服器端加密，提供與 Azure Key Vault 的整合體驗。 您可以將[您的 RSA 金鑰](../articles/key-vault/keys/hsm-protected-keys.md)匯入 Key Vault，或在 Azure Key Vault 中產生新的 RSA 金鑰。 

Azure 受控磁碟會使用[信封加密](../articles/storage/common/storage-client-side-encryption.md#encryption-and-decryption-via-the-envelope-technique)，以完全透明的方式處理加密和解密。 其會使用以 [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 為基礎的資料加密金鑰 (DEK) 來加密資料，而這是使用您的金鑰來保護。 儲存體服務會產生資料加密金鑰，並使用 RSA 加密，透過客戶管理的金鑰進行加密。 信封加密可讓您根據您的相容性原則，定期輪替 (變更) 您的金鑰，而不會影響您的 VM。 當您輪替金鑰時，儲存體服務會使用新的客戶管理的金鑰，重新加密資料加密金鑰。 

#### <a name="full-control-of-your-keys"></a>完全掌控您的金鑰

您必須將存取權授與 Key Vault 中的受控磁片，才能使用您的金鑰來加密和解密 DEK。 這可讓您完全控制您的資料和金鑰。 您可以隨時停用金鑰或撤銷對受控磁碟的存取權。 您也可以使用 Azure Key Vault 監視稽核加密金鑰使用量，以確保只有受控磁碟或其他信任的 Azure 服務會存取您的金鑰。

當您停用或刪除金鑰時，任何使用該金鑰之磁片的 Vm 都會自動關機。 之後，除非再次啟用金鑰或指派新的金鑰，否則將無法使用 VM。

對於進階 SSD、標準 SSD，以及標準 HDD：當您停用或刪除您的金鑰時，對於磁碟使用該金鑰的任何 VM，都將自動關閉。 之後，除非再次啟用金鑰或指派新的金鑰，否則將無法使用 VM。    

Ultra 磁片：當您停用或刪除金鑰時，任何使用該金鑰的 ultra 磁片的 Vm 將不會自動關閉。 一旦解除配置並重新啟動 VM 之後，磁碟將會停止使用金鑰，而 VM 將不會重新上線。 若要讓 VM 重新上線，您必須指派新金鑰，或啟用現有金鑰。    

下圖顯示受控磁碟如何透過 Azure Active Directory 和 Azure Key Vault，使用客戶管理的金鑰提出要求：

:::image type="content" source="media/virtual-machines-managed-disks-description-customer-managed-keys/customer-managed-keys-sse-managed-disks-workflow.png" alt-text="受控磁片和客戶管理的金鑰工作流程。系統管理員會建立 Azure Key Vault，然後建立磁片加密集，並設定磁片加密集。此集合與 VM 相關聯，可讓磁片利用 Azure AD 來進行驗證":::

下列清單詳細說明圖表：

1. Azure Key Vault 管理員會建立金鑰保存庫資源。
1. 金鑰保存庫管理員會將其 RSA 金鑰匯入 Key Vault，或在 Key Vault 中產生新的 RSA 金鑰。
1. 管理員會建立磁碟加密集資源的執行個體、指定 Azure Key Vault ID 和金鑰 URL。 磁碟加密集是為了簡化受控磁碟的金鑰管理而導入的新資源。 
1. 磁碟加密集建立後，會在 Azure Active Directory (AD) 中建立[系統指派的受控識別](../articles/active-directory/managed-identities-azure-resources/overview.md)，並與磁碟加密集建立關聯。 
1. 然後，Azure Key Vault 管理員會授與受控識別權限，以在金鑰保存庫中執行作業。
1. VM 使用者可以藉由將磁碟與磁碟加密集建立關聯，以建立磁碟。 VM 使用者也可以藉由將客戶管理的金鑰與磁碟加密集建立關聯，針對現有資源使用客戶管理的金鑰，啟用伺服器端加密。 
1. 受控磁碟使用受控識別，將要求傳送至 Azure Key Vault。
1. 對於讀取或寫入資料，受控磁碟會將要求傳送至 Azure Key Vault，以加密 (包裝) 和解密 (解除包裝) 資料加密金鑰，以便執行資料的加密和解密。 

若要撤銷客戶管理的金鑰的存取權，請參閱 [Azure Key Vault PowerShell](/powershell/module/azurerm.keyvault/) 和 [Azure Key Vault CLI](/cli/azure/keyvault)。 撤銷存取權會有效封鎖對儲存體帳戶中所有資料的存取，因為 Azure 儲存體無法存取加密金鑰。