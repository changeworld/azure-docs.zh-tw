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
ms.openlocfilehash: cd7b889560acbe484581f065b641375c222f7ca8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81536471"
---
根據預設，標準檔案共用最多隻能跨越5個 TiB，不過共用限制可以增加至 100 TiB。 若要這麼做，必須在儲存體帳戶層級啟用*大型檔案共用*功能。 高階儲存體帳戶（*FileStorage*儲存體帳戶）沒有大型檔案共用功能旗標，因為所有 premium 檔案共用都已啟用布建至完整 100 TiB 容量。

您只能在本機多餘或區域多餘的標準儲存體帳戶上啟用大型檔案共用。 一旦您啟用了大型檔案共用功能旗標，就無法將冗余層級變更為異地多餘或異地區域冗余存放裝置。

若要在現有的儲存體帳戶上啟用大型檔案共用，請流覽至儲存體帳戶目錄中的 [設定] 視圖，然後將 [大型檔案共用滾輪] 切換**至 [已**啟用]：

![Azure 入口網站中 [啟用大型檔案共用滾輪] 參數的螢幕擷取畫面](media/storage-files-tiers-enable-large-shares/enable-lfs-0.png)

您也可以透過[`Set-AzStorageAccount`](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount) PowerShell Cmdlet 和[`az storage account update`](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-update) Azure CLI 命令來啟用 100 TiB 檔案共用。 如需啟用大型檔案共用的詳細指示，請參閱[啟用和建立大型檔案共用](../articles/storage/files/storage-files-how-to-create-large-file-share.md)。

若要深入瞭解如何在新的儲存體帳戶上建立檔案共用，請參閱[建立 Azure 檔案共用](../articles/storage/files/storage-how-to-create-file-share.md)。
