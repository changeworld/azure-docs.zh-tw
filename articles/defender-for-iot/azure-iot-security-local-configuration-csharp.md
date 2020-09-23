---
title: '適用于 IoT 的 Defender 安全性代理程式本機設定 (c # ) '
description: '深入瞭解適用于 IoT 安全性服務的 Defender、適用于 c # 的安全性代理程式本機設定檔。'
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.custom: devx-track-csharp
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: 19fa5b2949888993954f3075d1e10c9e8f126e2f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934495"
---
# <a name="understanding-the-local-configuration-file-c-agent"></a>瞭解 (c # 代理程式) 的本機設定檔

適用于 IoT 的 Defender 安全性代理程式會使用本機設定檔案中的設定。

當代理程式啟動時，安全性代理程式會讀取設定檔一次。 在本機設定檔中找到的設定包含驗證設定和其他代理程式相關設定。

C # 安全性代理程式會使用多個設定檔：

- **General.config** 代理程式的相關設定。
- **Authentication.config** 驗證相關設定 (包括驗證詳細資料) 。
- **SecurityIotInterface.config** -IoT 相關的設定。

設定檔包含預設設定。 系統會在代理程式安裝期間填入驗證設定，並在重新開機代理程式時，對設定檔進行變更。

## <a name="configuration-file-location"></a>設定檔位置

若為 Linux：

- 作業系統設定檔位於 `/var/ASCIoTAgent` 。

若為 Windows：

- 作業系統設定檔位於安全性代理程式的目錄中。

### <a name="generalconfig-configurations"></a>General.config 設定

| 設定名稱 | 可能值 | 詳細資料 |
|:-----------|:---------------|:--------|
| agentId | GUID | 代理程式唯一識別碼 |
| readRemoteConfigurationTimeout | TimeSpan | 從 IoT 中樞提取遠端設定的時間週期。 如果代理程式無法在指定的時間內提取設定，此作業將會超時。|
| schedulerInterval | TimeSpan | 內部排程器間隔。 |
| producerInterval | TimeSpan | 事件生產者工作者間隔。 |
| consumerInterval | TimeSpan | 事件取用者工作者間隔。 |
| highPriorityQueueSizePercentage | 0 < 數位 < 1 | 高優先順序訊息專用的總快取部分。 |
| logLevel | 「Off」、「嚴重」、「錯誤」、「警告」、「資訊」、「Debug」  | 等於或高於此嚴重性的記錄訊息會記錄到 Linux) 中 (Syslog 的 debug 主控台。 |
| fileLogLevel |  「Off」、「嚴重」、「錯誤」、「警告」、「資訊」、「Debug」| 等於或高於此嚴重性的記錄訊息會記錄到 Linux) 的 (Syslog 檔案中。 |
| diagnosticVerbosityLevel | "None"、"Some"、"All"、 | 診斷事件的詳細程度層級。 無-不傳送診斷事件，只會傳送具有高重要性的部分診斷事件，所有記錄也會以診斷事件的形式傳送。 |
| >logfilepath | 檔案路徑 | 如果 fileLogLevel > Off，記錄檔會寫入至這個檔案。 |
| defaultEventPriority | 「高」、「低」、「關閉」 | 預設事件優先順序。 |

### <a name="generalconfig-example"></a>General.config 範例

```xml
<?xml version="1.0" encoding="utf-8"?>
<General>
  <add key="agentId" value="da00006c-dae9-4273-9abc-bcb7b7b4a987" />
  <add key="readRemoteConfigurationTimeout" value="00:00:30" />
  <add key="schedulerInterval" value="00:00:01" />
  <add key="producerInterval" value="00:02:00" />
  <add key="consumerInterval" value="00:02:00" />
  <add key="highPriorityQueueSizePercentage" value="0.5" />
  <add key="logLevel" value="Information" />
  <add key="fileLogLevel" value="Off"/>
  <add key="diagnosticVerbosityLevel" value="Some" />
  <add key="logFilePath" value="IotAgentLog.log" />
  <add key="defaultEventPriority" value="Low"/>
</General>
```

### <a name="authenticationconfig"></a>Authentication.config

| 組態名稱 | 可能值 | 詳細資料 |
|:-----------|:---------------|:--------|
| moduleName | 字串 | 安全性模組身分識別的名稱。 此名稱必須對應至裝置中的模組身分識別名稱。 |
| deviceId | 字串 | 在 Azure IoT 中樞) 中註冊的裝置 (識別碼。 || schedulerInterval | TimeSpan 字串 | 內部排程器間隔。 |
| gatewayHostname | 字串 | Azure Iot 中樞的主機名稱。 通常 <我的中樞>。 azure-devices.net |
| filePath | 字串-檔案的路徑 | 包含驗證秘密之檔案的路徑。|
| 類型 | "SymmetricKey"、"New-selfsignedcertificate" | 驗證的使用者密碼。 如果使用者密碼是對稱金鑰，請選擇 [ *SymmetricKey* ]，如果秘密是自我簽署的憑證，請選擇 *自我簽署憑證* 。 |
| 身分識別 | 「DPS」、「模組」、「裝置」 | 驗證身分識別-如果透過 DPS 進行驗證，則為 DPS、使用模組認證進行驗證的模組，或使用裝置認證進行驗證時的裝置。
| certificateLocationKind |  "LocalFile"、"Store" | LocalFile 如果憑證儲存在檔案中，請儲存憑證是否位於憑證存放區中。 |
| idScope | 字串 | DPS 的識別碼範圍 |
| registrationId | 字串  | DPS 裝置註冊識別碼。 |
|

### <a name="authenticationconfig-example"></a>Authentication.config 範例

```xml
<?xml version="1.0" encoding="utf-8"?>
<Authentication>
  <add key="moduleName" value="azureiotsecurity"/>
  <add key="deviceId" value="d1"/>
  <add key="gatewayHostname" value=""/>
  <add key="filePath" value="c:\p-dps-d1.pfx"/>
  <add key="type" value="SelfSignedCertificate" />                     <!-- SymmetricKey, SelfSignedCertificate-->
  <add key="identity" value="DPS" />                 <!-- Device, Module, DPS -->
  <add key="certificateLocationKind" value="LocalFile" />  <!-- LocalFile, Store -->
  <add key="idScope" value="0ne0005335B"/>
  <add key="registrationId" value="d1"/>
</Authentication>
```

### <a name="securityiotinterfaceconfig"></a>SecurityIotInterface.config

| 設定名稱 | 可能值 | 詳細資料 |
|:-----------|:---------------|:--------|
| transportType | "Ampq" "Mqtt" | IoT 中樞傳輸類型。 |
|

### <a name="securityiotinterfaceconfig-example"></a>SecurityIotInterface.config 範例

```xml
<ExternalInterface>
  <add key="facadeType"  value="Microsoft.Azure.Security.IoT.Agent.Common.SecurityIoTHubInterface, Security.Common" />
  <add key="transportType" value="Amqp"/>
</ExternalInterface>
```

## <a name="next-steps"></a>下一步

- 閱讀適用于 IoT 的 Defender 服務 [總覽](overview.md)
- 深入瞭解適用于 IoT 的 Defender [架構](architecture.md)
- 啟用適用于 IoT 的 Defender [服務](quickstart-onboard-iot-hub.md)
- 閱讀適用于 IoT 的 Defender 服務 [常見問題](resources-frequently-asked-questions.md)
- 了解如何存取[未經處理的安全性資料](how-to-security-data-access.md)
- 了解[建議](concept-recommendations.md)
- 瞭解安全性 [警示](concept-security-alerts.md)
