---
title: 故障排除 Azure IoT 中心錯誤 403006 設備最大有效檔上傳限制超過
description: 瞭解如何修復錯誤 403006 設備最大主動檔上傳限制超過
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 1e3c05e4cc3ccf34573b55d3729aded16e26d66e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960837"
---
# <a name="403006-devicemaximumactivefileuploadlimitexceeded"></a>403006 DeviceMaximumActiveFileUploadLimitExceeded

本文介紹了**403006 設備最大主動檔上傳極限錯誤**的原因和解決方案。

## <a name="symptoms"></a>徵狀

您的檔上載請求失敗，錯誤代碼**403006**和消息"使用中的檔案上載請求的數量不能超過 10"。

## <a name="cause"></a>原因

每個設備用戶端限制為[10 個併發檔上載](./iot-hub-devguide-quotas-throttling.md#other-limits)。 

如果設備在檔上載完成後未通知 IoT 中心，則可以輕鬆地超過限制。 此問題通常是由不可靠的設備端網路引起的。

## <a name="solution"></a>解決方法

確保設備能夠及時[通知物聯網中心檔上傳完成](./iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload)。 然後，嘗試[減少檔上載配置的 SAS 權杖 TTL。](iot-hub-configure-file-upload.md)

## <a name="next-steps"></a>後續步驟

要瞭解有關檔上載的更多內容，請參閱[使用 IoT 中心上載檔和](./iot-hub-devguide-file-upload.md)[使用 Azure 門戶配置 IoT 中心檔上載](./iot-hub-configure-file-upload.md)。