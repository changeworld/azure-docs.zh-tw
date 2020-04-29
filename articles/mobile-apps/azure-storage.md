---
title: 使用 Azure 儲存體建立高度安全、持久、可擴充應用程式的雲端儲存體
description: 瞭解在雲端中儲存大型結構化和非結構化行動應用程式資料的服務。
author: codemillmatt
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 682f06936480d9018d759b4df3bed80be1206faf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80240982"
---
# <a name="cloud-storage-for-highly-secure-durable-scalable-apps-with-azure-storage"></a>具有 Azure 儲存體的高安全性、耐用、可擴充應用程式的雲端儲存體
[Azure 儲存體](https://azure.microsoft.com/services/storage/)是適用于新式應用程式的 Microsoft 雲端儲存體解決方案，可為數據物件提供可大幅調整的物件存放區、適用于雲端的檔案系統服務、可靠訊息的訊息存放區，以及 NoSQL 存放區。 Azure 儲存體的特色：
- **持久性和高可用性：** 冗余可確保您的資料在發生暫時性硬體失敗時安全。 您也可以選擇在資料中心或地理區域間複寫資料，以便在發生地方性災難或天然災害時獲得額外保護。 以此方式複寫資料，可在發生未預期的中斷事件時保持高可用性。
- **安全：** 寫入 Azure 儲存體的所有資料都會由服務加密。 Azure 儲存體在存取您資料的人員控管上，提供更細微的控制。
- **可調整：** 服務的設計可大幅擴充，以符合現今應用程式的資料儲存和效能需求。
- **受控：** Azure 會為您處理硬體維護、更新和重大問題。
- **可存取：** 資料可透過 HTTP 或 HTTPS 從世界各地存取。 Microsoft 提供各種語言的用戶端程式庫，例如 .NET、JAVA、node.js、Python、PHP、Ruby 和 Go，以及成熟的 REST API。 Azure PowerShell 或 Azure CLI 都支援腳本。 Azure 入口網站和 Azure 儲存體總管提供簡單的視覺解決方案來處理您的資料。

使用下列服務在您的行動應用程式中啟用雲端存放裝置。

## <a name="azure-blob-storage"></a>Azure Blob 儲存體
[Azure Blob 儲存體](https://azure.microsoft.com/services/storage/blobs/)提供適用于雲端的物件儲存體解決方案。 Blob 儲存體已優化，可用於儲存大量非結構化資料，而不會遵守特定資料模型或定義，例如文字或二進位檔。 它支援用戶端程式庫所使用的各種語言。 Blob 儲存體的設計目的是：
- 將影像或檔直接提供給瀏覽器。
- 儲存檔案以供分散式存取。
- 串流影片和音訊。
- 寫入記錄檔。
- 儲存用於備份和還原、嚴重損壞修復和封存的資料。
- 儲存資料供內部部署或 Azure 託管服務進行分析。

**參考**
- [Azure 入口網站](https://portal.azure.com)
- [Azure Blob 儲存體文件](/azure/storage/blobs/storage-blobs-introduction)
- [快速入門](/azure/storage/blobs/storage-quickstart-blobs-portal)
- [範例](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="azure-table-storage"></a>Azure 資料表儲存體
[Azure 表格儲存體](https://azure.microsoft.com/services/storage/tables/)是在雲端儲存結構化 NoSQL 資料的服務，並提供具有無架構設計的索引鍵或屬性存放區。 Azure 資料表儲存體可儲存大量的結構化資料。 此服務是 NoSQL 資料存放區，可接受來自 Azure 雲端內部和外部的已驗證呼叫。 Azure 資料表非常適合用來儲存結構化的非關聯式資料。 資料表儲存體通常用來：
- 儲存數 tb 的結構化資料，能夠提供 web 規模的應用程式。
- 儲存不需要複雜聯結、外鍵或預存程式的資料集，而且可以進行反正規化以進行快速存取。
- 使用叢集索引快速地查詢資料。
- 使用 OData 通訊協定和 LINQ 查詢搭配 Windows Communication Foundation （WCF）資料服務 .NET 程式庫來存取資料。

您可以使用資料表儲存體來儲存和查詢龐大的結構化、非關聯式資料集。 您的資料表會隨著需求增加而調整。

**參考**
- [Azure 入口網站](https://portal.azure.com)
- [Azure 表格儲存體檔](/azure/storage/tables/table-storage-overview)
- [範例](/azure/cosmos-db/tutorial-develop-table-dotnet?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fstorage%2Ftables%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [快速入門](/azure/storage/tables/table-storage-quickstart-portal)

## <a name="azure-files"></a>Azure 檔案
使用[Azure 檔案儲存體](https://azure.microsoft.com/services/storage/files/)，您可以設定高可用性網路檔案共用，以使用標準伺服器訊息區（SMB）通訊協定來存取。 多個 Vm 可以同時與讀取和寫入權限共用相同的檔案。 您也可以使用 REST 介面或儲存體用戶端程式庫來讀取檔案。 您可以使用指向檔案並包含共用存取簽章（SAS）權杖的 URL，從世界各地存取檔案。 您可以產生 SAS 權杖。 它們允許特定的一段時間記憶體取私人資產。

Azure 檔案共用可以用來：
- **取代或補充內部部署檔案伺服器：** 熱門的作業系統（例如 Windows、macOS 和 Linux）可以在世界各地直接掛接 Azure 檔案共用。 透過 Azure 檔案同步，也可以將 Azure 檔案共用複寫到 Windows Server (在內部部署環境或雲端)，從而在資料的使用位置進行高效能和分散式快取。
- 隨即**轉移應用程式：** 將應用程式遷移至預期檔案共用儲存檔案應用程式或使用者資料的雲端。
- **簡化雲端開發：** Azure 檔案儲存體也可以用多種方式來簡化新的雲端開發專案。 例如：
    - **共用的應用程式設定：** 分散式應用程式的常見模式是將設定檔放在可從許多應用程式實例存取的集中位置。 應用程式實例可以透過 REST API 的檔案來載入其設定。 使用者可以視需要在本機掛接 SMB 共用來存取它們。
    - **診斷共用：** Azure 檔案共用是一個方便的位置，可讓雲端應用程式寫入其記錄、計量和損毀傾印。 應用程式實例可以透過 REST API 的檔案來寫入記錄。 開發人員可以藉由在本機電腦上掛接檔案共用來存取這些檔案。 這項功能可提供極大的彈性。 開發人員可以採用雲端開發，而不需要放棄他們知道的現有工具。

**參考**
- [Azure 入口網站](https://portal.azure.com)
- [Azure 檔案文件](/azure/storage/files/storage-files-introduction)
- [快速入門](/azure/storage/files/storage-files-quick-create-use-windows)

## <a name="azure-queue-storage"></a>Azure 佇列儲存體
[Azure 佇列儲存體](https://azure.microsoft.com/services/storage/queues/)是用來儲存大量訊息的服務。 您可以使用 HTTP 或 HTTPS，透過已驗證的呼叫，從世界各地存取訊息。 一則佇列訊息的大小可能高達 64 KB。 佇列可能包含數百萬則訊息，最高可達儲存體帳戶的總容量限制。 佇列通常用來建立要以非同步方式處理的待處理項目 (backlog)。

**參考**
- [Azure 入口網站](https://portal.azure.com)
- [Azure 佇列儲存體文件](/azure/storage/queues/)
- [快速入門](/azure/storage/queues/storage-quickstart-queues-portal)
- [範例](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
