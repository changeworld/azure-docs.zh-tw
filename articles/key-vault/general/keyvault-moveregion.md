---
title: Azure Key Vault 將保存庫移至不同的區域 |Microsoft Docs
description: 將金鑰保存庫移至不同區域的指引。
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to move my vault to another region.
ms.openlocfilehash: e65a723ac9daafdc09896a50e197034104408df2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82254144"
---
# <a name="moving-an-azure-key-vault-across-regions"></a>跨區域移動 Azure Key Vault

## <a name="overview"></a>概觀

Key Vault 不支援允許將金鑰保存庫移至另一個區域的資源移動作業。 如果您有業務需要將金鑰保存庫移至另一個區域，本文將涵蓋因應措施。 每個選項都有限制，在實際執行環境中嘗試使用這些因應措施之前，請務必先瞭解這些因應措施的含意。

如果您需要將金鑰保存庫移至另一個區域，解決方案會在所需的區域中建立新的金鑰保存庫，並從現有的金鑰保存庫將每個個別的秘密手動複製到新的金鑰保存庫。 這項作業可以用下列其中一種方式完成，如下所列。

## <a name="design-considerations"></a>設計考量

* Key Vault 名稱是全域唯一的。 您將無法重複使用相同的保存庫名稱。

* 您將需要在新的金鑰保存庫中重新設定存取原則和網路設定。

* 您必須在新的金鑰保存庫中重新設定虛刪除和清除保護。

* 備份和還原作業不會保留 autorotation 設定，您可能需要重新設定這些設定。

## <a name="option-1---use-the-key-vault-backup-and-restore-commands"></a>選項 1-使用 key vault 備份和還原命令

您可以使用 backup 命令，在保存庫中備份每個個別的秘密、金鑰和憑證。 您的密碼將會下載為加密的 blob。 然後，您可以將 blob 還原至新的金鑰保存庫。 命令記載于下面的連結。

[Azure Key Vault 命令](https://docs.microsoft.com/powershell/module/azurerm.keyvault/?view=azurermps-6.13.0#key_vault)

### <a name="limitations"></a>限制

* 您無法在一個地理位置備份金鑰保存庫，並將它還原到另一個地理位置。 深入瞭解 Azure 地理位置。 [連結](https://azure.microsoft.com/global-infrastructure/geographies/)

* Backup 命令會備份每個秘密的所有版本。 如果您的密碼具有大量舊版（大於10），則要求可能會超過允許的要求大小上限，而作業也會失敗。

## <a name="option-2---manually-download-and-upload-secrets"></a>選項 2-手動下載並上傳秘密

您可以手動下載特定的秘密類型。 例如，您可以將憑證下載為 .pfx 檔案。 此選項可排除某些秘密類型的地理限制，例如憑證。 您可以將 .pfx 檔案上傳到任何區域中的任何金鑰保存庫。 您的密碼將會以非密碼保護的格式下載。 當您在執行移動時，您會負責保護秘密 Key Vault。
