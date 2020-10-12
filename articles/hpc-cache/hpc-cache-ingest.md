---
title: 將資料移至 Azure HPC Cache 雲端容器
description: 如何填入 Azure Blob 儲存體以搭配 Azure HPC Cache 使用
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 10/30/2019
ms.author: v-erkel
ms.openlocfilehash: 0da8a4fc1b59976c50cd96f2155715a4cb178cc9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87072775"
---
# <a name="move-data-to-azure-blob-storage"></a>將資料移至 Azure Blob 儲存體

如果您的工作流程包含將資料移至 Azure Blob 儲存體，請確定您使用的是有效率的策略。 您可以預先載入新 Blob 容器中的資料，然後將其定義為儲存體目標，或新增容器，然後使用 Azure HPC Cache 複製您的資料。

本文說明將資料移至 Blob 儲存體以搭配 Azure HPC Cache 使用的最佳方式。

請記住下列事實：

* Azure HPC Cache 使用特製化的儲存格式來組織 Blob 儲存體中的資料。 這就是為什麼 Blob 儲存體目標必須是新的、空白的容器，或是先前用於 Azure HPC Cache 資料的 Blob 容器。

* 當您使用多個用戶端和平行作業時，透過 Azure HPC Cache 將資料複製到後端儲存體目標會更有效率。 一個用戶端的簡易複製命令會以緩慢的速度移動資料。

以 Python 為基礎的公用程式可將內容載入 Blob 儲存體容器。 若要深入瞭解，請閱讀 [Blob 儲存體中的預先載入資料](#pre-load-data-in-blob-storage-with-clfsload) 。

如果您不想要使用載入公用程式，或想要將內容新增至現有的儲存目標，請遵循 [透過 Azure HPC Cache 複製資料](#copy-data-through-the-azure-hpc-cache)中的平行資料內嵌秘訣。

## <a name="pre-load-data-in-blob-storage-with-clfsload"></a>使用 CLFSLoad 預先載入 Blob 儲存體中的資料

您可以使用 Avere CLFSLoad 公用程式，將資料複製到新的 Blob 儲存體容器，然後再將它新增為儲存體目標。 此公用程式會在單一 Linux 系統上執行，並以 Azure HPC Cache 所需的專屬格式來寫入資料。 CLFSLoad 是填入 Blob 儲存體容器以與快取搭配使用的最有效方式。

Avere CLFSLoad 公用程式可透過 Azure HPC Cache 團隊的要求取得。 要求您的小組連絡人，或開啟 [支援票證](hpc-cache-support-ticket.md) 以要求協助。

此選項僅適用于新的空白容器。 請先建立容器，再使用 Avere CLFSLoad。

詳細資訊包含在 Avere CLFSLoad 散發套件中，Azure HPC Cache 團隊的要求中提供。

流程的一般總覽：

1. 使用 Python 3.6 版或更新版本來準備 Linux 系統 (VM 或實體) 。 建議使用 Python 3.7 來提高效能。
1. 在 Linux 系統上安裝 Avere-CLFSLoad 軟體。
1. 從 Linux 命令列執行轉移。

Avere CLFSLoad 公用程式需要下列資訊：

* 包含您 Blob 儲存體容器的儲存體帳戶識別碼
* 空白 Blob 儲存體容器的名稱
* 共用存取簽章 (SAS) 權杖，可讓公用程式寫入容器
* 指向資料來源的本機路徑-本機目錄（其中包含要複製的資料），或包含資料的已掛接遠端系統本機路徑

## <a name="copy-data-through-the-azure-hpc-cache"></a>透過 Azure HPC Cache 複製資料

如果您不想要使用 Avere CLFSLoad 公用程式，或如果您想要將大量資料新增至現有的 Blob 儲存體目標，您可以透過快取進行複製。 Azure HPC Cache 是設計來同時提供多個用戶端，因此若要透過快取複製資料，您應該使用來自多個用戶端的並行寫入。

![顯示多用戶端、多執行緒資料移動的圖表：左上方有一個內部部署硬體儲存體的圖示，其中有多個來自它的箭頭。 這些箭頭指向四部用戶端機器。 從每一部用戶端電腦的三個箭號指向 Azure HPC Cache。 從 Azure HPC Cache 中，有多個箭號指向 Blob 儲存體。](media/hpc-cache-parallel-ingest.png)

``cp`` ``copy`` 您通常用來將資料從某個儲存系統傳送到另一個儲存體系統的或命令，是一次只複製一個檔案的單一執行緒進程。 這表示檔案伺服器一次只會擷取一個檔案，這會浪費快取的資源。

本節說明用來建立多用戶端、多執行緒檔案複製系統以將資料移至具有 Azure HPC Cache 之 Blob 儲存體的策略。 它說明檔案傳輸概念和決策點，這些可用來以多個用戶端和簡單的複製命令進行有效率的資料複製。

此外，也說明一些能夠提供幫助的公用程式。 ``msrsync`` 公用程式可用來把將資料集分割成貯體及使用 rsync 命令的處理序部分自動化。 ``parallelcp`` 指令碼是另一個公用程式，可自動讀取來源目錄並發出複製命令。

### <a name="strategic-planning"></a>策略規劃

建置策略來平行複製資料時，您應該了解檔案大小、檔案計數及目錄深度方面的權衡取捨。

* 當檔案較小時，攸關的計量是每秒檔案數。
* 當檔案較大 (10 MiBi 或更大) 時，攸關的計量是每秒位元組數。

每個複製處理序都有輸送量速率和檔案傳輸速率，藉由對複製命令的長度進行計時及分解檔案大小和檔案計數，即可測量這些速率。 本文件未涵蓋這些速率的測量方式說明，但請務必了解您將處理的是大型檔案還是小型檔案。

使用 Azure HPC Cache 進行平行資料內嵌的策略包括：

* 手動複製-您可以在背景執行一個以上的複製命令，對預先定義的一組檔案或路徑執行一次以上的複製命令，以手動方式在用戶端上建立多執行緒複製。 如需詳細資料，請參閱 [Azure HPC Cache 資料內嵌-手動複製方法](hpc-cache-ingest-manual.md) 。

* 部分自動化複製 ``msrsync``  -  ``msrsync`` 是一個執行多個平行處理常式的包裝函式 ``rsync`` 。 如需詳細資訊，請參閱 [Azure HPC Cache 資料內嵌 msrsync 方法](hpc-cache-ingest-msrsync.md)。

* 編寫腳本複製方式 ``parallelcp`` -瞭解如何在 [Azure HPC Cache 資料內嵌-平行複製腳本方法](hpc-cache-ingest-parallelcp.md)中建立和執行平行複製腳本。

## <a name="next-steps"></a>接下來的步驟

設定儲存體之後，請瞭解用戶端可以如何掛接快取。

* [存取 Azure HPC Cache 系統](hpc-cache-mount.md)
