---
title: 大型資料集的 Azure 資料轉送選項 (中速至高速網路頻寬) | Microsoft Docs
description: 了解當您的環境中有中速至高速的網路頻寬，且您正打算要轉送大型資料集時，應如何選擇資料轉送的 Azure 解決方案。
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: 962bb7a4484f28d52ffd4f0cae985140ec2f0d28
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006025"
---
# <a name="data-transfer-for-large-datasets-with-moderate-to-high-network-bandwidth"></a>在有中速至高速的網路頻寬之情況下進行大型資料集的資料轉送
 
本文提供資料轉送解決方案的概觀，適用於當您的環境中有中速至高速的網路頻寬，且您正打算要轉送大型資料集的情況。 本文也描述建議的資料轉送選項和適用於此情節之各主要功能的矩陣。

若要了解所有可用資料轉送選項的概觀，請移至[選擇 Azure 資料轉送解決方案](storage-choose-data-transfer-solution.md)。

## <a name="scenario-description"></a>案例描述

大型資料集是指資料大小等級為 TB 至 PB。 中速至高速的網路頻寬是指 100 Mbps 至 10 Gbps。

## <a name="recommended-options"></a>建議的選項

在此情節中，建議的選項是根據您的網路頻寬是中速或是高速來區分。

### <a name="moderate-network-bandwidth-100-mbps---1-gbps"></a>中速網路頻寬 (100 Mbps-1 Gbps)

若使用的是中速網路頻寬，您需要預估資料透過網路轉送的時間。

使用下表來估計時間，並根據結果選擇離線轉送或透過網路轉送。 該表格顯示針對各種可用的頻寬 (假設利用率 90%) 進行網路資料轉送的預估時間。  

![網路轉送或離線轉送](media/storage-solution-large-dataset-low-network/storage-network-or-offline-transfer.png)

- 如果網路轉送的預估速度太慢，您就應該使用實體裝置。 此案例中建議的選項是來自 Azure 資料箱系列的離線轉送裝置，或是使用您自己磁碟機的 Azure 匯入/匯出。

    - **適用於離線轉送的 Azure 資料箱系列**：當您受到時間、網路可用性或成本的限制時，使用 Microsoft 提供的資料箱裝置以將大量資料移動到 Azure。 使用如 Robocopy 等工具複製內部部署資料。 根據要轉送之資料的大小，您可以選擇資料箱磁碟、資料箱或 Data Box Heavy。
    - **Azure 匯入/匯出**：使用 Azure 匯入/匯出服務，透過寄送您自己的磁碟機，安全地將大量資料匯入至 Azure Blob 儲存體和 Azure 檔案中。 這項服務也能用來將資料從 Azure Blob 儲存體傳輸到磁碟機，然後運送到您的內部部署網站。

