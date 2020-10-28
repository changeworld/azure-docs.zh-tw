---
title: 小型資料集的 Azure 資料轉送選項 (低速至中速網路頻寬) | Microsoft Docs
description: 了解當您的環境中有低速至中速的網路頻寬，且您正打算要轉送小型資料集時，應如何選擇資料轉送的 Azure 解決方案。
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: alkohli
ms.openlocfilehash: f59d1e297ba4d7607d7abd07a78da4784f55d20f
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792900"
---
# <a name="data-transfer-for-small-datasets-with-low-to-moderate-network-bandwidth"></a>使用低速至中速網路頻寬進行小型資料集資料轉送
 
本文提供資料轉送解決方案的概觀，適用於當您的環境中有低速至中速的網路頻寬，且您正打算要轉送小型資料集的情況。 本文也描述建議的資料轉送選項和適用於此情節之各主要功能的矩陣。

若要了解所有可用資料轉送選項的概觀，請移至[選擇 Azure 資料轉送解決方案](storage-choose-data-transfer-solution.md)。

## <a name="scenario-description"></a>案例描述

小型資料集是指資料大小等級為 GB 至幾 TB。 低速至中速網路頻寬表示 45 Mbps (資料中心中的 T3 連線) 至 1Gbps。

- 如果您只要轉送幾個檔案，而且不需要自動化資料轉送，請考慮使用具備圖形化介面的工具。
- 如果您熟悉系統管理，則可考慮使用命令列或程式設計/指令碼工具。

## <a name="recommended-options"></a>建議的選項

此案例中建議的選項為：

- **圖形化介面工具** ：例如Azure 入口網站中的 Azure 儲存體總管和 Azure 儲存體。 這提供簡單的方式以檢視您的資料，並快速地轉送幾個檔案。

    - **Azure 儲存體總管** ：此跨平台工具可讓您管理 Azure 儲存體帳戶的內容。 它能讓您上傳、下載及管理 Blob、檔案、佇列、資料表與 Azure Cosmos DB 實體。 將其用於 Blob 儲存體來管理 Blob 和資料夾，以及在您的本機檔案系統與 Blob 儲存體之間，或在儲存體帳戶彼此之間上傳和下載 Blob。
    - **Azure 入口網站** ：Azure 入口網站中的 Azure 儲存體提供 Web 型介面，可用來瀏覽檔案和上傳新檔案 (一次一個)。 如果您不要安裝任何工具或發出命令來快速瀏覽檔案，或只需上傳少數幾個新檔案，就很適合使用這個選項。

- **指令碼/程式設計工具** ：例如 AzCopy/PowerShell/Azure CLI 和 Azure 儲存體 REST APIs。

    - **AzCopy** ：使用此命令列工具可獲得最佳效能，輕鬆將資料複製到 Azure Blobs、檔案和表格儲存體，或從其中複製資料。 AzCopy 支援並行和平行處理原則，並且能夠繼續中斷的複製作業。
    - **Azure PowerShell** ：針對熟悉系統管理的使用者，在 Azure PowerShell 中使用 Azure 儲存體模組來轉送資料。
    - **Azure CLI** ：使用此跨平台工具來管理 Azure 服務並將資料上傳至 Azure 儲存體。
    - **Azure 儲存體 REST API/SDK** ：當您在建置應用程式時，您可以針對 Azure 儲存體 REST API/SDK 來開發應用程式，並使用以多種語言提供的 Azure 用戶端程式庫。


## <a name="comparison-of-key-capabilities"></a>主要功能的比較

下表摘要列出主要功能的差異。

| 功能 | Azure 儲存體總管 | Azure 入口網站 | AzCopy<br>Azure PowerShell<br>Azure CLI | Azure 儲存體 REST API 或 SDK |
|---------|------------------------|--------------|-----------------------------------------|---------------------------------|
| 可用性 | 下載並安裝 <br>獨立工具 | 在 Azure 入口網站中的 Web 型瀏覽工具 | 命令列工具 |.NET、Java、Python、JavaScript、C++、Go、Ruby 和 PHP 的可程式化介面 |
| 圖形化介面 | 是 | 是 | 否 | 否 |
| 支援的平台 | Windows、Mac、Linux | Web 型 |Windows、Mac、Linux |所有平台 |
| 允許的 Blob 儲存體作業<br>(適用於 Blob 和資料夾) | 上傳<br>下載<br>管理 | 上傳<br>下載<br>管理 |上傳<br>下載<br>管理 | 是，可自訂 |
| 允許的 Data Lake Gen1 儲存體<br>作業 (適用於檔案和資料夾) | 上傳<br>下載<br>管理 | 否 |上傳<br>下載<br>管理                   | 否 |
| 允許的檔案儲存體作業<br>(適用於檔案和目錄) | 上傳<br>下載<br>管理 | 上傳<br>下載<br>管理   |上傳<br>下載<br>管理 | 是，可自訂 |
| 允許的資料表儲存體作業<br>(適用於資料表) |管理 | 否 |AzCopy v7 中的資料表支援 |是，可自訂|
| 允許的佇列儲存體 | 管理 | 否  |否 | 是，可自訂|


## <a name="next-steps"></a>後續步驟

- 了解如何[使用 Azure 儲存體總管轉送資料](../../machine-learning/team-data-science-process/move-data-to-azure-blob-using-azure-storage-explorer.md)。
- [使用 AzCopy 轉送資料](./storage-use-azcopy-v10.md)