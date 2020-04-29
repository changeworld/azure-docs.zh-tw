---
title: 將資料從 data lake 和資料倉儲遷移至 Azure
description: 使用 Azure Data Factory 將資料從您的 data lake 和資料倉儲遷移至 Azure。
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 7/30/2019
ms.openlocfilehash: 4408546c892299e5bbbc22b00a4b334c36eda616
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81416438"
---
# <a name="use-azure-data-factory-to-migrate-data-from-your-data-lake-or-data-warehouse-to-azure"></a>使用 Azure Data Factory 將資料從您的 data lake 或資料倉儲遷移至 Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

如果您想要將您的 data lake 或企業資料倉儲（EDW）遷移至 Microsoft Azure，請考慮使用 Azure Data Factory。 Azure Data Factory 適用于下列案例：

- 從 Amazon Simple Storage Service （Amazon S3）或內部部署 Hadoop 分散式檔案系統（HDFS）遷移至 Azure 的海量資料工作負載
- 從 Oracle Exadata、Netezza、Teradata 或 Amazon Redshift EDW 至 Azure 的遷移

Azure Data Factory 可以移動用於 data lake 遷移的 pb （PB）資料，以及數十 tb 的資料來進行資料倉儲遷移。

## <a name="why-azure-data-factory-can-be-used-for-data-migration"></a>為何 Azure Data Factory 可用於資料移轉

- Azure Data Factory 可以輕鬆地相應增加處理能力，以無伺服器的方式來移動資料，使其具有高效能、彈性和擴充性。 而且您只需支付使用的部分。 同時應注意下列項目： 
  - Azure Data Factory 對於資料量或檔案數目沒有任何限制。
  - Azure Data Factory 可以完全使用您的網路和儲存體頻寬，達到您環境中最高的資料移動輸送量。
  - Azure Data Factory 使用隨用隨付方法，因此您只需支付實際用來執行資料移轉至 Azure 的時間。  
- Azure Data Factory 可以同時執行一次性的歷程記錄負載和排程的累加式載入。
- Azure Data Factory 使用 Azure integration runtime （IR）在可公開存取的 data lake 和倉儲端點之間移動資料。 它也可以使用自我裝載 IR，在 Azure 虛擬網路（VNet）或防火牆後方移動 data lake 和倉儲端點的資料。
- Azure Data Factory 具有企業級安全性：您可以使用 Windows Installer （MSI）或服務身分識別進行安全的服務對服務整合，或使用 Azure Key Vault 來進行認證管理。
- Azure Data Factory 提供無程式碼的撰寫體驗，以及豐富的內建監視儀表板。  

## <a name="online-vs-offline-data-migration"></a>線上與離線資料移轉

Azure Data Factory 是標準的線上資料移轉工具，可透過網路（網際網路、ER 或 VPN）傳輸資料。 而在進行離線資料移轉時，使用者會實際將資料傳輸裝置從其組織傳送至 Azure 資料中心。  

當您選擇線上和離線遷移方法時，有三個重要的考慮：  

- 要遷移的資料大小
- 網路頻寬
- 遷移視窗

例如，假設您計畫使用 Azure Data Factory 來完成兩周內的資料移轉（您的*遷移視窗*）。 請注意下表中的粉紅色/藍色剪線。 任何指定之資料行的最小粉紅色資料格，會顯示其遷移視窗最接近但不到兩周的資料大小/網路頻寬配對。 （藍色資料格中的任何大小/頻寬配對都有超過兩周的線上遷移視窗。） 

![線上與離線](media/data-migration-guidance-overview/online-offline.png)這份表格可協助您根據資料大小和可用的網路頻寬，透過線上遷移（Azure Data Factory），判斷您是否可以符合預期的遷移視窗。 如果線上遷移視窗超過兩周，您會想要使用離線遷移。

> [!NOTE]
> 藉由使用線上遷移，您可以透過單一工具來完成歷程記錄資料載入和累加式摘要端對端。  透過這種方法，您的資料可以在整個遷移視窗期間，在現有的存放區與新的存放區之間保持同步。 這表示您可以使用重新整理的資料，在新的存放區上重建您的 ETL 邏輯。


## <a name="next-steps"></a>後續步驟

- [從 AWS S3 將資料遷移至 Azure](data-migration-guidance-s3-azure-storage.md)
- [將資料從內部部署 hadoop 叢集遷移至 Azure](data-migration-guidance-hdfs-azure-storage.md)
- [將資料從內部部署 Netezza 伺服器遷移到 Azure](data-migration-guidance-netezza-azure-sqldw.md)
