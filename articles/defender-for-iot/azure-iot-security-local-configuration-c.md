---
title: '安全性代理程式本機設定 (C) '
description: 瞭解適用于 C 的代理程式本機設定的 Defender。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: elazark
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/08/2020
ms.author: v-ekrieg
ms.openlocfilehash: d96a73c3ba996fecf24a4232e1391f0b814be868
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91851249"
---
# <a name="understanding-the-localconfigurationjson-file---c-agent"></a>了解 LocalConfiguration.json 檔案 - C 代理程式

適用于 IoT 的 Defender 安全性代理程式會使用本機設定檔案中的設定。
當代理程式啟動時，安全性代理程式會讀取設定一次。
在本機設定檔中找到的設定包含驗證設定和其他代理程式相關設定。
檔案包含 JSON 標記法中「機碼/值」配對的設定，並在安裝代理程式時填入設定。

根據預設，檔案位於：/var/ASCIoTAgent/LocalConfiguration.js開啟

重新開機代理程式時，會發生設定檔案的變更。

## <a name="security-agent-configurations-for-c"></a>C 的安全性代理程式設定

| 設定名稱 | 可能值 | 詳細資料 |
|:-----------|:---------------|:--------|
| AgentId | GUID | 代理程式唯一識別碼 |
| TriggerdEventsInterval | ISO8601 字串 | 觸發事件集合的排程器間隔 |
| ConnectionTimeout | ISO8601 字串 | 連線至 IoThub 之前的時間間隔已超時 |
| 驗證 | >jsonobject | 驗證設定。 此物件包含對 IoTHub 進行驗證所需的所有資訊 |
| 身分識別 | "DPS"、"SecurityModule"、"Device" | 驗證身分識別-如果透過 DPS 進行驗證，則為 DPS，如果使用裝置認證進行驗證，則 SecurityModule 是否透過安全性模組認證或裝置進行驗證 |
| AuthenticationMethod | "SasToken"、"New-selfsignedcertificate" | 驗證的使用者密碼-如果 [使用密碼] 是對稱金鑰，請選擇 [SasToken]。如果秘密是自我簽署憑證，請選擇 [自我簽署憑證]  |
| FilePath | 檔案的路徑 (字串)  | 包含驗證秘密之檔案的路徑 |
| HostName | 字串 | Azure IoT 中樞的主機名稱。 通常 <我的中樞>。 azure-devices.net |
| DeviceId | 字串 | 在 Azure IoT 中樞中註冊的裝置 (識別碼)  |
| DPS | >jsonobject | DPS 相關設定 |
| IDScope | 字串 | DPS 的識別碼範圍 |
| RegistrationId | 字串  | DPS 裝置註冊識別碼 |
| 記錄 | >jsonobject | 代理程式記錄器相關設定 |
| SystemLoggerMinimumSeverity | 0 <= 數位 <= 4 | 等於或高於此嚴重性的記錄訊息會記錄到/var/log/syslog (0 是最低嚴重性)  |
| DiagnosticEventMinimumSeverity | 0 <= 數位 <= 4 | 等於或高於此嚴重性的記錄訊息將會傳送為診斷事件 (0 是最低嚴重性)  |

## <a name="security-agent-configurations-code-example"></a>安全性代理程式設定程式碼範例

```json
{
    "Configuration" : {
        "AgentId" : "b97faf0a-0f57-471f-9dab-46a8e1764946",
        "TriggerdEventsInterval" : "PT2M",
        "ConnectionTimeout" : "PT30S",
        "Authentication" : {
            "Identity" : "Device",
            "AuthenticationMethod" : "SasToken",
            "FilePath" : "/path/to/my/SymmetricKey",
            "DeviceId" : "my-device",
            "HostName" : "my-iothub.azure-devices.net",
            "DPS" : {
                "IDScope" : "",
                "RegistrationId" : ""
            }
        },
        "Logging": {
            "SystemLoggerMinimumSeverity": 0,
            "DiagnoticEventMinimumSeverity": 2
        }
    }
}
```

## <a name="next-steps"></a>後續步驟

- 閱讀適用于 IoT 的 Defender 服務 [總覽](overview.md)
- 深入瞭解適用于 IoT 的 Defender [架構](architecture.md)
- 啟用適用于 IoT 的 Defender [服務](quickstart-onboard-iot-hub.md)
- 閱讀適用于 IoT 的 Defender 服務 [常見問題](resources-frequently-asked-questions.md)
- 了解如何存取[未經處理的安全性資料](how-to-security-data-access.md)
- 了解[建議](concept-recommendations.md)
- 瞭解安全性 [警示](concept-security-alerts.md)
