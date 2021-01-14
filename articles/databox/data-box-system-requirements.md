---
title: Microsoft Azure 資料箱的系統需求 | Microsoft Docs
description: 瞭解 Azure 資料箱的重要系統需求，以及連線至資料箱的用戶端。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 12/23/2020
ms.author: alkohli
ms.openlocfilehash: 0190e295c2ab206242ab8a44a09ffb42746d75bd
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98209540"
---
# <a name="azure-data-box-system-requirements"></a>Azure 資料箱的系統需求

本文說明 Microsoft Azure 資料箱以及連線至資料箱的用戶端的重要系統需求。 建議您在部署資料箱之前仔細檢查資訊，然後在部署和作業期間，于需要時參考資訊。

系統需求包括：

* **軟體需求：** 針對連線至資料箱的主機，描述本機 web UI 支援的作業系統、檔案傳輸協定、儲存體帳戶、儲存體類型和瀏覽器。
* **網路需求：** 針對資料箱，描述網路連線和埠的需求，以取得資料箱的最佳操作。


## <a name="software-requirements"></a>軟體需求

軟體需求包括支援的作業系統、檔案傳輸通訊協定、儲存體帳戶、儲存體類型，以及本機 web UI 的瀏覽器。

### <a name="supported-operating-systems-for-clients"></a>支援用戶端的作業系統

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]


### <a name="supported-file-transfer-protocols-for-clients"></a>用戶端支援的檔案傳輸協定

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

> [!IMPORTANT] 
> 資料箱共用的連接不支援透過 REST 進行匯出訂單。 

### <a name="supported-storage-accounts"></a>支援的儲存體帳戶

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>支援的儲存體類型

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>支援的網頁瀏覽器

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>網路需求

您的資料中心必須有高速網路。 強烈建議您至少要有 1 10-GbE 的連接。 如果無法使用 10 GbE 的連線，您可以使用 1 GbE 資料連結來複製資料，但是複製速度會受到影響。

### <a name="port-requirements"></a>連接埠需求

下表列出必須在您的防火牆中開啟以允許 SMB 或 NFS 流量的埠。 在 *下表中，* (*輸入*) 指的是傳入用戶端要求存取裝置的方向。 *輸出* (或 *輸出*) 指的是您的資料箱裝置在外部傳送資料的方向，而不是在部署之外。 例如，資料可能會輸出到網際網路。

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]


## <a name="next-steps"></a>後續步驟

* [部署 Azure 資料箱](data-box-deploy-ordered.md)