- 如果預估的網路傳轉送速度合理，則您可以使用[高速網路頻寬](#high-network-bandwidth)中所述的下列任何工具。


### <a name="high-network-bandwidth-1-gbps---100-gbps"></a>高速網路頻寬 (1 Gbps - 100 Gbps)

如果可用網路頻寬是高速的，請使用下列其中一個工具。

- **AzCopy**：使用此命令列工具可獲得最佳效能，輕鬆將資料複製到 Azure Blobs、檔案和表格儲存體，或從其中複製資料。 AzCopy 支援並行和平行處理原則，並且能夠繼續中斷的複製作業。
- **Azure 儲存體 REST API/SDK**：當您在建置應用程式時，您可以針對 Azure 儲存體 REST API 來開發應用程式，並使用以多種語言提供的 Azure SDK。
- **適用于線上傳輸的 Azure 資料箱系列** – Data box Edge 和 Data box Gateway 是可將資料移入和移出 Azure 的線上網路裝置。 當在上傳資料之前，同時需要持續擷取資料及預先處理資料時，使用 Data Box Edge 實體裝置。 「資料箱閘道」是該裝置的虛擬版本，並具備相同的資料傳輸功能。 在每個案例中，資料轉送都是由裝置管理。
- **Azure Data Factory** - Data Factory 應該用於擴充轉送作業，如果需要協調流程和監視功能也可以使用它。 使用 Azure Data Factory 以在數個 Azure 服務、內部部署之間或這兩者的組合之間，定期轉送檔案。 您可以使用 Data Factory 來建立資料驅動的工作流程 (管線) 並進行排程，工作流程會從不同的資料存放區內嵌資料，並自動進行資料移動和資料轉送。

## <a name="comparison-of-key-capabilities"></a>主要功能的比較

下表摘要列出建議選項的主要功能差異。

### <a name="moderate-network-bandwidth"></a>中速網路頻寬

如果使用離線資料轉送，請使用下表來了解主要功能差異。

|                                     |    資料箱磁碟      |    資料箱                                      |    Data Box Heavy            |    匯入/匯出                       |
|-------------------------------------|---------------------------------|--------------------------------------------------|------------------------------------------|----------------------------------------|
|    **資料大小**                    |    最多 35 TB                 |    每個裝置最多 80 TB                       |    每個裝置最多 800 TB               |    變數                            |
|    **Data type**                    |    Azure Blob                  |    Azure Blob<br>Azure 檔案                    |    Azure Blob<br>Azure 檔案            |    Azure Blob<br>Azure 檔案          |
|    **板型規格**                  |    每筆訂單 5 個 SSD             |    每筆訂單 1 X 50 磅 桌上型電腦大小裝置    |    每筆訂單 1 X ~500 磅 大型裝置    |    每筆訂單最多 10 個 HDD/SSD        |
|    **初始設定時間**               |    低 <br>(15 分鐘)            |    低至中度 <br> (<30 分鐘)               |    中<br>(1-2 小時)               |    中度到困難<br>(變動) |
|    **將資料傳送到 Azure**           |    是                          |    是                                           |    是                                   |    是                                 |
|    **從 Azure 匯出資料**           |    否                           |    否                                            |    否                                    |    是                                 |
|    **加密**                   |    AES 128 位元                  |    AES 256 位元                                   |    AES 256 位元                           |    AES 128 位元                         |
|    **硬體**                     |     Microsoft 提供          |    Microsoft 提供                            |    Microsoft 提供                    |    客戶提供                   |
|    **網路介面**            |    USB 3.1/SATA                 |    RJ 45、SFP+                                   |    RJ45、QSFP+                           |    SATA II/SATA III                    |
|    **夥伴整合**          |    部分                         |    [高](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureExpressPod)                                          |    [高](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureExpressPod)                                  |    部分                                |
|    **運送中**                     |    Microsoft 管理            |    Microsoft 管理                             |    Microsoft 管理                     |    由客戶管理                    |
| **當資料移動時的使用**     |在商務服務界限內|在商務服務界限內|在商務服務界限內|跨越地理界限，例如美國到歐洲|
|    **定價**                          |    [定價](https://azure.microsoft.com/pricing/details/databox/disk/)                    |   [定價](https://azure.microsoft.com/pricing/details/storage/databox/)                                      |  [定價](https://azure.microsoft.com/pricing/details/storage/databox/heavy/)                               |   [定價](https://azure.microsoft.com/pricing/details/storage-import-export/)                            |


如果使用線上資料轉送，請使用下一節中適用於高速網路頻寬的表格。

### <a name="high-network-bandwidth"></a>高速網路頻寬

|                                     |    工具 AzCopy、 <br>Azure PowerShell、 <br>Azure CLI             |    Azure 儲存體 REST API、SDK                   |    資料箱閘道或 Data Box Edge          |    Azure Data Factory                                            |
|-------------------------------------|------------------------------------|----------------------------------------------|----------------------------------|-----------------------------------------------------------------------|
|    **Data type**              |    Azure Blob、Azure 檔案、Azure 資料表    |    Azure Blob、Azure 檔案、Azure 資料表    |    Azure Blob、Azure 檔案                           |   支援 70 種以上的資料存放區和格式化資料連接器    |
|    **板型規格**            |    命令列工具                        |    程式設計介面                    |    Microsoft 提供的虛擬 <br>或實體裝置     |    Azure 入口網站中的服務                                            |
|    **初始的單次設定** |    簡單               |    中                       |    簡單 (<30 分鐘) 至中度 (1-2 小時)            |    大規模                                                          |
|    **資料前置處理**          |    否                                        |    否                                        |    是 (使用 Edge 計算)                               |    是                                                                |
|    **從其他雲端轉送**   |    否                                        |    否                                        |    否                                                    |    是                                                                |
|    **使用者類型**                    |    IT 專業人員或開發人員                                       |    Dev                                       |    IT 專業人員                                                |    IT 專業人員                                                             |
|    **定價**                      |    適用免費資料輸出費用         |    適用免費資料輸出費用         |    [定價](https://azure.microsoft.com/pricing/details/storage/databox/edge/)                                               |    [定價](https://azure.microsoft.com/pricing/details/data-factory/)                                                            |

## <a name="next-steps"></a>後續步驟

- [了解如何使用匯入/匯出轉送資料](./storage-import-export-data-to-blobs.md)。
- 了解如何

    - [使用資料箱磁碟傳輸資料](../../databox/data-box-disk-quickstart-portal.md)。
    - [使用資料箱傳輸資料](../../databox/data-box-quickstart-portal.md)。
- [使用 AzCopy 傳輸資料](./storage-use-azcopy-v10.md)。
- 了解如何：
    - [使用資料箱閘道傳輸資料](../../databox-online/data-box-gateway-deploy-add-shares.md)。
    - [在傳送至 Azure 之前，使用 Data Box Edge 來轉換資料](../../databox-online/azure-stack-edge-deploy-configure-compute.md)。
- [了解如何使用 Azure Data Factory 轉送資料](../../data-factory/quickstart-create-data-factory-portal.md)。
- 使用 REST API 轉送資料

    - [在 .NET 中](/dotnet/api/overview/azure/storage)
    - [使用 Java](/java/api/overview/azure/storage)