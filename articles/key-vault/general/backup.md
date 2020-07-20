---
title: Azure Key Vault 備份 | Microsoft Docs
description: 使用本文來協助您備份儲存在 Azure Key Vault 中的祕密、金鑰或憑證。
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: sudbalas
ms.openlocfilehash: 8a152e2771f0b207e81f42c6ecae2e4d14605051
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86156304"
---
# <a name="azure-key-vault-backup"></a>Azure Key Vault 備份

本文件將說明如何為儲存在金鑰保存庫中的個別祕密、金鑰和憑證執行備份。 此備份的目的是要在萬一您無法存取金鑰保存庫時，為您提供所有秘密的離線複本。

## <a name="overview"></a>概觀

Key Vault 會自動提供幾項功能來維護可用性並防止資料遺失。 您應只在有重要業務理由，而需保存祕密的備份時，才嘗試執行此備份。 備份金鑰保存庫中的祕密可能會造成額外的作業挑戰，例如，在祕密過期或輪替時維護多組記錄、權限和備份。

Key Vault 會在發生災害的情況下保有可用性，並會自動將要求容錯移轉至配對的區域，而不需要使用者介入。 如需詳細資訊，請參閱下方連結。 [Azure Key Vault 災害復原](https://docs.microsoft.com/azure/key-vault/general/disaster-recovery-guidance)

Key Vault 會透過虛刪除和清除保護，防止秘密遭到意外及惡意刪除。 如果您想要防止祕密遭到意外或惡意刪除，請在金鑰保存庫上設定虛刪除和清除保護功能。 如需詳細資訊，請參閱下列文件。 [Azure Key Vault 復原](https://docs.microsoft.com/azure/key-vault/general/overview-soft-delete)

## <a name="limitations"></a>限制

Azure Key Vault 目前不支援在單一作業中備份整個金鑰保存庫的方式。 使用本文件所列命令對金鑰保存庫執行自動備份的任何嘗試，都不會受到 Microsoft 或 Azure Key Vault 小組的支援。

嘗試使用下列文件中顯示的命令建立自訂自動化，可能會導致錯誤。

* 備份具有多個版本的祕密可能會造成逾時錯誤。
* 備份會建立時間點快照集。 祕密可能會在備份期間更新，而這會導致加密金鑰不符。
* 超過金鑰保存庫服務的每秒要求數限制會使金鑰保存庫受到節流，並導致備份失敗。

## <a name="design-considerations"></a>設計考量

當您備份儲存在金鑰保存庫中的物件時 (祕密、金鑰或憑證)，備份作業會將物件下載為加密的 Blob。 此 Blob 無法在 Azure 外部解密。 若要從此 Blob 取得可用的資料，您必須將 Blob 還原到相同 Azure 訂用帳戶和 Azure 地理位置中的金鑰保存庫。
[Azure 地理位置](https://azure.microsoft.com/global-infrastructure/geographies/)

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶上的參與者層級權限或更高權限
* 主要金鑰保存庫，其中包含您想要備份的祕密
* 將在其中還原祕密的次要金鑰保存庫。

## <a name="back-up-and-restore-with-azure-portal"></a>使用 Azure 入口網站進行備份和還原

### <a name="back-up"></a>備份

1. 瀏覽至 Azure 入口網站。
2. 選取您的金鑰保存庫。
3. 瀏覽至您想要備份的物件 (秘密、金鑰或憑證)。

    ![映像](../media/backup-1.png)

4. 選取物件。
5. 選取 [下載備份]

    ![映像](../media/backup-2.png)
    
6. 按一下 [下載] 按鈕。

    ![映像](../media/backup-3.png)
    
7. 將加密的 Blob 儲存在安全的位置。

### <a name="restore"></a>還原

1. 瀏覽至 Azure 入口網站。
2. 選取您的金鑰保存庫。
3. 瀏覽至您想要還原的物件類型 (秘密、金鑰或憑證)。
4. 選取 [還原備份]

    ![映像](../media/backup-4.png)
    
5. 瀏覽至您儲存加密 Blob 的位置。
6. 選取 [確定]。

## <a name="back-up-and-restore-with-the-azure-cli"></a>使用 Azure CLI 進行備份和還原

```azurecli
## Login To Azure
az login

## Set your Subscription
az account set --subscription {AZURE SUBSCRIPTION ID}

## Register Key Vault as a Provider
az provider register -n Microsoft.KeyVault

## Backup a Certificate in Key Vault
az keyvault certificate backup --file {File Path} --name {Certificate Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Backup a Key in Key Vault
az keyvault key backup --file {File Path} --name {Key Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Backup a Secret in Key Vault
az keyvault secret backup --file {File Path} --name {Secret Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a Certificate in Key Vault
az keyvault certificate restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a Key in Key Vault
az keyvault key restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a Secret in Key Vault
az keyvault secret restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

```

## <a name="next-steps"></a>後續步驟

開啟 Azure Key Vault 的記錄和監視功能。 [Azure 金鑰保存庫記錄](https://docs.microsoft.com/azure/key-vault/general/logging)
