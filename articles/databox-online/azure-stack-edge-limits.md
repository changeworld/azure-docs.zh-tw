---
title: Azure Stack 的邊緣限制 |Microsoft Docs
description: 瞭解部署和操作 Azure Stack Edge 時的限制和建議大小，包括服務限制、裝置限制和儲存體限制。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/22/2019
ms.author: alkohli
ms.openlocfilehash: 8bbcf3f61121813b91cb951809992d10977a640c
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "87922705"
---
# <a name="azure-stack-edge-limits"></a>Azure Stack Edge 限制

當您部署和操作 Microsoft Azure Stack 邊緣解決方案時，請考慮這些限制。 

## <a name="azure-stack-edge-service-limits"></a>Azure Stack Edge 服務限制

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="azure-stack-device-limits"></a>Azure Stack 裝置限制

下表描述 Azure Stack Edge 裝置的限制。 

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

- [準備部署 Azure Stack Edge](azure-stack-edge-deploy-prep.md)
