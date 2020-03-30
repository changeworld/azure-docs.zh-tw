---
title: 瞭解 Azure 安全中心，用於 IoT 安全代理本地設定檔，用於 C# |微軟文檔
description: 詳細瞭解適用于 IoT 安全服務的 Azure 安全中心、C# 的安全代理本地設定檔。
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
ms.openlocfilehash: 0172ada68ffa652fb0c301c89238beca4f4ce2f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74664183"
---
# <a name="understanding-the-local-configuration-file-c-agent"></a>瞭解本地設定檔 （C# 代理）


IoT 安全代理的 Azure 安全中心使用本地設定檔中的配置。

當代理啟動時，安全代理讀取設定檔一次。 在本地設定檔中找到的配置同時包含身份驗證配置和其他代理相關配置。

C# 安全代理使用多個設定檔：

- **常規.配置**- 代理相關配置。
- **身份驗證.config** - 身份驗證相關配置（包括身份驗證詳細資訊）。
- **安全 Iot 介面.配置**- IoT 相關配置。

設定檔包含預設配置。 身份驗證配置在代理安裝期間填充，並在重新開機代理時對設定檔進行更改。 

## <a name="configuration-file-location"></a>設定檔位置
若為 Linux：
- 作業系統設定檔位於`/var/ASCIoTAgent`中。

若為 Windows：
- 作業系統設定檔位於安全代理的目錄中。 

### <a name="generalconfig-configurations"></a>常規.配置配置

| 設定名稱 | 可能值 | 詳細資料 | 
|:-----------|:---------------|:--------|
| 代理 Id | GUID | 代理唯一識別碼 |
| 讀取遠端配置超時 | TimeSpan | 從 IoT 中心獲取遠端配置的時間段。 如果代理無法在指定時間內獲取配置，則操作將超時。|
| 計畫時間間隔 | TimeSpan | 內部計畫程式間隔。 |
| 生產者間隔 | TimeSpan | 事件產生器輔助角色間隔。 |
| 消費者間隔 | TimeSpan | 事件消費者輔助角色間隔。 |
| 高優先順序佇列大小百分比 | 0 < 數位 < 1 | 專用於高優先順序消息的總緩存部分。 |
| logLevel | "關閉"、"致命"、"錯誤"、"警告"、"資訊"、"調試"  | 將等於且高於此嚴重性的錯誤日誌記錄到調試主控台（Linux 中的 Syslog）。 |
| 檔日誌級別 |  "關閉"、"致命"、"錯誤"、"警告"、"資訊"、"調試"| 相同且高於此嚴重性的日誌消息將記錄到檔中（Linux 中的 Syslog）。 |
| 診斷詳細級別 | "無"，"一些"，"所有" | 診斷事件的風險級別。 無 - 未發送診斷事件，某些 - 僅發送具有高重要性的診斷事件，所有 - 所有日誌也作為診斷事件發送。 |
| 日誌檔路徑 | 檔路徑 | 如果 fileLogLevel >關閉，日誌將寫入此檔。 |
| 預設事件優先順序 | "高"，"低"，"關" | 預設事件優先順序。 |

### <a name="generalconfig-example"></a>常規.config 示例
```XML
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

### <a name="authenticationconfig"></a>身份驗證. 配置

| 組態名稱 | 可能值 | 詳細資料 | 
|:-----------|:---------------|:--------|
| moduleName | 字串 | 安全模組標識的名稱。 此名稱必須與設備中的模組標識名稱對應。 |
| deviceId | 字串 | 設備的 ID（在 Azure IoT 中心註冊）。 || 計畫時間間隔 | 時間跨度字串 | 內部計畫程式間隔。 |
| 閘道主機名稱 | 字串 | Azure Iot 中心的主機名稱。 通常<我的集線器>.azure-devices.net |
| filePath | 字串 - 檔路徑 | 包含身份驗證金鑰的檔的路徑。|
| type | "對稱金鑰"，"自簽章憑證" | 用於身份驗證的使用者金鑰。 如果使用者金鑰是對稱金鑰，請選擇*SymmetricKey，* 如果密碼是自簽章憑證，請選擇*自簽章憑證*。 |
| 身分識別 | "DPS"、"模組"、"設備" | 身份驗證標識 - 通過 DPS 進行身份驗證的 DPS、使用模組憑據進行身份驗證的模組或使用設備憑據進行身份驗證的設備。
| 證書位置金德 |  "本地檔"，"存儲" | 如果憑證存放區在檔中，則本地檔，如果證書位於憑證存放區中，則存儲。 |
| idScope | 字串 | DPS 的 ID 範圍 |
| 註冊 Id | 字串  | DPS 設備註冊 ID。 |
|

### <a name="authenticationconfig-example"></a>身份驗證.config 示例
```XML
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
### <a name="securityiotinterfaceconfig"></a>安全Iot介面.配置

| 設定名稱 | 可能值 | 詳細資料 | 
|:-----------|:---------------|:--------|
| 傳輸類型 | "安普克""Mqtt" | IoT 集線器傳輸類型。 |
|

### <a name="securityiotinterfaceconfig-example"></a>安全Iot介面.config示例
```XML
<ExternalInterface>
  <add key="facadeType"  value="Microsoft.Azure.Security.IoT.Agent.Common.SecurityIoTHubInterface, Security.Common" />
  <add key="transportType" value="Amqp"/>
</ExternalInterface>
```

## <a name="next-steps"></a>後續步驟
- 閱讀 Azure 安全中心，瞭解 IoT 服務[概述](overview.md)
- 瞭解有關 IoT[體系結構](architecture.md)的 Azure 安全中心
- 啟用適用于 IoT[服務的](quickstart-onboard-iot-hub.md)Azure 安全中心
- 閱讀 Azure 安全中心，瞭解 IoT 服務[常見問題解答](resources-frequently-asked-questions.md)
- 了解如何存取[未經處理的安全性資料](how-to-security-data-access.md)
- 了解[建議](concept-recommendations.md)
- 瞭解安全[警報](concept-security-alerts.md)