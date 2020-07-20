---
title: 大型資料集的 Azure 資料轉送選項 (低速或無網路頻寬) | Microsoft Docs
description: 了解當您的環境中具備有限或無網路頻寬，且您正打算要轉送大型資料集時，應如何選擇資料轉送的 Azure 解決方案。
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: 6514b149b057d690d37551bd0489c63852addb1e
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86207503"
---
# <a name="data-transfer-for-large-datasets-with-low-or-no-network-bandwidth"></a>在低速或無網路頻寬之情況下進行大型資料集的資料轉送
 
本文提供資料轉送解決方案的概觀，適用於當您的環境中有有限或沒有網路頻寬，且您正打算要轉送大型資料集的情況。 本文也描述建議的資料轉送選項和適用於此情節之各主要功能的矩陣。

若要了解所有可用資料轉送選項的概觀，請移至[選擇 Azure 資料轉送解決方案](storage-choose-data-transfer-solution.md)。

## <a name="offline-transfer-or-network-transfer"></a>離線轉送或網路轉送

大型資料集代表您有數 TB 到 數 PB 的資料。 您沒有網路頻寬、您的網路速度很慢，或網路不穩定。 此外：

- 您受限於網際網路服務提供者 (ISP) 的網路轉送成本。
- 安全性或組織原則不允許在處理敏感性資料時建立輸出連線。

在所有上述的情況中，都可以使用實體裝置來進行一次性的大量資料轉送。 選擇 Microsoft 提供的資料箱磁碟、資料箱、Data Box Heavy 裝置，或使用自己的磁碟匯入/匯出。

若要確認實體裝置是否是適合的選項，請使用下列表格。 它顯示針對各種可用的頻寬 (假設利用率 90%) 進行網路資料轉送的預估時間。 如果網路轉送的預估速度太慢，您就應該使用實體裝置。  

![網路轉送或離線轉送](media/storage-solution-large-dataset-low-network/storage-network-or-offline-transfer.png)

## <a name="recommended-options"></a>建議的選項

在此案例中可用的選項是適用於 Azure 資料箱離線轉送或 Azure 匯入/匯出的裝置。

- **適用於離線轉送的 Azure 資料箱系列**：當您受到時間、網路可用性或成本的限制時，使用 Microsoft 提供的資料箱裝置以將大量資料移動到 Azure。 使用如 Robocopy 等工具複製內部部署資料。 根據要轉送之資料的大小，您可以選擇資料箱磁碟、資料箱或 Data Box Heavy。
- **Azure 匯入/匯出**：使用 Azure 匯入/匯出服務，透過寄送您自己的磁碟機，安全地將大量資料匯入至 Azure Blob 儲存體和 Azure 檔案中。 這項服務也能用來將資料從 Azure Blob 儲存體傳輸到磁碟機，然後運送到您的內部部署網站。

## <a name="comparison-of-key-capabilities"></a>主要功能的比較

下表摘要列出主要功能的差異。

|                                     |    資料箱磁碟      |    資料箱                                      |    Data Box Heavy              |    匯入/匯出                       |
|-------------------------------------|---------------------------------|--------------------------------------------------|------------------------------------------|----------------------------------------|
|    **資料大小**                    |    最多 35 TB                 |    每個裝置最多 80 TB                       |    每個裝置最多 800 TB               |    變數                            |
|    **Data type**                    |    Azure Blob                  |    Azure Blob<br>Azure 檔案儲存體                    |    Azure Blob<br>Azure 檔案儲存體            |    Azure Blob<br>Azure 檔案儲存體          |
|    **板型規格**                  |    每筆訂單 5 個 SSD             |    每筆訂單 1 X 50 磅 桌上型電腦大小裝置    |    每筆訂單 1 X ~500 磅 大型裝置    |    每筆訂單最多 10 個 HDD/SSD        |
|    **初始設定時間**           |    低度 <br>(15 分鐘)            |    低至中度 <br> (<30 分鐘)               |    中度<br>(1-2 小時)               |    中度到困難<br>(變動) |
|    **將資料傳送到 Azure**           |    是                          |    是                                           |    是                                   |    是                                 |
|    **從 Azure 匯出資料**       |    否                           |    否                                            |    否                                    |    是                                 |
|    **加密**                   |    AES 128 位元                  |    AES 256 位元                                   |    AES 256 位元                           |    AES 128 位元                         |
|    **硬體**                     |     Microsoft 提供          |    Microsoft 提供                            |    Microsoft 提供                    |    客戶提供                   |
|    **網路介面**            |    USB 3.1/SATA                 |    RJ 45、SFP+                                   |    RJ45、QSFP+                           |    SATA II/SATA III                    |
|    **夥伴整合**          |    部分                         |    [高](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureExpressPod)                                          |    [高](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureExpressPod)                                  |    部分                                |
|    **運送中**                     |    Microsoft 管理            |    Microsoft 管理                             |    Microsoft 管理                     |    由客戶管理                    |
| **當資料移動時的使用**     |在商務服務界限內|在商務服務界限內|在商務服務界限內|跨越地理界限，例如美國到歐洲|
|    **定價**                      |    [定價](https://azure.microsoft.com/pricing/details/databox/disk/)                    |   [定價](https://azure.microsoft.com/pricing/details/storage/databox/)                                      |  [定價](https://azure.microsoft.com/pricing/details/storage/databox/heavy/)                               |   [定價](https://azure.microsoft.com/pricing/details/storage-import-export/)                            |


## <a name="next-steps"></a>後續步驟

- 了解如何

    - [使用資料箱磁碟轉送資料](https://docs.microsoft.com/azure/databox/data-box-disk-quickstart-portal)。
    - [使用資料箱轉送資料](https://docs.microsoft.com/azure/databox/data-box-quickstart-portal)。
    - [使用匯入/匯出轉送資料](/azure/storage/common/storage-import-export-data-to-blobs)。
