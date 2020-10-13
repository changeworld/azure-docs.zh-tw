---
title: Azure 資料箱 Gateway 正式推出版本資訊 |Microsoft Docs
description: 描述執行正式發行版本之 Azure 資料箱閘道的重大開啟問題和解決方式。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/26/2019
ms.author: alkohli
ms.openlocfilehash: 1a8a9840cc6e1f3627c5fbd30e0b7432db0f16e4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "82561043"
---
# <a name="azure-data-box-edgeazure-data-box-gateway-general-availability-release-notes"></a>Azure 資料箱 Edge/Azure 資料箱閘道正式推出版本資訊

## <a name="overview"></a>概觀

下列版本資訊指出 Azure 資料箱 Edge 和 Azure 資料箱閘道的正式運作 (GA) 版本的重大未解決問題和已解決的問題。 

版本資訊會持續更新，並在發現需要提出因應措施的重大問題時有所增補。 在您部署 Data Box Edge/Data Box Gateway 之前，請仔細檢查版本資訊中所包含的資訊。

GA 版本對應于軟體版本：

- **Data Box Gateway 1903 (1.5.814.447) **
- **Data Box Edge 1903 (1.5.814.447) **


## <a name="whats-new"></a>新功能

- **新的虛擬磁片映射** -Azure 入口網站中現在提供新的 VHDX 和 VMDK。 下載這些映射以布建、設定及部署新的資料箱閘道 GA 裝置。 無法將在舊的預覽版本中建立的資料箱閘道裝置更新為此版本。 如需詳細資訊，請移至[準備部署 Azure 資料箱閘道](data-box-gateway-deploy-prep.md)。
- **Nfs 支援** -nfs 支援目前為預覽狀態，可供存取 Data box Edge 和 Data box Gateway 裝置的 v3.0 和4.1 版用戶端使用。
- **儲存體復原** -您的 Data Box Edge 裝置可以使用儲存體復原功能來承受一個資料磁片失敗。 此功能目前為預覽狀態。 您可以在本機 web UI 中，選取**儲存體設定**中的**復原**選項來啟用儲存體復原。


## <a name="known-issues-in-ga-release"></a>GA 版本的已知問題

下表提供執行版本之資料箱閘道的已知問題摘要。

| 否。 | 功能 | 問題 | 因應措施/註解 |
| --- | --- | --- | --- |
| **1.** |檔案類型 | 不支援下列檔案類型：字元檔、區塊檔案、通訊端、管道、符號連結。  |複製這些檔案會導致在 NFS 共用上建立長度為 0 的檔案。 這些檔案處於錯誤狀態，也會在 error.xml** 中回報。 <br> 目錄的符號連結導致目錄永遠不會標示為離線。 因此，您可能不會在目錄上看到灰色叉叉，此圖示表示目錄已離線，而且所有相關聯的內容皆已完整上傳至 Azure。 |
| **2.** |刪除 | 由於此版本有錯誤，如果已刪除 NFS 共用，則無法刪除此共用。 共用狀態會顯示「刪除中」**。  |當共用使用不支援的檔案名稱時，才會發生這種情況。 |
| **3.** |複製 | 資料複製失敗，發生錯誤：因為檔案系統限制而無法完成要求的操作。  |不支援與檔案大小大於 128 KB 相關聯的替代資料串流 (ADS) 。   |


## <a name="next-steps"></a>接下來的步驟

- [準備部署 Azure 資料箱閘道](data-box-gateway-deploy-prep.md)。
- [準備部署 Azure 資料箱 Edge](azure-stack-edge-deploy-prep.md)。
