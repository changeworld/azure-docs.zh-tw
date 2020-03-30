---
title: 微軟 Azure 資料盒 重型系統要求*微軟文檔
description: 瞭解 Azure 資料盒繁重的軟體和網路要求
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: article
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 6097dba9193fce4c8158137b488080107881994a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260068"
---
# <a name="azure-data-box-heavy-system-requirements"></a>Azure 資料盒 重系統要求

本文介紹了 Azure 資料盒重設備和連接到設備的用戶端的重要系統要求。 我們建議您在部署資料盒重時仔細查看資訊，然後在部署和後續操作期間根據需要將其引用。

系統需求包括：

* **連接到資料框繁重的主機的軟體要求**- 描述支援的平臺、本地 Web UI 的瀏覽器、SMB 用戶端以及可連接到資料框的主機的任何其他要求。
* **資料盒重的網路要求**- 提供有關資料盒重型設備最佳操作的網路要求的資訊。

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

您的資料中心必須有高速網路。 為了最快的複製速度，可以並行使用兩個 40 GbE 連接（每個節點一個）。 如果沒有 40 GbE 可用，我們建議您至少有兩個 10 GbE 連接（每個節點一個）。

### <a name="port-requirements"></a>連接埠需求

下表列出了需要在防火牆中打開以允許 SMB 或 NFS 流量的埠。 在這個資料表中，in** 或 inbound** 指的是輸入用戶端要求存取裝置的方向。 *出**或出站*是指資料盒重設備在部署之外向外部發送資料的方向：例如，出站到 Internet。

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]

## <a name="next-steps"></a>後續步驟

* [部署 Azure 資料箱](data-box-deploy-ordered.md)
