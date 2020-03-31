---
title: 將資料從資料湖和資料倉儲遷移到 Azure
description: 使用 Azure 資料工廠將資料從資料湖和資料倉儲遷移到 Azure。
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
ms.openlocfilehash: aaf1593cc049e8b23f8ebe36fea022b3029ccd04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930807"
---
# <a name="use-azure-data-factory-to-migrate-data-from-your-data-lake-or-data-warehouse-to-azure"></a>使用 Azure 資料工廠將資料從資料湖或資料倉儲遷移到 Azure

如果要將資料湖或企業資料倉儲 （EDW） 遷移到 Microsoft Azure，請考慮使用 Azure 資料工廠。 Azure 資料工廠非常適合以下方案：

- 大資料工作負載從 Amazon 簡單存儲服務 （Amazon S3） 或本地 Hadoop 分散式檔案系統 （HDFS） 遷移到 Azure
- EDW 從 Oracle Exadata、Netezza、Teradata 或 Amazon 紅移遷移到 Azure

Azure 資料工廠可以移動 PB 的資料以進行資料湖遷移，並為資料倉儲遷移移動數十 TB 的資料。

## <a name="why-azure-data-factory-can-be-used-for-data-migration"></a>為什麼 Azure 資料工廠可用於資料移轉

- Azure 資料工廠可以輕鬆地擴展處理能力，以無伺服器方式移動資料，具有高性能、彈性和可擴充性。 而你只為你使用的東西付費。 同時應注意下列項目： 
  - Azure 資料工廠對資料卷或檔數量沒有限制。
  - Azure 資料工廠可以充分利用網路和存儲頻寬，實現環境中資料移動輸送量最高的。
  - Azure 資料工廠使用即用即付方法，因此只需為實際用於運行資料移轉到 Azure 的時間付費。  
- Azure 資料工廠可以執行一次性歷史載入和計畫增量載入。
- Azure 資料工廠使用 Azure 集成運行時 （IR） 在可公開訪問的資料湖和倉庫終結點之間移動資料。 它還可以使用自託管 IR 在 Azure 虛擬網路 （VNet） 或防火牆後面移動資料湖和倉庫終結點的資料。
- Azure 資料工廠具有企業級安全性：可以使用 Windows 安裝程式 （MSI） 或服務標識進行安全服務到服務集成，或使用 Azure 金鑰保存庫進行憑據管理。
- Azure 資料工廠提供無代碼創作體驗和豐富的內置監視儀表板。  

## <a name="online-vs-offline-data-migration"></a>線上與離線資料移轉

Azure 資料工廠是一種標準連線資料移轉工具，用於通過網路（Internet、ER 或 VPN）傳輸資料。 而通過離線資料移轉，使用者將資料傳輸設備從組織實際運送到 Azure 資料中心。  

在連線和離線遷移方法之間進行選擇時，有三個關鍵注意事項：  

- 要遷移的資料的大小
- 網路頻寬
- 遷移視窗

例如，假設您計畫使用 Azure 資料工廠在兩周內完成資料*遷移（遷移視窗*）。 請注意下表中的粉紅色/藍色切割線。 任何給定列的最低粉紅色儲存格顯示遷移視窗最接近但少於兩周的資料大小/網路頻寬配對。 （藍色儲存格中的任何大小/頻寬配對的線上遷移視窗都超過兩周。 

![連線與離線](media/data-migration-guidance-overview/online-offline.png)此表可説明您根據資料大小和可用網路頻寬通過連線遷移（Azure 資料工廠）確定是否滿足預期的遷移視窗。 如果連線遷移視窗超過兩周，則需要使用離線遷移。

> [!NOTE]
> 通過使用線上遷移，您可以通過單個工具實現歷史資料載入和增量饋送。  通過此方法，可以在整個遷移視窗期間在現有存儲和新存儲之間保持同步。 這意味著您可以使用刷新的資料在新存儲上重建 ETL 邏輯。


## <a name="next-steps"></a>後續步驟

- [從 AWS S3 將資料遷移至 Azure](data-migration-guidance-s3-azure-storage.md)
- [將資料從本地雙有群集遷移到 Azure](data-migration-guidance-hdfs-azure-storage.md)
- [將資料從內部部署 Netezza 伺服器遷移到 Azure](data-migration-guidance-netezza-azure-sqldw.md)
