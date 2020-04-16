---
title: 將資料從資料湖與資料主目錄移到 Azure
description: 使用 Azure 資料工廠將數據從資料湖和數據倉庫遷移到 Azure。
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416438"
---
# <a name="use-azure-data-factory-to-migrate-data-from-your-data-lake-or-data-warehouse-to-azure"></a>使用 Azure 資料工廠將資料從資料湖或資料倉儲移到 Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

如果要將數據湖或企業數據倉庫 (EDW) 遷移到 Microsoft Azure,請考慮使用 Azure 數據工廠。 Azure 資料工廠非常適合以下方案:

- 大數據工作負載從 Amazon 簡單儲存服務 (Amazon S3) 或本地 Hadoop 分散式檔案系統 (HDFS) 遷移到 Azure
- EDW 從 Oracle Exadata、Netezza、Teradata 或 Amazon 紅移遷移到 Azure

Azure 數據工廠可以移動 PB 的數據以進行數據湖遷移,併為數據倉庫遷移移動數十 TB 的數據。

## <a name="why-azure-data-factory-can-be-used-for-data-migration"></a>為什麼 Azure 資料工廠可用於資料移轉

- Azure 數據工廠可以輕鬆地擴展處理能力,以無伺服器方式移動數據,具有高性能、彈性和可擴充性。 而你只為你使用的東西付費。 同時應注意下列項目： 
  - Azure 資料工廠對資料卷或文件數量沒有限制。
  - Azure 數據工廠可以充分利用網路和儲存頻寬,實現環境中數據移動輸送量最高的。
  - Azure 數據工廠使用即用即付方法,因此只需為實際用於運行數據遷移到 Azure 的時間付費。  
- Azure 數據工廠可以執行一次性歷史載入和計劃增量載入。
- Azure 資料工廠使用 Azure 整合執行時 (IR) 在可公開存取的資料湖和倉庫終結點之間行動資料。 它還可以使用自託管 IR 在 Azure 虛擬網路 (VNet) 或防火牆後面移動數據湖和倉庫終結點的數據。
- Azure 資料工廠具有企業級安全性:可以使用 Windows 安裝程式 (MSI) 或服務標識進行安全服務到服務整合,或使用 Azure 密鑰保管庫進行認證管理。
- Azure 數據工廠提供無代碼創作體驗和豐富的內置監視儀錶板。  

## <a name="online-vs-offline-data-migration"></a>線上與離線資料移轉

Azure 資料工廠是一種標準連線資料遷移工具,用於透過網路(Internet、ER或 VPN)傳輸數據。 而通過離線資料遷移,使用者將資料傳輸設備從組織實際運送到 Azure 資料中心。  

在連線和離線遷移方法之間進行選擇時,有三個關鍵注意事項:  

- 要移至資料的大小
- 網路頻寬
- 移轉視窗

例如,假設您計劃使用 Azure 數據工廠在兩周內完成數據*遷移(遷移視窗*)。 請注意下表中的粉紅色/藍色切割線。 任何給定列的最低粉紅色單元格顯示遷移視窗最接近但少於兩周的數據大小/網路頻寬配對。 (藍色單元格中的任何大小/頻寬配對的在線遷移視窗都超過兩周。 

![](media/data-migration-guidance-overview/online-offline.png)此表可幫助您根據資料大小和可用網路頻寬透過線上遷移(Azure資料工廠)確定是否滿足預期的遷移視窗。 如果連線遷移視窗超過兩周,則需要使用脫機遷移。

> [!NOTE]
> 通過使用線上遷移,您可以通過單個工具實現歷史數據載入和增量饋送。  通過此方法,可以在整個遷移視窗期間在現有存儲和新存儲之間保持同步。 這意味著您可以使用刷新的數據在新儲存上重建 ETL 邏輯。


## <a name="next-steps"></a>後續步驟

- [從 AWS S3 將資料遷移至 Azure](data-migration-guidance-s3-azure-storage.md)
- [將資料從本地雙有群集移到 Azure](data-migration-guidance-hdfs-azure-storage.md)
- [將資料從內部部署 Netezza 伺服器遷移到 Azure](data-migration-guidance-netezza-azure-sqldw.md)
