---
title: Azure Key Vault 與 Azure Stack Edge 資源和裝置啟用的整合
description: 描述在 Azure Stack Edge Pro 裝置啟用期間，Azure Key Vault 如何與秘密管理產生關聯。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: alkohli
ms.openlocfilehash: 8957d8982a3bfe1da2811dc10d0c3e77a72fc288
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92367596"
---
# <a name="azure-key-vault-integration-with-azure-stack-edge"></a>Azure Key Vault 與 Azure Stack Edge 整合 

Azure Key Vault 已與 Azure Stack Edge 的資源整合，以進行秘密管理。 本文提供在裝置啟用期間如何針對 Azure Stack Edge 資源建立 Azure Key Vault 的詳細資料，然後用於秘密管理。 


## <a name="about-key-vault-and-azure-stack-edge"></a>關於 Key vault 和 Azure Stack Edge

Azure Key Vault 雲端服務可用來安全地儲存及控制權杖、密碼、憑證、API 金鑰和其他秘密的存取權。 Key Vault 也可讓您輕鬆地建立和控制用來加密資料的加密金鑰。 如需有關允許的交易和對應費用的詳細資訊，請參閱 [Azure Key Vault 的定價](https://azure.microsoft.com/pricing/details/key-vault/)。

針對 Azure Stack Edge 服務，使用的其中一個秘密是 (CIK) 的通道完整性金鑰。 此金鑰可讓您加密秘密。 透過金鑰保存庫的整合，CIK 會安全地儲存在金鑰保存庫中。 如需詳細資訊，請參閱 [安全地儲存秘密和金鑰](../key-vault/general/overview.md#securely-store-secrets-and-keys)。


## <a name="key-vault-creation"></a>金鑰保存庫建立

在產生啟用金鑰的過程中，會為 Azure Stack Edge 資源建立金鑰保存庫。 

- 當您建立 Azure Stack Edge 資源時，您必須註冊 *KeyVault* 資源提供者。 如果您擁有訂用帳戶的擁有者或參與者存取權，則會自動註冊資源提供者。 金鑰保存庫會建立在與 Azure Stack Edge 資源相同的訂用帳戶和資源群組中。 

- 當您建立 Azure Stack Edge 資源時，也會建立受控服務識別 (MSI) ，它會在資源的存留期間持續存在，並與雲端上的資源提供者通訊。 

    啟用 MSI 時，Azure 會為 Azure Stack Edge 資源建立受信任的身分識別。

- 在您建立 Azure Stack Edge 資源並從 Azure 入口網站產生啟用金鑰之後，就會建立金鑰保存庫。 此金鑰保存庫可用於秘密管理，只要有 Azure Stack Edge 資源存在，就會持續保存。 

    ![產生啟用金鑰時所建立的 Key Vault](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-3.png)

- 您可以選擇接受預設金鑰名稱，或指定金鑰保存庫的自訂名稱。 金鑰保存庫名稱的長度必須介於3到24個字元之間。 您無法使用已在使用中的金鑰保存庫。 <!--The MSI is then used to authenticate to key vault to retrieve secrets.--> 

    ![Azure Stack Edge 資源建立期間建立的 MSI](media/azure-stack-edge-gpu-deploy-prep/create-resource-8.png)

- 若要流覽至 Azure 金鑰保存庫，請移至您 Azure Stack Edge 資源中的 **屬性** ，然後選取金鑰保存庫名稱。 

- 為了防止意外刪除，金鑰保存庫上已啟用資源鎖定。 在金鑰保存庫上也會啟用虛刪除，如果意外刪除，則會在90天內還原金鑰保存庫。 如需詳細資訊，請參閱 [Azure Key Vault 虛刪除總覽](../key-vault/general/soft-delete-overview.md)

    如果意外刪除金鑰保存庫，且未經過90天的清除保護持續時間，請遵循下列步驟來 [復原您的金鑰保存庫](../key-vault/general/soft-delete-powershell.md#recovering-a-key-vault)。 

- 如果您有現有的 Azure Stack Edge 資源，且 Azure Key Vault 與 Azure Stack Edge 資源整合，則不會受到影響。 您可以繼續使用現有的 Azure Stack Edge 資源。 

- 刪除您的 Azure Stack Edge 資源時，也會一併刪除該資源的 Azure Key Vault。 系統會提示您進行確認。 如果您不想要刪除此金鑰保存庫，您可以選擇不提供同意。 只會刪除 Azure Stack Edge 資源，讓金鑰保存庫保持不變。 

- 如果此金鑰保存庫是用來儲存其他金鑰，您仍然可以在刪除後的90天內將其還原。 在該清除保護期間，金鑰保存庫名稱無法用來建立新的金鑰保存庫。

如果您遇到與金鑰保存庫和裝置啟用相關的任何問題，請參閱針對 [裝置啟用問題進行疑難排解](azure-stack-edge-gpu-troubleshoot-activation.md)。

<!--## Key vault secret management

When you generate an activation key, the following events occur:

1. You request an activation key in the Azure portal. The request is then sent to Key Vault resource provider. 
1. A standard tier key vault with access policy is created and is locked by default. This key vault uses the default name or the custom name that you specified.
1. The key vault authenticates with MSI the request to generate activation key. The MSI is also added to the key vault access policy and a channel integrity key is generated and placed in the key vault.
1. The activation key is returned to the Azure portal. You can then copy this key and use it in the local UI to activate your device.-->



## <a name="next-steps"></a>後續步驟

- 深入瞭解如何 [產生啟用金鑰](azure-stack-edge-gpu-deploy-prep.md#get-the-activation-key)。

