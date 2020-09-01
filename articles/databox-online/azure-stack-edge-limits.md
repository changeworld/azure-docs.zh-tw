---
title: Azure Stack Edge 限制 |Microsoft Docs
description: 當您部署和操作 Azure Stack Edge （包括服務限制、裝置限制和儲存體限制）時，瞭解限制和建議大小。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: e21f5a89d9f1f21eb99071a141794f99c07a8dfa
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89079807"
---
# <a name="azure-stack-edge-limits"></a>Azure Stack Edge 限制

當您部署和操作 Microsoft Azure Stack Edge 解決方案時，請考慮這些限制。 

## <a name="azure-stack-edge-service-limits"></a>Azure Stack Edge 服務限制

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="azure-stack-device-limits"></a>Azure Stack 裝置限制

下表說明 Azure Stack Edge 裝置的限制。 

| 描述 | 值 |
|---|---|
|不知道。 (每個裝置的檔案) |1 億 |
|不知道。 (每個裝置的共用) |24 |
|不知道。 共用數 |1 |
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
