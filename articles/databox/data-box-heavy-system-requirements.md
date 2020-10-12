---
title: Microsoft Azure Data Box Heavy 系統需求 |Microsoft Docs
description: 瞭解 Azure Data Box Heavy 的軟體和網路需求
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: article
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 6097dba9193fce4c8158137b488080107881994a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84707747"
---
# <a name="azure-data-box-heavy-system-requirements"></a>Azure Data Box Heavy 系統需求

本文說明 Azure Data Box Heavy 裝置以及連線至裝置的用戶端的重要系統需求。 建議您先仔細檢查資訊，再部署 Data Box Heavy，然後在部署和後續作業期間，視需要回頭查看。

系統需求包括：

* 連線**至 Data Box Heavy 的主機軟體需求**-說明支援的平臺、本機 web UI 的瀏覽器、SMB 用戶端，以及可以連線到資料箱之主機的任何其他需求。
* **Data Box Heavy 的網路需求** -提供 Data Box Heavy 裝置最佳操作的網路需求相關資訊。

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

您的資料中心必須有高速網路。 為了達到最快的複製速度，2 40-GbE 連接可以平行使用 (每個節點) 一個連線。 如果您沒有 40-GbE 可用，建議您在每個節點)  (一個以上的 2 10-GbE 連接。

### <a name="port-requirements"></a>連接埠需求

下表列出必須在您的防火牆中開啟以允許 SMB 或 NFS 流量的埠。 在這個資料表中，in** 或 inbound** 指的是輸入用戶端要求存取裝置的方向。 *輸出* 或 *輸出* 是指您的 Data Box Heavy 裝置在外部傳送資料的方向，而不是部署：例如，輸出到網際網路。

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]

## <a name="next-steps"></a>接下來的步驟

* [部署 Azure 資料箱](data-box-deploy-ordered.md)
