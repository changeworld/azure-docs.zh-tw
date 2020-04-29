---
title: Microsoft Azure 資料箱的系統需求 | Microsoft Docs
description: 了解 Azure 資料箱的軟體和網路需求
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: e232ad131b1c0930afcf5e7e78b386aba2c9490b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79259951"
---
# <a name="azure-data-box-system-requirements"></a>Azure 資料箱的系統需求

本文會針對 Microsoft Azure 資料箱以及連線至資料箱的用戶端，說明其各自的重要系統需求。 建議您先仔細檢閱此資訊再部署資料箱，之後在部署和後續作業期間若有必要，也請回頭查閱。

系統需求包括：

* **連線到資料箱的主機軟體需求** - 說明支援的平台、本機 Web UI 的瀏覽器、SMB 用戶端，以及可以連線到資料箱的主機其他需求。
* **資料箱的網路需求** - 提供資料箱最佳作業的網路需求相關資訊。


## <a name="software-requirements"></a>軟體需求

軟體需求包括支援的作業系統、支援的本機 Web UI 瀏覽器和 SMB 用戶端相關資訊。

### <a name="supported-operating-systems-for-clients"></a>支援用戶端的作業系統

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]

### <a name="supported-file-systems-for-linux-clients"></a>Linux 用戶端的支援檔案系統

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

### <a name="supported-storage-accounts"></a>支援的儲存體帳戶

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>支援的儲存體類型

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>支援的網頁瀏覽器

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>網路需求

您的資料中心必須有高速網路。 強烈建議您具有至少一個 10 GbE 的連線。 如果無法使用 10 GbE 連線，也可以使用 1 GbE 資料連結來複製資料，但是複製速度會受到影響。

### <a name="port-requirements"></a>連接埠需求

下表列出需要在防火牆中開啟以允許 SMB 或 NFS 流量的埠。 在這個資料表中，in** 或 inbound** 指的是輸入用戶端要求存取裝置的方向。 [ *Out* ] 或 [*輸出*] 是指您的資料箱裝置在外部將資料傳送到部署之外的方向：例如，輸出到網際網路。

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]


## <a name="next-steps"></a>後續步驟

* [部署 Azure 資料箱](data-box-deploy-ordered.md)
