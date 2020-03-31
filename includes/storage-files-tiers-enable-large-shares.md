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
ms.openlocfilehash: 8a2e5defd0672516d52d4f3477641f39eca63368
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597772"
---
預設情況下，標準檔共用只能跨越 5 TiB，儘管共用限制可以增加到 100 TiB。 為此，必須在存儲帳戶級別啟用*大型檔共用*功能。 高級存儲帳戶（*檔存儲*存儲帳戶）沒有大型檔共用功能標誌，因為已啟用所有高級檔共用以預配高達 100 個 TiB 的全部容量。

您只能在本地冗余或區域冗余標準存儲帳戶上啟用大型檔共用。 啟用大型檔共用功能標誌後，無法將冗余級別更改為異地冗余或地理區域冗余存儲。

要在現有存儲帳戶上啟用大型檔共用，請導航到存儲帳戶目錄中的 **"配置"** 視圖，並將大型檔共用搖臂開關切換到已啟用：

![Azure 門戶中啟用大型檔共用搖臂開關的螢幕截圖](media/storage-files-tiers-enable-large-shares/enable-lfs-0.png)

還可以通過[`Set-AzStorageAccount`](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount)PowerShell Cmdlet 和[`az storage account update`](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-update)Azure CLI 命令啟用 100 個 TiB 檔共用。

要瞭解有關如何在新存儲帳戶上啟用大型檔共用的更多詳細資訊，請參閱[創建 Azure 檔共用](../articles/storage/files/storage-how-to-create-file-share.md)。