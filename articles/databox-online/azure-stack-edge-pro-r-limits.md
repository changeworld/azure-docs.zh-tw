---
title: Azure Stack Edge Pro R 限制 |Microsoft Docs
description: 描述 Azure Stack Edge Pro R 的系統限制和建議大小。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: dfff3bdd716c54a6c83dbc9fec63c794c1fba85b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466323"
---
# <a name="azure-stack-edge-pro-r-limits"></a>Azure Stack Edge Pro R 限制

當您部署和操作 Azure Stack Edge Pro R 解決方案時，請考慮這些限制。

## <a name="azure-stack-edge-pro-r-service-limits"></a>Azure Stack Edge Pro R 服務限制

[!INCLUDE [azure-stack-edge-gateway-service-limits](../../includes/azure-stack-edge-gateway-service-limits.md)]

## <a name="azure-stack-edge-pro-r-device-limits"></a>Azure Stack Edge Pro R 裝置限制

下表說明 Azure Stack Edge Pro R 裝置的限制。

| 描述 | 值 |
|---|---|
|否。 (每個裝置的檔案) |1 億 |
|否。 共用數 |1 |
|最大值。 每個裝置的共用端點和 REST 端點| 24 |
|最大值。 每一裝置的分層式儲存體帳戶| 24|
|寫入至共用的檔案大小上限| 5 TB |
|每一裝置的資源群組數目上限| 800 |

## <a name="azure-storage-limits"></a>Azure 儲存體限制

[!INCLUDE [azure-stack-edge-gateway-storage-limits](../../includes/azure-stack-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>資料上傳注意事項

[!INCLUDE [azure-stack-edge-gateway-storage-data-upload-caveats](../../includes/azure-stack-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Azure 儲存體帳戶大小和物件大小限制

[!INCLUDE [azure-stack-edge-gateway-storage-acct-limits](../../includes/azure-stack-edge-gateway-storage-acct-limits.md)]


## <a name="azure-object-size-limits"></a>Azure 物件大小限制

[!INCLUDE [azure-stack-edge-gateway-storage-object-limits](../../includes/azure-stack-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>後續步驟

- [準備部署 Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-prep.md)
