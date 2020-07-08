---
title: '安全性代理程式本機設定（c #）'
description: '深入瞭解 IoT 安全性服務的 Azure 資訊安全中心、c # 的安全性代理程式本機設定檔。'
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
ms.openlocfilehash: fc869a8ab905275c8082c4fd375f8f6d6d48d97e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85205453"
---
# <a name="understanding-the-local-configuration-file-c-agent"></a>瞭解本機設定檔案（c # 代理程式）

IoT 安全性代理程式的 Azure 資訊安全中心會使用本機設定檔案中的設定。

當代理程式啟動時，安全性代理程式會讀取設定檔。 本機設定檔中找到的設定同時包含驗證設定和其他代理程式相關設定。

C # 安全性代理程式會使用多個設定檔：

- **General.config**代理程式的相關設定。
- **Authentication.config** -驗證相關設定（包括驗證詳細資料）。
- **SecurityIotInterface.config** IoT 相關設定。

設定檔包含預設設定。 驗證設定會在代理程式安裝期間填入，而當代理程式重新開機時，系統會對設定檔進行變更。

## <a name="configuration-file-location"></a>設定檔案位置

若為 Linux：

- 作業系統設定檔位於 `/var/ASCIoTAgent` 。

若為 Windows：

- 作業系統設定檔位於安全性代理程式的目錄中。

### <a name="generalconfig-configurations"></a>General.config 設定

| 設定名稱 | 可能值 | 詳細資料 |
|:-----------|:---------------|:--------|
| agentId | GUID | 代理程式唯一識別碼 |
| readRemoteConfigurationTimeout | TimeSpan | 從 IoT 中樞提取遠端設定的時間週期。 如果代理程式無法在指定的時間內提取設定，作業將會超時。|
| schedulerInterval | TimeSpan | 內部排程器間隔。 |
| producerInterval | TimeSpan | 事件產生者背景工作間隔。 |
| consumerInterval | TimeSpan | 事件取用者背景工作間隔。 |
| highPriorityQueueSizePercentage | 0 < 數位 < 1 | 高優先順序訊息專用的總快取部分。 |
| logLevel | 「關閉」、「嚴重」、「錯誤」、「警告」、「資訊」、「Debug」  | 等於和高於此嚴重性的記錄訊息會記錄到 [調試] 主控台（Linux 中的 Syslog）。 |
| fileLogLevel |  「關閉」、「嚴重」、「錯誤」、「警告」、「資訊」、「Debug」| 等於和高於此嚴重性的記錄訊息會記錄到檔案（Linux 中的 Syslog）。 |
| diagnosticVerbosityLevel | "None"、"Some"、"All"、 | 診斷事件的詳細資訊層級。 無-不傳送診斷事件，只會傳送具有高重要性的部分診斷事件，所有記錄也會當做診斷事件傳送。 |
| logFilePath | 檔案的路徑 | 如果 fileLogLevel > Off，記錄檔就會寫入這個檔案。 |
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
| deviceId | 字串 | 裝置的識別碼（如 Azure IoT 中樞中所註冊）。 || schedulerInterval | TimeSpan 字串 | 內部排程器間隔。 |
| gatewayHostname | 字串 | Azure Iot 中樞的主機名稱。 通常 <我的中樞>。 azure-devices.net |
| filePath | 字串-檔案的路徑 | 包含驗證密碼之檔案的路徑。|
| 類型 | "SymmetricKey"、"SelfSignedCertificate" | 用於驗證的使用者密碼。 如果使用者密碼是對稱金鑰，請選擇 [ *SymmetricKey* ]，如果密碼是自我簽署憑證，請選擇 [*自我簽署憑證*]。 |
| 身分識別 | 「DPS」、「模組」、「裝置」 | 驗證身分識別-如果驗證是透過 DPS、模組（如果使用模組認證進行驗證，則為），或在使用裝置認證進行驗證時為裝置。
| certificateLocationKind |  "LocalFile"、"Store" | LocalFile 如果憑證儲存在檔案中，則儲存憑證是否位於憑證存放區中。 |
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

## <a name="next-steps"></a>後續步驟

- 閱讀 IoT 服務的 Azure 資訊安全中心[總覽](overview.md)
- 深入瞭解 IoT[架構](architecture.md)的 Azure 資訊安全中心
- 啟用 IoT[服務](quickstart-onboard-iot-hub.md)的 Azure 資訊安全中心
- 閱讀 IoT 服務的 Azure 資訊安全中心[常見問題](resources-frequently-asked-questions.md)
- 了解如何存取[未經處理的安全性資料](how-to-security-data-access.md)
- 了解[建議](concept-recommendations.md)
- 瞭解安全性[警示](concept-security-alerts.md)
