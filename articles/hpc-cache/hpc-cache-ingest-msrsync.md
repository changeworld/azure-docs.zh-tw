---
title: Azure HPC Cache 資料內嵌-msrsync
description: 如何使用 msrsync 將資料移至 Azure HPC Cache 中的 Blob 儲存體目標
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 10/30/2019
ms.author: v-erkel
ms.openlocfilehash: 323ecd6a2dd001c3c8df1b2ec15f0ae8402ec70c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87092400"
---
# <a name="azure-hpc-cache-data-ingest---msrsync-method"></a>Azure HPC Cache 資料內嵌-msrsync 方法

本文提供詳細指示，說明如何使用 ``msrsync`` 公用程式，將資料複製到 Azure Blob 儲存體容器以搭配 Azure HPC Cache 使用。

若要深入瞭解如何將資料移至 Azure HPC Cache 的 Blob 儲存體，請參閱 [將資料移至 Azure blob 儲存體](hpc-cache-ingest.md)。

您 ``msrsync`` 可以使用此工具，將資料移至 Azure HPC Cache 的後端儲存體目標。 此工具的設計目的是要藉由執行多個平行的 ``rsync`` 處理序，將頻寬使用情況最佳化。 您可以從 GitHub 取得它，網址為 https://github.com/jbd/msrsync。

``msrsync`` 會將來源目錄分解成個別的「貯體」，然後在每個貯體上執行個別的 ``rsync`` 處理序。

使用四核心 VM 的初步測試在使用 64 個處理序時所顯示的效率最佳。 請使用 ``msrsync`` 選項 ``-p`` 將處理序數目設定為 64。

請注意，``msrsync`` 只能在本機磁碟區寫入和寫出。 來源和目的地必須可作為用來發出命令的工作站上的本機裝載來存取。

請遵循下列指示，以使用 ``msrsync`` Azure HPC Cache 填入 Azure Blob 儲存體：

1. 安裝及其 ``msrsync`` 必要條件 (``rsync`` 和 Python 2.6 或更新版本) 
1. 決定要複製的檔案和目錄總數。

   例如，您 ``prime.py`` ```prime.py --directory /path/to/some/directory``` 可以藉由下載) 來使用包含引數 (的公用程式 <https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py> 。

   如果未使用 ``prime.py`` ，您可以使用 GNU 工具來計算專案的數目，如下所示 ``find`` ：

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. 將項目數除以 64 以決定每一處理序的項目數。 當您執行命令時，請將此數目與 ``-f`` 選項搭配使用來設定貯體的大小。

1. 發出 ``msrsync`` 下列命令來複製檔案：

   ```bash
   msrsync -P --stats -p64 -f<ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   例如，此命令的設計目的是將64進程中的11000檔案從/test/source-repository 移至/mnt/hpccache/repository：

   ``mrsync -P --stats -p64 -f170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/hpccache/repository``
