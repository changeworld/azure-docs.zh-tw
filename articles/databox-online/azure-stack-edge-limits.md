---
title: Azure Stack Edge Pro 限制 |Microsoft Docs
description: 當您部署和操作 Azure Stack Edge Pro 時，請瞭解限制和建議的大小，包括服務限制、裝置限制和儲存體限制。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 5f3c39ce7334145d3ffc1d54badb1f7b766da70f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90904445"
---
# <a name="azure-stack-edge-pro-limits"></a>Azure Stack Edge Pro 限制

當您部署和操作 Microsoft Azure Stack Edge Pro 解決方案時，請考慮這些限制。 

## <a name="azure-stack-edge-service-limits"></a>Azure Stack Edge 服務限制

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="azure-stack-edge-device-limits"></a>Azure Stack Edge 裝置限制

下表說明 Azure Stack Edge Pro 裝置的限制。 

| 描述 | 值 |
|---|---|
|否。 (每個裝置的檔案) |1 億 |
|否。 (每個裝置的共用) |24 |
|否。 共用數 |1 |
|寫入至共用的檔案大小上限| 5 TB |

## <a name="azure-storage-limits"></a>Azure 儲存體限制

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>資料上傳注意事項

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Azure 儲存體帳戶大小和物件大小限制

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]


## <a name="azure-object-size-limits"></a>Azure 物件大小限制

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>後續步驟

- [準備部署 Azure Stack Edge Pro](azure-stack-edge-deploy-prep.md)
