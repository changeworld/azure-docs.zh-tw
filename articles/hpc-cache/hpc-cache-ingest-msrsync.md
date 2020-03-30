---
title: Azure HPC 緩存資料引入 - msrsync
description: 如何使用 msrsync 將資料移動到 Azure HPC 緩存中的 Blob 存儲目標
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 4f8863d706d623d613ac156cf202c3b7b12f2ae0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74168434"
---
# <a name="azure-hpc-cache-data-ingest---msrsync-method"></a>Azure HPC 緩存資料引入 - msrsync 方法

本文詳細介紹了使用實用程式``msrsync``將資料複製到 Azure Blob 存儲容器，以便與 Azure HPC 緩存一起使用。

要瞭解有關將資料移動到 Azure HPC 緩存的 Blob 存儲的詳細資訊，請閱讀[將資料移動到 Azure Blob 存儲](hpc-cache-ingest.md)。

該工具``msrsync``可用於將資料移動到 Azure HPC 緩存的後端存儲目標。 此工具的設計目的是要藉由執行多個平行的 ``rsync`` 處理序，將頻寬使用情況最佳化。 您可以從 GitHub 取得它，網址為 https://github.com/jbd/msrsync。

``msrsync`` 會將來源目錄分解成個別的「貯體」，然後在每個貯體上執行個別的 ``rsync`` 處理序。

使用四核心 VM 的初步測試在使用 64 個處理序時所顯示的效率最佳。 請使用 ``msrsync`` 選項 ``-p`` 將處理序數目設定為 64。

請注意，``msrsync`` 只能在本機磁碟區寫入和寫出。 源和目標必須作為用於發出命令的工作站上的本地裝載進行訪問。

按照以下說明使用 Azure ``msrsync`` HPC 緩存填充 Azure Blob 存儲：

1. 安裝``msrsync``及其先決條件（``rsync``和 Python 2.6 或更高版本）
1. 決定要複製的檔案和目錄總數。

   例如，將實用程式``prime.py``與參數```prime.py --directory /path/to/some/directory```一起使用（可通過下載<https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py>可用）。

   如果不使用``prime.py``，則可以使用 GNU``find``工具計算專案數，如下所示：

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. 將項目數除以 64 以決定每一處理序的項目數。 當您執行命令時，請將此數目與 ``-f`` 選項搭配使用來設定貯體的大小。

1. 發出複製``msrsync``檔的命令：

   ```bash
   msrsync -P --stats -p64 -f<ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   例如，此命令旨在將 64 個進程中的 11，000 個檔從 /test/源存儲庫移動到 /mnt/hpccache/存儲庫：

   ``mrsync -P --stats -p64 -f170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/hpccache/repository``
