---
title: 將金鑰保存庫移至不同的區域 - Azure Key Vault |Microsoft Docs
description: 本文會說明如何將金鑰保存庫移至不同區域。
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
ms.openlocfilehash: 4f9f43b3d0aa0af8696300933c08c140951e5e52
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83651235"
---
# <a name="move-an-azure-key-vault-across-regions"></a>在區域之間移動 Azure 金鑰保存庫

Azure Key Vault 不支援資源移動作業，此作業可將金鑰保存庫從一個區域移至另一個區域。 本文會介紹適用於因業務需要而需將金鑰保存庫移至另一個區域的組織的因應措施。 各種因應措施都有其限制。 請務必先了解因應措施可能帶來的影響，再於實際執行環境採用因應措施。

若要將金鑰保存庫移至另一個區域，請在目標區域中建立金鑰保存庫，再手動將各項秘密從目前的金鑰保存庫複製到新的金鑰保存庫。 以下兩種選項可擇一使用。

## <a name="design-considerations"></a>設計考量

開始進行前，請記住以下概念：

* 金鑰保存庫名稱為全域唯一。 您無法重複使用保存庫名稱。
* 您需要在新的金鑰保存庫中重新設定存取原則和網路設定。
* 您需要在新的金鑰保存庫中重新設定虛刪除和清除保護。
* 備份和還原作業不會保留您的自動輪換設定。 您可能需要重新設定。

## <a name="option-1-use-the-key-vault-backup-and-restore-commands"></a>選項 1：使用金鑰保存庫備份和還原命令

您可以使用備份命令來備份保存庫中的各個秘密、金鑰和憑證。 您的秘密會下載為加密的 blob， 接著即可將 blob 還原至新的金鑰保存庫。 如需命令清單，請參閱 [Azure Key Vault 命令](https://docs.microsoft.com/powershell/module/azurerm.keyvault/?view=azurermps-6.13.0#key_vault) (英文)。

使用備份和還原命令有兩個限制：

* 您無法備份某個地理區的金鑰保存庫，再還原到另一個地理區。 如需詳細資訊，請參閱 [Azure 的地理區](https://azure.microsoft.com/global-infrastructure/geographies/)。

* 備份命令會備份每項秘密的所有版本。 如果您的祕密有非常多的舊有版本 (超過 1 0個)，則要求大小或許會超過允許上限，作業可能會失敗。

## <a name="option-2-manually-download-and-upload-the-key-vault-secrets"></a>選項 2：手動下載並上傳金鑰保存庫祕密

您可以手動下載特定的秘密類型。 例如，您可以將憑證下載為 PFX 檔案。 此選項可去除某些秘密類型的地理限制 (例如憑證)。 您可以將 PFX 檔案上傳到任何地理區的任何金鑰保存庫。 會以無密碼保護格式下載秘密。 移動期間由您負責保護您的秘密。
