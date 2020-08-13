---
title: 使用設備針對 Azure 進行資料傳輸的選項 | Microsoft Docs
description: 瞭解如何選擇適用于在 Data Box Edge、Azure 檔案同步和 StorSimple 8000 系列之間將內部部署資料傳輸至 Azure 的應用裝置。
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: 903342230b28f8e4fbdcdfd93543e5a0aa54975e
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2020
ms.locfileid: "88183270"
---
# <a name="compare-storsimple-with-azure-file-sync-and-data-box-edge-data-transfer-options"></a>比較 StorSimple、Azure 檔案同步及 Data Box Edge 資料傳輸選項 

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]
 
本檔概述將內部部署資料傳輸至 Azure 的選項，並比較： Data Box Edge 與 Azure 檔案同步與 StorSimple 8000 系列。

- **[Data Box Edge](/azure/databox-online/data-box-edge-overview)**：Data Box Edge 是內部部署網路設備，可將資料移入和移出 Azure，並具有能在上傳期間預先處理資料的 AI 功能 Edge 計算能力。 「資料箱閘道」是該裝置的虛擬版本，並具備相同的資料傳輸功能。
- **[Azure 檔案同步](/azure/storage/files/storage-sync-files-deployment-guide)**：您可使用 Azure 檔案同步將組織的檔案共用集中在 Azure 檔案服務中，同時保有內部部署檔案伺服器的靈活度、效能及相容性。 Azure 檔案同步會將 Windows Server 轉換成 Azure 檔案共用的快速快取。 Azure 檔案同步正式推出的消息已於 2018 前半年宣布。
- **[StorSimple](/azure/storsimple/storsimple-overview)**：StorSimple 是混合式裝置，可透過與 Azure 儲存體緊密整合，協助企業合併其儲存體基礎結構，以在單一解決方案上取得主要儲存體、資料保護、封存，以及災害復原功能。 如需 StorSimple 的產品生命週期，請造訪[這裡](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series)。

## <a name="comparison-summary"></a>比較摘要

|                           |StorSimple 8000   |Azure 檔案同步   |資料箱邊緣           |
|---------------------------|----------------------------------------|-------------------------------|-----------------------------------------|
|**概觀**     |分層式的混合式儲存體及封存|一般檔案伺服器儲存體，具備雲端階層和多站台同步。  |儲存體解決方案以預先處理資料，並將它透過網路傳送至 Azure。        |
|**案例**    |檔案伺服器、封存、備份目標 |檔案伺服器、封存 (多站台)   |資料傳輸、資料預先處理 (包括 ML 推斷)、IoT、封存    |
|**Edge 計算** |無法使用 |無法使用 |支援使用 Azure IoT Edge 執行容器    |
|**板型規格**  |實體裝置   |安裝在 Windows Server 上的代理程式 |實體裝置   |
|**硬體**     |由 Microsoft 作為服務的一部分提供的實體裝置 | 由客戶提供 |由 Microsoft 作為服務的一部分提供的實體裝置  |
|**資料格式**  |自訂格式   |檔案儲存體         |Blob 或檔案    |
|**通訊協定支援** |iSCSI          |SMB、NFS    | SMB 或 NFS      |
|**定價**      |[StorSimple](https://azure.microsoft.com/pricing/details/storsimple/) |[Azure 檔案同步](https://azure.microsoft.com/pricing/details/storage/files/)  |[資料箱邊緣](https://azure.microsoft.com/pricing/details/storage/databox/edge/)  |

## <a name="next-steps"></a>後續步驟

- 了解 [Azure Data Box Edge](/azure/databox-online/data-box-edge-overview) 和 [Azure 資料箱閘道](/azure/databox-online/data-box-gateway-overview)
- 了解 [Azure 檔案同步](/azure/storage/files/storage-sync-files-deployment-guide)
