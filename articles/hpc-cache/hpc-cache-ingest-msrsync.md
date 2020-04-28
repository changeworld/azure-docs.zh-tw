---
title: Azure HPC 快取資料內嵌-msrsync
description: 如何使用 msrsync 將資料移至 Azure HPC Cache 中的 Blob 儲存體目標
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 2e0442b6aa1404ae5f57445179979496faa09863
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82194970"
---
# <a name="azure-hpc-cache-data-ingest---msrsync-method"></a>Azure HPC 快取資料內嵌-msrsync 方法

本文提供詳細的指示，說明如何``msrsync``使用公用程式將資料複製到 azure Blob 儲存體容器，以與 azure HPC 快取搭配使用。

若要深入瞭解如何將資料移至 Azure HPC 快取的 Blob 儲存體，請參閱[將資料移至 Azure blob 儲存體](hpc-cache-ingest.md)。

此``msrsync``工具可以用來將資料移至 Azure HPC 快取的後端儲存體目標。 此工具的設計目的是要藉由執行多個平行的 ``rsync`` 處理序，將頻寬使用情況最佳化。 您可以從 GitHub 取得它，網址為 https://github.com/jbd/msrsync。

``msrsync`` 會將來源目錄分解成個別的「貯體」，然後在每個貯體上執行個別的 ``rsync`` 處理序。

使用四核心 VM 的初步測試在使用 64 個處理序時所顯示的效率最佳。 請使用 ``msrsync`` 選項 ``-p`` 將處理序數目設定為 64。

請注意，``msrsync`` 只能在本機磁碟區寫入和寫出。 來源和目的地必須可在用來發出命令的工作站上以本機掛接的形式存取。

遵循這些指示，以``msrsync``使用 azure HPC 快取來填入 azure Blob 儲存體：

1. 安裝``msrsync``及其必要條件（``rsync``和 Python 2.6 或更新版本）
1. 決定要複製的檔案和目錄總數。

   例如，使用公用程式``prime.py``搭配引數```prime.py --directory /path/to/some/directory``` （可透過下載<https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py>取得）。

   如果未使用``prime.py``，您可以使用 GNU ``find``工具來計算專案數，如下所示：

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. 將項目數除以 64 以決定每一處理序的項目數。 當您執行命令時，請將此數目與 ``-f`` 選項搭配使用來設定貯體的大小。

1. 發出``msrsync``命令以複製檔案：

   ```bash
   msrsync -P --stats -p64 -f<ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   例如，此命令是設計用來將64進程中的11000檔案從/test/source-repository 移至/mnt/hpccache/repository：

   ``mrsync -P --stats -p64 -f170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/hpccache/repository``
