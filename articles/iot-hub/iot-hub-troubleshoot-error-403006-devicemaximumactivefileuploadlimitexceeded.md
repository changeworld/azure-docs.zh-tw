---
title: 疑難排解 Azure IoT 中樞錯誤 403006 DeviceMaximumActiveFileUploadLimitExceeded
description: 瞭解如何修正錯誤 403006 DeviceMaximumActiveFileUploadLimitExceeded
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 1e3c05e4cc3ccf34573b55d3729aded16e26d66e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76960837"
---
# <a name="403006-devicemaximumactivefileuploadlimitexceeded"></a>403006 DeviceMaximumActiveFileUploadLimitExceeded

本文說明**403006 DeviceMaximumActiveFileUploadLimitExceeded**錯誤的原因和解決方案。

## <a name="symptoms"></a>徵兆

您的檔案上傳要求失敗，錯誤碼為**403006** ，訊息為「活動檔案上傳要求數不能超過10個」。

## <a name="cause"></a>原因

每個裝置用戶端限制為[10 個並行](./iot-hub-devguide-quotas-throttling.md#other-limits)檔案上傳。 

如果您的裝置未在檔案上傳完成時通知 IoT 中樞，您可以輕鬆地超過限制。 此問題通常是因為不可靠的裝置端網路所造成。

## <a name="solution"></a>解決方法

請確定裝置可以立即[通知 IoT 中樞檔案上傳完成](./iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload)。 然後，請嘗試[減少 SAS 權杖 TTL 以進行檔案上傳](iot-hub-configure-file-upload.md)設定。

## <a name="next-steps"></a>後續步驟

若要深入瞭解檔案上傳，請參閱[上傳 IoT 中樞的](./iot-hub-devguide-file-upload.md)檔案和[使用 Azure 入口網站來設定 IoT 中樞](./iot-hub-configure-file-upload.md)檔案上傳。