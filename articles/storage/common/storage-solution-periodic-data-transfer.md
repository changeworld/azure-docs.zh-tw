---
title: 選擇適合定期資料轉送的 Azure 解決方案 | Microsoft Docs
description: 了解當您會定期轉送資料時，如何選擇資料轉送的 Azure 解決方案。
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: 09498587ad65a8de0935a3a4455bb3b66ed6c3ec
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92491252"
---
# <a name="solutions-for-periodic-data-transfer"></a>適用於定期資料轉送的解決方案
 
本文提供資料轉送解決方案的概觀，適用於當您會定期轉送資料的情況。 透過網路的定期資料轉送可歸類為定期或持續資料移動。 本文也描述建議的資料轉送選項和適用於此情節之各主要功能的矩陣。

若要了解所有可用資料轉送選項的概觀，請移至[選擇 Azure 資料轉送解決方案](storage-choose-data-transfer-solution.md)。

## <a name="recommended-options"></a>建議的選項

定期資料轉送的建議選項會依轉送是定期或持續的，而分為兩個類別。

- **指令碼編寫/程式設計工具**：適用於在固定間隔發生的資料轉送，使用 AzCopy 和 Azure 儲存體 REST API 等指令碼編寫與程式設計工具。 這些工具適合 IT 專業人員和開發人員使用。

    - **AzCopy**：使用此命令列工具可獲得最佳效能，輕鬆將資料複製到 Azure Blobs、檔案和表格儲存體，或從其中複製資料。 AzCopy 支援並行和平行處理原則，並且能夠繼續中斷的複製作業。
    - **Azure 儲存體 REST API/SDK**：當您在建置應用程式時，您可以針對 Azure 儲存體 REST API 來開發應用程式，並使用以多種語言提供的 Azure SDK。 REST API 也可以利用 Azure 儲存體資料移動程式庫，這特別適合將資料複製到 Azure 或從其中複製資料的高效能複製。

- **持續資料擷取工具**：針對持續進行的資料擷取，您可以選取其中一個資料箱線上轉送裝置或 Azure Data Factory。 這些工具由 IT 專業人員設定，並能以透明地自動進行資料轉送。

    - **Azure Data Factory** - Data Factory 應該用於擴充轉送作業，如果需要協調流程和監視功能也可以使用它。 使用 Azure Data Factory 來設定雲端管線，以在數個 Azure 服務、內部部署之間或這兩者的組合之間，定期轉送檔案。 Azure Data Factory 可讓您協調資料驅動的工作流程，從不同資料存放區內嵌資料，並自動進行資料移動和資料轉換。
    - **適用於線上轉送的 Azure 資料箱系列**：Data Box Edge 和 Azure 資料箱閘道是能將資料移入及移出 Azure 的線上網路裝置。 Data Box Edge 使用人工智慧 (AI) 支援的 Edge 計算，以預先處理資料再上傳。 「資料箱閘道」是該裝置的虛擬版本，並具備相同的資料傳輸功能。


## <a name="comparison-of-key-capabilities"></a>主要功能的比較

下表摘要列出主要功能的差異。

### <a name="scriptedprogrammatic-network-data-transfer"></a>指令碼編寫/程式設計網路資料轉送

| 功能                  | AzCopy                                 | Azure 儲存體 REST API       |
|-----------------------------|----------------------------------------|-------------------------------|
| 板型規格                 | 來自 Microsoft 的命令列工具       | 客戶對儲存體開發 <br> 使用 Azure 用戶端程式庫的 REST API |
| 初始的單次設定     | 最小                                | 中度、變動的開發投入量    |
| 資料格式                 | Azure Blob、Azure 檔案、Azure 資料表 | Azure Blob、Azure 檔案、Azure 資料表   |
| 效能                 | 已最佳化                      | 開發時最佳化                  |
| 定價                     | 適用免費資料輸出費用      | 適用免費資料輸出費用        |

### <a name="continuous-data-ingestion-over-network"></a>透過網路持續擷取資料

| 功能                                       | 資料箱閘道 | 資料箱邊緣   | Azure Data Factory        |
|----------------------------------|-----------------------------------------|--------------------------|---------------------------|
| 板型規格                                   | 虛擬裝置             | 實體裝置          | Azure 入口網站中的服務、內部部署代理程式                                                            |
| 硬體                                      | 您的 Hypervisor            | 由 Microsoft 提供    | NA                                                            |
| 初始設定投入量                          | 低 (<30 分鐘)。            | 中 (~數小時) | 大 (~數天)                                                 |
| 資料格式                                   | Azure Blob、Azure 檔案   | Azure Blob、Azure 檔案 | [支援 70 種以上的資料存放區和格式化資料連接器](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)|
| 資料預先處理                           | 否                         | 是，透過 Edge 計算    | 是                                                           |
| 本機快取<br>(儲存內部部署資料)    | 是                        | 是                      | 否                                                            |
| 從其他雲端轉送                    | 否                         | 否                       | 是                                                           |
| 定價                                       | [定價](https://azure.microsoft.com/pricing/details/storage/databox/gateway/)                    | [定價](https://azure.microsoft.com/pricing/details/storage/databox/edge/)                  | [定價](https://azure.microsoft.com/pricing/details/data-factory/)                                                       |

## <a name="next-steps"></a>後續步驟

- [使用 AzCopy 傳輸資料](/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)。
- [使用 Azure 儲存體 REST API 轉送資料的詳細資訊](/dotnet/api/overview/azure/storage)。
- 了解如何：
    - [使用資料箱閘道傳輸資料](/azure/databox-online/data-box-gateway-deploy-add-shares)。
    - [先使用 Azure Data Box Edge 轉換資料再傳送至 Azure](/azure/databox-online/data-box-edge-deploy-configure-compute)。
- [了解如何使用 Azure Data Factory 轉送資料](/azure/data-factory/tutorial-bulk-copy-portal)。
