---
title: 雲存儲，使用 Azure 存儲構建高度安全、耐用、可擴展的應用
description: 瞭解在雲中存儲大型結構化和非結構化移動應用程式資料的服務。
author: codemillmatt
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 682f06936480d9018d759b4df3bed80be1206faf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240982"
---
# <a name="cloud-storage-for-highly-secure-durable-scalable-apps-with-azure-storage"></a>雲存儲，使用 Azure 存儲提供高度安全、耐用、可擴展的應用
[Azure 存儲](https://azure.microsoft.com/services/storage/)是 Microsoft 面向現代應用程式的雲存儲解決方案，它為數據物件提供可大規模擴展的物件存儲、雲的檔案系統服務、用於可靠消息傳送的消息存儲和 NoSQL 存儲。 Azure 儲存體的特色：
- **耐用且高度可用：** 冗余可確保在發生暫態硬體故障時資料是安全的。 您也可以選擇在資料中心或地理區域間複寫資料，以便在發生地方性災難或天然災害時獲得額外保護。 以此方式複寫資料，可在發生未預期的中斷事件時保持高可用性。
- **安全：** 寫入 Azure 存儲的所有資料都由服務加密。 Azure 儲存體在存取您資料的人員控管上，提供更細微的控制。
- **可擴展：** 服務設計為可大規模擴展，以滿足當今應用程式的資料存儲和性能需求。
- **託管：** Azure 可為您處理硬體維護、更新和關鍵問題。
- **可訪問：** 可通過 HTTP 或 HTTPS 從世界任何地方訪問資料。 Microsoft 以多種語言提供用戶端庫，如 .NET、JAVA、Node.js、Python、PHP、Ruby 和 Go，以及成熟的 REST API。 Azure PowerShell 或 Azure CLI 中支援腳本編寫。 Azure 門戶和 Azure 存儲資源管理器提供了用於處理資料的簡便視覺化解決方案。

使用以下服務在移動應用中啟用雲存儲。

## <a name="azure-blob-storage"></a>Azure Blob 儲存體
[Azure Blob 存儲](https://azure.microsoft.com/services/storage/blobs/)為雲提供了物件存儲解決方案。 Blob 存儲經過優化，用於存儲大量不符合特定資料模型或定義（如文本或二進位）的非結構化資料。 它支援用戶端庫使用的各種語言。 Blob 存儲旨在：
- 將圖像或文檔直接送達瀏覽器。
- 存儲檔以進行分散式訪問。
- 資料流視頻和音訊。
- 寫入日誌檔。
- 存儲用於備份和還原、災害復原和存檔的資料。
- 存儲資料，以便按本地或 Azure 託管的服務進行分析。

**引用**
- [Azure 門戶](https://portal.azure.com)
- [Azure Blob 儲存體文件](/azure/storage/blobs/storage-blobs-introduction)
- [快速入門](/azure/storage/blobs/storage-quickstart-blobs-portal)
- [樣品](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="azure-table-storage"></a>Azure 資料表儲存體
[Azure 表存儲](https://azure.microsoft.com/services/storage/tables/)是一種服務，用於在雲中存儲結構化的 NoSQL 資料，並提供具有無架構設計的金鑰或屬性存儲。 Azure 資料表儲存體可儲存大量的結構化資料。 該服務是 NoSQL 資料存儲，它接受 Azure 雲內外的經過身份驗證的調用。 Azure 表是存儲結構化非關係資料的理想選擇。 表存儲通常用於：
- 存儲能夠為 Web 規模應用程式提供服務的 TB 結構化資料。
- 存儲不需要複雜聯接、外鍵或預存程序的資料集，並且可以非正常化以快速訪問。
- 使用群集索引快速查詢資料。
- 使用 OData 協定和 LINQ 查詢訪問資料，這些查詢與 Windows 通信基礎 （WCF） 資料服務 .NET 庫一起。

您可以使用表存儲來存儲和查詢大量結構化的非關係資料。 您的表會隨著需求增加而縮放。

**引用**
- [Azure 門戶](https://portal.azure.com)
- [Azure 資料表儲存體文件](/azure/storage/tables/table-storage-overview)
- [樣品](/azure/cosmos-db/tutorial-develop-table-dotnet?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fstorage%2Ftables%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [快速入門](/azure/storage/tables/table-storage-quickstart-portal)

## <a name="azure-files"></a>Azure 檔案
使用[Azure 檔](https://azure.microsoft.com/services/storage/files/)，可以設置可以使用標準伺服器訊息區 （SMB） 協定訪問的高可用性網路檔共用。 多個 VM 可以同時共用具有讀取和寫入存取權限的相同檔。 您還可以使用 REST 介面或存儲用戶端庫讀取檔。 您可以使用指向該檔的 URL 並包含共用訪問簽名 （SAS） 權杖，從世界任何地方訪問檔。 您可以生成 SAS 權杖。 它們允許在特定時間內特定地訪問私有資產。

Azure 檔案共用可以用來：
- **替換或補充本地檔案伺服器：** 流行的作業系統（如 Windows、macOS 和 Linux）可以直接將 Azure 檔共用裝載到世界上任何位置。 透過 Azure 檔案同步，也可以將 Azure 檔案共用複寫到 Windows Server (在內部部署環境或雲端)，從而在資料的使用位置進行高效能和分散式快取。
- **提升和換檔應用：** 將應用程式遷移到希望檔共用以存儲檔應用程式或使用者資料的雲。
- **簡化雲開發：** Azure 檔還可通過多種方式用於簡化新的雲開發專案。 例如：
    - **共用應用程式設定：** 分散式應用程式的一個常見模式是，在集中位置將設定檔從許多應用程式實例訪問它們。 應用程式實例可以通過檔 REST API 載入其配置。 使用者可以根據需要通過在本地安裝 SMB 共用來訪問它們。
    - **診斷共用：** Azure 檔共用是雲應用程式編寫日誌、指標和崩潰轉儲的方便場所。 日誌可以由應用程式實例通過檔 REST API 寫入。 開發人員可以通過在其本地電腦上安裝檔共用來訪問它們。 此功能可實現極大的靈活性。 開發人員可以採用雲開發，而無需放棄他們所知道的現有工具。

**引用**
- [Azure 門戶](https://portal.azure.com)
- [Azure 檔案文件](/azure/storage/files/storage-files-introduction)
- [快速入門](/azure/storage/files/storage-files-quick-create-use-windows)

## <a name="azure-queue-storage"></a>Azure 佇列儲存體
[Azure 佇列存儲](https://azure.microsoft.com/services/storage/queues/)是存儲大量消息的服務。 您可以使用 HTTP 或 HTTPS 通過經過身份驗證的調用從世界任何地方訪問消息。 一則佇列訊息的大小可能高達 64 KB。 佇列可能包含數百萬條消息，最高為存儲帳戶的總容量限制。 佇列通常用來建立要以非同步方式處理的待處理項目 (backlog)。

**引用**
- [Azure 門戶](https://portal.azure.com)
- [Azure 佇列儲存體文件](/azure/storage/queues/)
- [快速入門](/azure/storage/queues/storage-quickstart-queues-portal)
- [樣品](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
