---
title: 包含檔案
description: 包含檔案
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 327e86642041a607ada7c1173bb053b12a41832c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011686"
---
根據預設，標準檔案共用最多只能跨越 5 TiB，不過共用限制可以增加至 100 TiB。 若要這麼做，您必須在儲存體帳戶層級啟用「大型檔案共用」功能。 進階儲存體帳戶 (*FileStorage* 儲存體帳戶) 沒有大型檔案共用功能旗標，因為所有進階檔案共用都已經啟用最多佈建至完整 100 TiB 容量。

您只能在本地備援或區域備援標準儲存體帳戶上啟用大型檔案共用。 一旦啟用大型檔案共用功能旗標，您就無法將備援層級變更為異地備援或異地區域備援儲存體。

若要在現有的儲存體帳戶上啟用大型檔案共用，請瀏覽至儲存體帳戶目錄中的 [組態] 檢視，並將大型檔案共用翹板開關切換為 [已啟用]：

![Azure 入口網站中啟用大型檔案共用翹板開關的螢幕擷取畫面](media/storage-files-tiers-enable-large-shares/enable-lfs-0.png)

您也可以透過 [`Set-AzStorageAccount`](/powershell/module/az.storage/set-azstorageaccount) PowerShell Cmdlet 和 [`az storage account update`](/cli/azure/storage/account#az-storage-account-update) Azure CLI 命令來啟用 100 TiB 檔案共用。 如需有關啟用大型檔案共用的詳細指示，請參閱[啟用和建立大型檔案共用](../articles/storage/files/storage-files-how-to-create-large-file-share.md)。

若要深入了解如何在新的儲存體帳戶上建立檔案共用，請參閱[建立 Azure 檔案共用](../articles/storage/files/storage-how-to-create-file-share.md)。