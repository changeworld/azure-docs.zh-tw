---
title: Azure Key Vault 將保存庫移至不同的資源群組 |Microsoft Docs
description: 將金鑰保存庫移至不同資源群組的指導方針。
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 04/29/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to move my vault to another resource group.
ms.openlocfilehash: bbc27af9eb448911093473d6ab20fde8004c7b88
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87097592"
---
# <a name="moving-an-azure-key-vault-across-resource-groups"></a>跨資源群組移動 Azure Key Vault

## <a name="overview"></a>總覽

在資源群組之間移動金鑰保存庫是支援的金鑰保存庫功能。 在資源群組之間移動金鑰保存庫，並不會影響金鑰保存庫防火牆或存取原則設定。 連線的應用程式和服務主體應該會如預期繼續正常執行。

## <a name="design-considerations"></a>設計考量

您的組織可能已在資源群組層級，利用強制或排除來實作為 Azure 原則。 在您的金鑰保存庫目前所在的資源群組中，可能會有一組不同的原則指派，以及您要移動金鑰保存庫的資源群組。 原則需求的衝突可能會中斷您的應用程式。

### <a name="example"></a>範例

您的應用程式已連線到金鑰保存庫，以建立兩年的有效憑證。 您嘗試移動金鑰保存庫的資源群組具有原則指派，其會封鎖建立有效期超過一年的憑證。 將金鑰保存庫移至新的資源群組之後，Azure 原則指派將會封鎖用來建立有效期為兩年的憑證的作業。

### <a name="solution"></a>解決方案

請確定您已移至 Azure 入口網站上的 [Azure 原則] 頁面，並查看您目前資源群組的原則指派，以及您要移至的資源群組，並確定沒有任何不符的情況。

## <a name="procedure"></a>程序

1. 登入 Azure 入口網站
2. 瀏覽至您的金鑰保存庫
3. 按一下 [總覽] 索引標籤
4. 選取 [移動] 按鈕
5. 從下拉式清單選項中選取 [移至另一個資源群組]
6. 選取您想要移動金鑰保存庫的資源群組
7. 認可有關移動資源的警告
8. 選取 [確定]

Key Vault 現在會評估資源移動的有效性，並在發生任何錯誤時發出警示。 如果找不到任何錯誤，將會完成資源移動。 
