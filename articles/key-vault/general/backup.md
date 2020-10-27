---
title: 備份儲存在 Azure Key Vault 中的祕密、金鑰或憑證 | Microsoft Docs
description: 使用本文來協助您備份儲存在 Azure Key Vault 中的祕密、金鑰或憑證。
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/22/2020
ms.author: sudbalas
ms.openlocfilehash: 931aea02d0a3c26bb5c2e7158f9c4360976d3af5
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92440513"
---
# <a name="azure-key-vault-backup"></a>Azure Key Vault 備份

本文件說明如何備份金鑰保存庫中所儲存的祕密、金鑰和憑證。 備份的目的是要在萬一您無法存取金鑰保存庫時，為您提供所有秘密的離線複本。

## <a name="overview"></a>概觀

Azure Key Vault 會自動提供幾項功能來協助您維護可用性並防止資料遺失。 只有在重要的業務理由時，才備份祕密。 備份金鑰保存庫中的祕密可能會造成作業挑戰，例如，在祕密過期或輪替時維護多組記錄、權限和備份。

Key Vault 會在發生災害的情況下保有可用性，並會自動將要求容錯移轉至配對的區域，而不需要使用者介入。 如需詳細資訊，請參閱 [Azure Key Vault 可用性與備援](https://docs.microsoft.com/azure/key-vault/general/disaster-recovery-guidance)。

如果您想要防止祕密遭到意外或惡意刪除，請在金鑰保存庫上設定虛刪除和清除保護功能。 如需詳細資訊，請參閱 [Azure Key Vault 虛刪除概觀](https://docs.microsoft.com/azure/key-vault/general/soft-delete-overview)。

## <a name="limitations"></a>限制

> [!IMPORTANT]
> Key Vault 不支援備份超過 500 個舊版的金鑰、密碼或憑證物件的功能。 嘗試備份金鑰、密碼或憑證物件可能會導致錯誤。 無法刪除舊版的金鑰、密碼或憑證。

Key Vault 目前不提供在單一作業中備份整個金鑰保存庫的支援方式。 使用本文件所列命令對金鑰保存庫執行自動備份的任何嘗試可能會導致錯誤，且不會受到 Microsoft 或 Azure Key Vault 小組的支援。 

也請考慮可能出現下列結果：

* 備份具有多個版本的祕密可能會造成逾時錯誤。
* 備份會建立時間點快照集。 祕密可能會在備份期間更新，而這會導致加密金鑰不符。
* 如果超過金鑰保存庫服務的每秒要求數限制，您的金鑰保存庫會受到節流，並導致備份失敗。

## <a name="design-considerations"></a>設計考量

備份儲存金鑰保存庫物件時 (例如祕密、金鑰或憑證)，備份作業會將物件下載為加密的 Blob。 此 Blob 無法在 Azure 外部解密。 若要從此 Blob 取得可用的資料，您必須將 Blob 還原到相同 Azure 訂用帳戶和 [Azure 地理位置](https://azure.microsoft.com/global-infrastructure/geographies/)中的金鑰保存庫。

## <a name="prerequisites"></a>必要條件

若要備份金鑰保存庫物件，您必須具有： 

* Azure 訂用帳戶上的參與者層級權限或更高權限。
* 主要金鑰保存庫，其中包含您想要備份的祕密。
* 將在其中還原祕密的次要金鑰保存庫。

## <a name="back-up-and-restore-from-the-azure-portal"></a>從 Azure 入口網站進行備份與還原

遵循本節中的步驟，使用 Azure 入口網站來備份和還原物件。

### <a name="back-up"></a>備份

1. 移至 Azure 入口網站。
2. 選取您的金鑰保存庫。
3. 移至您想要備份的物件 (秘密、金鑰或憑證)。

    ![螢幕擷取畫面，顯示要選取金鑰設定和金鑰保存庫中物件的位置。](../media/backup-1.png)

4. 選取物件。
5. 選取 [下載備份]。

    ![螢幕擷取畫面，顯示要選取金鑰保存庫中下載備份按鈕的位置 。](../media/backup-2.png)
    
6. 選取 [下載]。

    ![螢幕擷取畫面，顯示要選取金鑰保存庫中下載按鈕的位置 。](../media/backup-3.png)
    
7. 將加密的 Blob 儲存在安全的位置。

### <a name="restore"></a>還原

1. 移至 Azure 入口網站。
2. 選取您的金鑰保存庫。
3. 移至至您想要還原的物件類型 (秘密、金鑰或憑證)。
4. 選取 [還原備份]。

    ![螢幕擷取畫面，顯示要選取金鑰保存庫中還原備份的位置 。](../media/backup-4.png)
    
5. 移至您儲存加密 Blob 的位置。
6. 選取 [確定]。

## <a name="back-up-and-restore-from-the-azure-cli"></a>從 Azure CLI 進行備份與還原

```azurecli
## Log in to Azure
az login

## Set your subscription
az account set --subscription {AZURE SUBSCRIPTION ID}

## Register Key Vault as a provider
az provider register -n Microsoft.KeyVault

## Back up a certificate in Key Vault
az keyvault certificate backup --file {File Path} --name {Certificate Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Back up a key in Key Vault
az keyvault key backup --file {File Path} --name {Key Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Back up a secret in Key Vault
az keyvault secret backup --file {File Path} --name {Secret Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a certificate in Key Vault
az keyvault certificate restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a key in Key Vault
az keyvault key restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a secret in Key Vault
az keyvault secret restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

```

## <a name="next-steps"></a>後續步驟

開啟 Key Vault 的[記錄和監視](https://docs.microsoft.com/azure/key-vault/general/logging)功能。
