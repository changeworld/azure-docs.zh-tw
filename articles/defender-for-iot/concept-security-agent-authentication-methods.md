---
title: 安全性代理程式驗證方法
description: 瞭解使用 Defender for IoT 服務時可用的不同驗證方法。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 896baa70fb6e18165a025459a063e33d705261ba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90934470"
---
# <a name="security-agent-authentication-methods"></a>安全性代理程式驗證方法

本文說明您可以搭配 AzureIoTSecurity 代理程式用來向 IoT 中樞進行驗證的不同驗證方法。

針對 IoT 中樞內的 IoT 上線至 Defender 的每個裝置，都需要安全性模組。 若要驗證裝置，Defender for IoT 可以使用兩種方法的其中一種。 選擇最適合您現有 IoT 解決方案的方法。

> [!div class="checklist"]
> * SecurityModule 選項
> * 裝置選項

## <a name="authentication-methods"></a>驗證方法

AzureIoTSecurity 代理程式執行驗證的兩個方法：

- **SecurityModule** 驗證模式<br>
代理程式會使用與裝置身分識別無關的安全模組身分識別來進行驗證。
如果您希望安全性代理程式透過安全性模組使用專用的驗證方法，請使用此驗證類型 (對稱金鑰) 。

- **裝置** 驗證模式<br>
在此方法中，安全性代理程式會先使用裝置身分識別進行驗證。 在初始驗證之後，Defender for IoT 代理程式會使用 REST API 搭配裝置的驗證資料來執行對 IoT 中樞的 **REST** 呼叫。 然後，適用于 IoT 的 Defender 代理程式會向 IoT 中樞要求安全性模組驗證方法和資料。 在最後一個步驟中，適用于 IoT 的 Defender 代理程式會對 Defender for IoT 模組執行驗證。

如果您希望安全性代理程式重複使用現有的裝置驗證方法 (自我簽署憑證或對稱金鑰) ，請使用此驗證類型。

若要瞭解如何設定，請參閱 [安全性代理程式安裝參數](#security-agent-installation-parameters) 。

## <a name="authentication-methods-known-limitations"></a>驗證方法的已知限制

- **SecurityModule** authentication 模式僅支援對稱金鑰驗證。
- **裝置**驗證模式不支援 CA-Signed 憑證。

## <a name="security-agent-installation-parameters"></a>安全性代理程式安裝參數

[部署安全性代理程式](how-to-deploy-agent.md)時，必須以引數的形式提供驗證詳細資料。
這些引數記載于下表。

|Linux 參數名稱 | Windows 參數名稱 | 速記參數 |描述|選項。|
|---------------------|---------------|---------|---------------|---------------|
|驗證-身分識別|AuthenticationIdentity|aui|驗證身分識別| **SecurityModule** 或 **裝置**|
|authentication-method|AuthenticationMethod|aum|驗證方法|**SymmetricKey** 或 **new-selfsignedcertificate**|
|file-path|FilePath|f|包含憑證或對稱金鑰之檔案的絕對完整路徑| |
|主機名稱|HostName|Hn|IoT 中樞的 FQDN|範例： ContosoIotHub.azure-devices.net|
|裝置識別碼|DeviceId|地|裝置識別碼|範例： MyDevice1|
|憑證-位置種類|CertificateLocationKind|Cl|憑證儲存位置|**LocalFile** 或 **Store**|
|

使用「安裝安全性代理程式」腳本時，會自動執行下列設定。 若要手動編輯安全性代理程式驗證，請編輯設定檔。

## <a name="change-authentication-method-after-deployment"></a>部署後變更驗證方法

使用安裝腳本部署安全性代理程式時，會自動建立設定檔。

若要在部署後變更驗證方法，則需要手動編輯設定檔。

### <a name="c-based-security-agent"></a>以 c # 為基礎的安全性代理程式

使用下列參數編輯 _Authentication.config_ ：

```xml
<Authentication>
  <add key="deviceId" value=""/>
  <add key="gatewayHostname" value=""/>
  <add key="filePath" value=""/>
  <add key="type" value=""/>
  <add key="identity" value=""/>
  <add key="certificateLocationKind" value="" />
</Authentication>
```

### <a name="c-based-security-agent"></a>以 C 為基礎的安全性代理程式

使用下列參數編輯 _LocalConfiguration.js_ ：

```json
"Authentication" : {
    "Identity" : "",
    "AuthenticationMethod" : "",
    "FilePath" : "",
    "DeviceId" : "",
    "HostName" : ""
}
```

## <a name="see-also"></a>另請參閱

- [安全性代理程式總覽](security-agent-architecture.md)
- [部署安全性代理程式](how-to-deploy-agent.md)
- [存取未經處理的安全性資料](how-to-security-data-access.md)
