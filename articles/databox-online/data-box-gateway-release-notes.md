---
title: Azure 資料盒閘道通用可用性版本資訊*微軟文檔
description: 介紹運行常規可用性版本的 Azure 資料盒閘道的關鍵打開問題和解決方法。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/26/2019
ms.author: alkohli
ms.openlocfilehash: f4ee3a5bd754335ab1c7f124671e9c37307a6a28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265398"
---
# <a name="azure-data-box-edgeazure-data-box-gateway-general-availability-release-notes"></a>Azure 資料框邊緣/Azure 資料框閘道通用可用性發佈說明

## <a name="overview"></a>總覽

以下版本資訊標識 Azure 資料框邊緣和 Azure 資料框閘道的通用可用性 （GA） 發佈的關鍵未決問題和已解決的問題。

版本資訊會持續更新，並在發現需要提出因應措施的重大問題時有所增補。 在部署資料框邊緣/資料盒閘道之前，請仔細查看版本資訊中包含的資訊。

GA 版本對應于軟體版本：

- **資料盒閘道 1903 （1.5.814.447）**
- **資料框邊緣 1903 （1.5.814.447）**


## <a name="whats-new"></a>新功能

- **新的虛擬磁片映射**- 新的 VHDX 和 VMDK 現在在 Azure 門戶中可用。 下載這些映射以預配、配置和部署新的資料盒閘道 GA 設備。 無法將在舊的預覽版本中建立的資料箱閘道裝置更新為此版本。 如需詳細資訊，請移至[準備部署 Azure 資料箱閘道](data-box-gateway-deploy-prep.md)。
- **NFS 支援**- NFS 支援當前處於預覽狀態，可用於訪問資料框邊緣和資料盒閘道設備的 v3.0 和 v4.1 用戶端。
- **存儲恢復能力**- 您的資料盒邊緣設備可以使用存儲恢復功能承受一個資料磁片的故障。 此功能目前為預覽狀態。 通過在本地 Web UI 中的 **"存儲"設置**中選擇 **"彈性"** 選項，可以啟用存儲恢復能力。


## <a name="known-issues-in-ga-release"></a>GA 版本中的已知問題

下表提供了運行版本的資料盒閘道的已知問題的摘要。

| 否。 | 功能 | 問題 | 因應措施/註解 |
| --- | --- | --- | --- |
| **1.** |檔案類型 | 不支援以下檔案類型：字元檔、塊檔、通訊端、管道、符號連結。  |複製這些檔案會導致在 NFS 共用上建立長度為 0 的檔案。 這些檔案處於錯誤狀態，也會在 error.xml** 中回報。 <br> 目錄的符號連結導致目錄永遠不會標示為離線。 因此，您可能不會在目錄上看到灰色叉叉，此圖示表示目錄已離線，而且所有相關聯的內容皆已完整上傳至 Azure。 |
| **2.** |刪除 | 由於此版本有錯誤，如果已刪除 NFS 共用，則無法刪除此共用。 共用狀態會顯示「刪除中」**。  |當共用使用不支援的檔案名稱時，才會發生這種情況。 |
| **3.** |[複製] | 資料複製失敗與錯誤：由於檔案系統限制，無法完成請求的操作。  |不支援與大於 128 KB 的檔案大小關聯的備用資料流程 （ADS）。   |


## <a name="next-steps"></a>後續步驟

- [準備部署 Azure 資料框閘道](data-box-gateway-deploy-prep.md)。
- [準備部署 Azure 資料框邊緣](data-box-edge-deploy-prep.md)。
