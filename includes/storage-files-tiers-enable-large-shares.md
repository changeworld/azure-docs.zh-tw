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
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536471"
---
默認情況下,標準檔共用只能跨越 5 TiB,儘管共用限制可以增加到 100 TiB。 為此,必須在存儲帳戶級別啟用*大型文件共用*功能。 進階儲存帳戶(*檔案儲存*儲存帳戶)沒有大型檔案共用功能標誌,因為已啟用所有高級檔案共用以預配高達 100 個 TiB 的全部容量。

您只能在本地冗餘或區域冗餘標準儲存帳戶上啟用大型文件共用。 啟用大型檔共用功能標誌後,無法將冗餘級別更改為異地冗餘或地理區域冗餘存儲。

要在現有儲存帳戶上啟用大型檔案共享,請瀏覽到儲存帳戶目錄中的 **「設定」** 檢視,並將大型檔案共用搖臂開關切換到已啟用:

![Azure 門戶開啟大型檔案共享搖臂開關的螢幕截圖](media/storage-files-tiers-enable-large-shares/enable-lfs-0.png)

還可以透過[`Set-AzStorageAccount`](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount)PowerShell cmdlet 和[`az storage account update`](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-update)Azure CLI 命令啟用 100 個 TiB 檔案共用。 有關啟用大型檔案分享的詳細說明,請參閱[啟用與建立大型檔案共享](../articles/storage/files/storage-files-how-to-create-large-file-share.md)。

要瞭解有關如何在新儲存帳戶上建立檔案分享的更多詳細資訊,請參閱[建立 Azure 檔案共用](../articles/storage/files/storage-how-to-create-file-share.md)。
