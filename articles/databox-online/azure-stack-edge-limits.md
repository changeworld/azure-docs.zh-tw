---
title: Azure Stack Edge Pro 限制 |Microsoft Docs
description: 當您部署和操作 Azure Stack Edge Pro 時，請瞭解限制和建議的大小，包括服務限制、裝置限制和儲存體限制。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/12/2020
ms.author: alkohli
ms.openlocfilehash: f1bb5662b48765c08ec58d1f7fa9d341de4937bc
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91992758"
---
# <a name="azure-stack-edge-pro-limits"></a>Azure Stack Edge Pro 限制

當您部署和操作 Microsoft Azure Stack Edge Pro 解決方案時，請考慮這些限制。 

## <a name="azure-stack-edge-service-limits"></a>Azure Stack Edge 服務限制

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="azure-stack-edge-device-limits"></a>Azure Stack Edge 裝置限制

下表說明 Azure Stack Edge Pro 裝置的限制。 

下表說明 Azure Stack Edge 裝置的限制。

| 描述 | 值 |
|---|---|
|不可以。 (每個裝置的檔案) |1 億 |
|不可以。 共用數 |1 |
|最大值。 每個裝置的共用端點和 REST 端點| 24 |
|最大值。 每一裝置的分層式儲存體帳戶| 24|
|寫入至共用的檔案大小上限| 5 TB |
|每一裝置的資源群組數目上限| 800 |

## <a name="azure-storage-limits"></a>Azure 儲存體限制

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>資料上傳注意事項

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Azure 儲存體帳戶大小和物件大小限制

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]


## <a name="azure-object-size-limits"></a>Azure 物件大小限制

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>接下來的步驟

- [準備部署 Azure Stack Edge Pro](azure-stack-edge-deploy-prep.md)
