---
title: Azure 資料箱閘道限制 | Microsoft Docs
description: 說明 Microsoft Azure 資料箱閘道的系統限制和建議大小。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/20/2019
ms.author: alkohli
ms.openlocfilehash: d160b0ead1ceb8c41bede20cb389a360d24258f9
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82561381"
---
# <a name="azure-data-box-gateway-limits"></a>Azure 資料箱閘道限制

當您部署和操作 Microsoft Azure 資料箱閘道解決方案時，請考慮這些限制。

## <a name="data-box-gateway-service-limits"></a>資料箱閘道服務限制

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="data-box-gateway-device-limits"></a>資料箱閘道裝置限制

下表說明資料箱閘道裝置的限制。

| 描述 | 值 |
|---|---|
|不需要。 (每個裝置的檔案) |1 億 <br> 針對每個要新增的25000000檔案（最大限制為100000000），您應該新增 2 TB 的磁碟空間、8 GB 的 RAM，以及4個 CPU 核心。 |
|不需要。 (每個裝置的共用) |24 |
|不需要。 每個 Azure 儲存體容器的共用 |1 |
|寫入至共用的檔案大小上限|針對 2 TB 的虛擬裝置，檔案大小上限為 500 GB。 <br> 檔案大小上限會以上述比例隨著資料磁碟大小增加，直到達到上限 5 TB 為止。 |

## <a name="azure-storage-limits"></a>Azure 儲存體限制

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>資料上傳注意事項

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Azure 儲存體帳戶大小和物件大小限制

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]

## <a name="azure-object-size-limits"></a>Azure 物件大小限制

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>後續步驟

- [準備部署 Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
