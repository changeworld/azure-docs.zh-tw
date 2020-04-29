---
title: 安全性代理程式本機設定（C）
description: 瞭解適用于 C 之代理程式本機設定的 Azure 資訊安全中心。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: cd344b9bebb69af210c482f46af6b2dd7edf7816
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81311697"
---
# <a name="understanding-the-localconfigurationjson-file---c-agent"></a>了解 LocalConfiguration.json 檔案 - C 代理程式

IoT 安全性代理程式的 Azure 資訊安全中心會使用本機設定檔案中的設定。
在代理程式啟動時，安全性代理程式會讀取設定一次。
本機設定檔中找到的設定包含驗證設定和其他代理程式相關設定。
此檔案包含以 JSON 標記法的「機碼/值」組設定，並在安裝代理程式時填入設定。

根據預設，檔案位於：/var/ASCIoTAgent/LocalConfiguration.json

當代理程式重新開機時，就會變更設定檔案。

## <a name="security-agent-configurations-for-c"></a>適用于 C 的安全性代理程式設定

| 設定名稱 | 可能值 | 詳細資料 |
|:-----------|:---------------|:--------|
| AgentId | GUID | 代理程式的唯一識別碼 |
| TriggerdEventsInterval | ISO8601 字串 | 觸發的事件集合的排程器間隔 |
| ConnectionTimeout | ISO8601 字串 | IoThub 連線超時之前的時間週期 |
| 驗證 | JsonObject | 驗證設定。 此物件包含對 IoTHub 進行驗證所需的所有資訊。 |
| 身分識別 | "DPS"、"SecurityModule"、"Device" | 驗證身分識別-如果透過 DPS 進行驗證，則為 DPS，如果使用裝置認證進行驗證，則會 SecurityModule 驗證是否經由安全性模組認證或裝置 |
| AuthenticationMethod | "SasToken"、"SelfSignedCertificate" | 驗證的使用者密碼-如果 [使用秘密] 是對稱金鑰，請選擇 [SasToken]，如果密碼是自我簽署憑證，請選擇 [自我簽署憑證]  |
| FilePath | 檔案路徑（字串） | 包含驗證密碼之檔案的路徑 |
| HostName | 字串 | Azure iot 中樞的主機名稱。 通常 <我的中樞>。 azure-devices.net |
| DeviceId | 字串 | 裝置的識別碼（如 Azure IoT 中樞中所註冊） |
| DPS | JsonObject | DPS 相關設定 |
| IDScope | 字串 | DPS 的識別碼範圍 |
| RegistrationId | 字串  | DPS 裝置註冊識別碼 |
| 記錄 | JsonObject | 代理程式記錄器的相關設定 |
| SystemLoggerMinimumSeverity | 0 <= 數位 <= 4 | 等於和高於此嚴重性的記錄訊息會記錄到/var/log/syslog （0是最低嚴重性） |
| DiagnosticEventMinimumSeverity | 0 <= 數位 <= 4 | 等於和高於此嚴重性的記錄訊息會當做診斷事件傳送（0是最低嚴重性） |

## <a name="security-agent-configurations-code-example"></a>安全性代理程式設定程式碼範例

```JSON
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

- 閱讀 IoT 服務的 Azure 資訊安全中心[總覽](overview.md)
- 深入瞭解 IoT[架構](architecture.md)的 Azure 資訊安全中心
- 啟用 IoT[服務](quickstart-onboard-iot-hub.md)的 Azure 資訊安全中心
- 閱讀 IoT 服務的 Azure 資訊安全中心[常見問題](resources-frequently-asked-questions.md)
- 了解如何存取[未經處理的安全性資料](how-to-security-data-access.md)
- 了解[建議](concept-recommendations.md)
- 瞭解安全性[警示](concept-security-alerts.md)
