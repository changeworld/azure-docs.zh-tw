---
title: Azure 資料箱閘道公開上市版本資訊 |Microsoft Docs
description: 描述執行正式發行版本之 Azure 資料箱閘道的重大未解決問題和解決方法。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/26/2019
ms.author: alkohli
ms.openlocfilehash: f4ee3a5bd754335ab1c7f124671e9c37307a6a28
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79265398"
---
# <a name="azure-data-box-edgeazure-data-box-gateway-general-availability-release-notes"></a>Azure Data Box Edge/Azure 資料箱閘道公開上市版本資訊

## <a name="overview"></a>概觀

下列版本資訊指出 Azure Data Box Edge 和 Azure 資料箱閘道的重大開啟問題和已解決公開上市（GA）發行的問題。

版本資訊會持續更新，並在發現需要提出因應措施的重大問題時有所增補。 在您部署 Data Box Edge/Data Box Gateway 之前，請仔細檢查版本資訊中包含的資訊。

GA 版本對應于軟體版本：

- **資料箱閘道1903（1.5.814.447）**
- **Data Box Edge 1903 （1.5.814.447）**


## <a name="whats-new"></a>新功能

- **新的虛擬磁片映射**-新的 VHDX 和 VMDK 現在已可在 Azure 入口網站中取得。 下載這些映射以布建、設定和部署新的資料箱閘道 GA 裝置。 無法將在舊的預覽版本中建立的資料箱閘道裝置更新為此版本。 如需詳細資訊，請移至[準備部署 Azure 資料箱閘道](data-box-gateway-deploy-prep.md)。
- **Nfs 支援**-nfs 支援目前為預覽狀態，適用于存取 Data Box Edge 和資料箱閘道裝置的 v3.0 和4.1 版用戶端。
- **儲存體復原**-您的 Data Box Edge 裝置可以使用儲存體復原功能來承受一個資料磁片故障。 此功能目前為預覽狀態。 您可以在本機 web UI 的**儲存設定**中選取 [**復原**] 選項，以啟用儲存體復原功能。


## <a name="known-issues-in-ga-release"></a>GA 版本中的已知問題

下表提供執行版本之資料箱閘道的已知問題摘要。

| 否。 | 功能 | 問題 | 因應措施/註解 |
| --- | --- | --- | --- |
| **1.** |檔案類型 | 不支援下列檔案類型：字元檔案、區塊檔案、通訊端、管道、符號連結。  |複製這些檔案會導致在 NFS 共用上建立長度為 0 的檔案。 這些檔案處於錯誤狀態，也會在 error.xml 中回報。 <br> 目錄的符號連結導致目錄永遠不會標示為離線。 因此，您可能不會在目錄上看到灰色叉叉，此圖示表示目錄已離線，而且所有相關聯的內容皆已完整上傳至 Azure。 |
| **2.** |刪除 | 由於此版本有錯誤，如果已刪除 NFS 共用，則無法刪除此共用。 共用狀態會顯示「刪除中」。  |當共用使用不支援的檔案名稱時，才會發生這種情況。 |
| **3.** |[複製] | 資料複製失敗，發生錯誤：無法完成要求的作業，因為檔案系統有所限制。  |不支援與大於 128 KB 的檔案大小相關聯的替代資料流程（ADS）。   |


## <a name="next-steps"></a>後續步驟

- [準備部署 Azure 資料箱閘道](data-box-gateway-deploy-prep.md)。
- [準備部署 Azure Data Box Edge](data-box-edge-deploy-prep.md)。
