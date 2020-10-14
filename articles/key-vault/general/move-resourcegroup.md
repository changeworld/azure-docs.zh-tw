---
title: Azure Key Vault 將保存庫移至不同的資源群組 |Microsoft Docs
description: 將金鑰保存庫移至不同資源群組的指引。
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 04/29/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to move my vault to another resource group.
ms.openlocfilehash: ea152e1d78ab1ea610eee5420394c89524673993
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92042377"
---
# <a name="moving-an-azure-key-vault-across-resource-groups"></a>在資源群組之間移動 Azure Key Vault

## <a name="overview"></a>總覽

在資源群組之間移動金鑰保存庫是支援的金鑰保存庫功能。 在資源群組之間移動金鑰保存庫並不會影響金鑰保存庫防火牆或存取原則設定。 已連線的應用程式和服務主體應會繼續如預期運作。

> [!IMPORTANT]
> **用於磁片加密的金鑰保存庫無法移動。**
> 如果您使用 key vault 搭配 VM 的磁片加密，則在啟用磁片加密時，金鑰保存庫無法移至不同的資源群組或訂用帳戶。 將金鑰保存庫移至新的資源群組或訂用帳戶之前，您必須先停用磁片加密。 

## <a name="design-considerations"></a>設計考量

您的組織可能已透過在資源群組層級強制執行或排除專案的方式來執行 Azure 原則。 資源群組中可能會有一組不同的原則指派，也就是您的金鑰保存庫目前存在的資源群組，以及您要移動金鑰保存庫的資源群組。 原則需求的衝突可能會中斷您的應用程式。

### <a name="example"></a>範例

您的應用程式已連線至金鑰保存庫，可建立兩年有效的憑證。 您嘗試移動金鑰保存庫的資源群組具有原則指派，會封鎖建立有效期限超過一年的憑證。 將您的金鑰保存庫移至新的資源群組之後，用來建立兩年有效憑證的作業將會被 Azure 原則指派封鎖。

### <a name="solution"></a>解決方案

請確定您移至 Azure 入口網站上的 [Azure 原則] 頁面，並查看目前資源群組的原則指派，以及您要移至的資源群組，並確定沒有任何不符的情況。

## <a name="procedure"></a>程序

1. 登入 Azure 入口網站
2. 瀏覽至您的金鑰保存庫
3. 按一下 [總覽] 索引標籤
4. 選取 [移動] 按鈕
5. 從下拉式選項中選取 [移至另一個資源群組]
6. 選取您要在其中移動金鑰保存庫的資源群組
7. 確認關於移動資源的警告
8. 選取 [確定]

Key Vault 現在會評估資源移動的有效性，並在發生任何錯誤時發出警示。 如果找不到任何錯誤，將會完成資源移動。 
