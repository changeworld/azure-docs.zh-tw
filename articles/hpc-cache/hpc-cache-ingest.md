---
title: 將資料移動到 Azure HPC 緩存雲容器
description: 如何填充 Azure Blob 存儲，以便與 Azure HPC 緩存一起使用
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: a5625341e3dd279d93a59c57cd3325245351723e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271872"
---
# <a name="move-data-to-azure-blob-storage"></a>將資料移動到 Azure Blob 存儲

如果工作流包括將資料移動到 Azure Blob 存儲，請確保使用有效的策略。 您可以將資料預載入到新的 Blob 容器中，然後再將其定義為存儲目標，也可以添加容器，然後使用 Azure HPC 緩存複製資料。

本文介紹了將資料移動到 Blob 存儲以便與 Azure HPC 緩存一起使用的最佳方式。

請記住以下事實：

* Azure HPC 緩存使用專用存儲格式來組織 Blob 存儲中的資料。 因此，Blob 存儲目標必須是新的空容器，或者以前用於 Azure HPC 緩存資料的 Blob 容器。 <!--([Avere vFXT for Azure](https://azure.microsoft.com/services/storage/avere-vfxt/) also uses this cloud file system.)-->

* 當您使用多個用戶端和平行作業時，通過 Azure HPC 緩存將資料複製到後端存儲目標的效率更高。 來自一個用戶端的簡單複製命令將緩慢移動資料。

基於 Python 的實用程式可用於將內容載入到 Blob 存儲容器中。 閱讀[Blob 存儲中的預載入資料](#pre-load-data-in-blob-storage-with-clfsload)以瞭解更多資訊。

如果不想使用載入實用程式，或者要將內容添加到現有存儲目標，請[按照通過 Azure HPC 緩存複製資料](#copy-data-through-the-azure-hpc-cache)中的並行資料引入提示。

## <a name="pre-load-data-in-blob-storage-with-clfsload"></a>使用 CLFSLoad 在 Blob 存儲中預載入資料

您可以使用 <!--[Avere CLFSLoad](https://aka.ms/avere-clfsload)--> Avere CLFSLoad 實用程式用於將資料複製到新的 Blob 存儲容器，然後再將其添加為存儲目標。 此實用程式在單個 Linux 系統上運行，以 Azure HPC 緩存所需的專有格式寫入資料。 CLFSLoad 是填充 Blob 存儲容器以用於緩存的最有效方法。

Avere CLFSLoad 實用程式可通過 Azure HPC 緩存團隊的請求提供。 請向您的團隊連絡人諮詢，或打開[支援票證](hpc-cache-support-ticket.md)以請求説明。

此選項僅適用于新的空容器。 在使用 Avere CLFSLoad 之前創建容器。

詳細資訊包含在 Avere CLFSLoad 分佈中，該分佈應 Azure HPC 緩存團隊的請求提供。 <!-- [Avere CLFSLoad readme](https://github.com/microsoft/Avere-CLFSLoad/blob/master/README.md). --><!-- caution literal link -->

流程概述：

1. 使用 Python 版本 3.6 或更高版本準備 Linux 系統（VM 或物理系統）。 為了更好的性能，建議使用 Python 3.7。
1. 在 Linux 系統上安裝 Avere-CLFSLoad 軟體。
1. 從 Linux 命令列執行傳輸。

Avere CLFSLoad 實用程式需要以下資訊：

* 包含 Blob 存儲容器的存儲帳戶 ID
* 空 Blob 存儲容器的名稱
* 允許實用程式寫入容器的共用訪問簽名 （SAS） 權杖
* 資料來源的本地路徑 - 包含要複製的資料的本地目錄，或到具有資料的裝載遠端系統的本地路徑

<!-- The requirements are explained in detail in the [Avere CLFSLoad readme](https://aka.ms/avere-clfsload). -->

## <a name="copy-data-through-the-azure-hpc-cache"></a>通過 Azure HPC 緩存複製資料

如果不想使用 Avere CLFSLoad 實用程式，或者要向現有 Blob 存儲目標添加大量資料，則可以通過緩存複製它。 Azure HPC 緩存旨在同時為多個用戶端提供服務，因此，要通過緩存複製資料，應使用來自多個用戶端的並行寫入。

![顯示多用戶端、多執行緒資料移動的圖表：左上方有一個內部部署硬體儲存體的圖示，其中有多個來自它的箭頭。 這些箭頭指向四部用戶端機器。 從每台用戶端電腦三個箭頭指向 Azure HPC 緩存。 從 Azure HPC 緩存中，多個箭頭指向 Blob 存儲。](media/hpc-cache-parallel-ingest.png)

通常用於``cp``將資料``copy``從一個存儲系統傳輸到另一個存儲系統的 或 命令是單線程進程，一次只複製一個檔。 這意味著檔案伺服器一次只引入一個檔，這是對緩存資源的浪費。

本節介紹創建多用戶端多執行緒檔案複製系統以使用 Azure HPC 緩存將資料移動到 Blob 存儲的策略。 它說明檔案傳輸概念和決策點，這些可用來以多個用戶端和簡單的複製命令進行有效率的資料複製。

此外，也說明一些能夠提供幫助的公用程式。 ``msrsync`` 公用程式可用來把將資料集分割成貯體及使用 rsync 命令的處理序部分自動化。 ``parallelcp`` 指令碼是另一個公用程式，可自動讀取來源目錄並發出複製命令。

### <a name="strategic-planning"></a>策略規劃

建置策略來平行複製資料時，您應該了解檔案大小、檔案計數及目錄深度方面的權衡取捨。

* 當檔案較小時，攸關的計量是每秒檔案數。
* 當檔案較大 (10 MiBi 或更大) 時，攸關的計量是每秒位元組數。

每個複製處理序都有輸送量速率和檔案傳輸速率，藉由對複製命令的長度進行計時及分解檔案大小和檔案計數，即可測量這些速率。 本文件未涵蓋這些速率的測量方式說明，但請務必了解您將處理的是大型檔案還是小型檔案。

使用 Azure HPC 緩存並行資料引入的策略包括：

* 手動複製 - 您可以根據預定義的檔或路徑集在後臺同時運行多個複製命令，從而在用戶端上手動創建多執行緒副本。 閱讀[Azure HPC 緩存資料引入 - 手動複製方法](hpc-cache-ingest-manual.md)瞭解詳細資訊。

* 部分自動``msrsync`` - ``msrsync``複製是運行多個並行``rsync``進程的包裝實用程式。 有關詳細資訊，請閱讀[Azure HPC 緩存資料引入 - msrsync 方法](hpc-cache-ingest-msrsync.md)。

* 腳本複製與``parallelcp``- 瞭解如何在[Azure HPC 緩存資料引入 - 並行複製腳本方法](hpc-cache-ingest-parallelcp.md)中創建和運行並行複製腳本。

## <a name="next-steps"></a>後續步驟

設置存儲後，瞭解用戶端如何裝載緩存。

* [訪問 Azure HPC 緩存系統](hpc-cache-mount.md)
