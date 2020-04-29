---
title: Microsoft Azure Data Box Heavy 系統需求 |Microsoft Docs
description: 瞭解您 Azure Data Box Heavy 的軟體和網路需求
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: article
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 6097dba9193fce4c8158137b488080107881994a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79260068"
---
# <a name="azure-data-box-heavy-system-requirements"></a>Azure Data Box Heavy 系統需求

本文說明 Azure Data Box Heavy 裝置和連線到裝置的用戶端的重要系統需求。 我們建議您先仔細閱讀資訊，然後再部署 Data Box Heavy，然後在部署和後續作業期間視需要回頭查看。

系統需求包括：

* 連線**到 Data Box Heavy 之主機的軟體需求**-描述支援的平臺、本機 web UI 的瀏覽器、SMB 用戶端，以及可連接至資料箱之主機的任何其他需求。
* **Data Box Heavy 的網路需求**-提供 Data Box Heavy 裝置最佳操作的網路需求相關資訊。

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

您的資料中心必須有高速網路。 如需最快速的複製速度，可以平行使用 2 40-GbE 連線（每個節點一個）。 如果您沒有 40-GbE 可用，建議您至少有 2 10-GbE 連線（每個節點一個）。

### <a name="port-requirements"></a>連接埠需求

下表列出需要在防火牆中開啟以允許 SMB 或 NFS 流量的埠。 在這個資料表中，in** 或 inbound** 指的是輸入用戶端要求存取裝置的方向。 [ *Out* ] 或 [*輸出*] 是指您的 Data Box Heavy 裝置在外部將資料傳送到部署之外的方向：例如，輸出到網際網路。

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]

## <a name="next-steps"></a>後續步驟

* [部署 Azure 資料箱](data-box-deploy-ordered.md)
