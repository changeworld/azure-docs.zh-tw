---
title: 安全性代理程式驗證方法
description: 瞭解使用 IoT 服務的 Azure 資訊安全中心時可用的不同驗證方法。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 10b38f20-b755-48cc-8a88-69828c17a108
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 0d9d51292c3cae9634af917819b558cdfd2fa04b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81311523"
---
# <a name="security-agent-authentication-methods"></a>安全性代理程式驗證方法

本文說明您可以與 AzureIoTSecurity 代理程式搭配使用的不同驗證方法，以 IoT 中樞進行驗證。

針對 IoT 中樞中的 IoT Azure 資訊安全中心的每個裝置上架，需要安全性模組。 若要驗證裝置，適用于 IoT 的 Azure 資訊安全中心可以使用兩種方法的其中一種。 選擇最適用于您現有 IoT 解決方案的方法。

> [!div class="checklist"]
> * SecurityModule 選項
> * 裝置選項

## <a name="authentication-methods"></a>驗證方法

AzureIoTSecurity 代理程式執行驗證的兩種方法：

- **SecurityModule**驗證模式<br>
代理程式是使用與裝置身分識別無關的安全性模組識別進行驗證。
如果您想要讓安全性代理程式透過安全性模組使用專用的驗證方法（僅限對稱金鑰），請使用此驗證類型。

- **裝置**驗證模式<br>
在此方法中，安全性代理程式會先使用裝置身分識別進行驗證。 初始驗證之後，IoT 代理程式的 Azure 資訊安全中心會使用 REST API 搭配裝置的驗證資料，對 IoT 中樞執行**REST**呼叫。 IoT 代理程式的 Azure 資訊安全中心接著會向 IoT 中樞要求安全性模組驗證方法和資料。 在最後一個步驟中，IoT 代理程式的 Azure 資訊安全中心會針對 IoT 模組的 Azure 資訊安全中心執行驗證。

如果您想要讓安全性代理程式重複使用現有的裝置驗證方法（自我簽署的憑證或對稱金鑰），請使用此驗證類型。

若要瞭解如何設定，請參閱[安全性代理程式安裝參數](#security-agent-installation-parameters)。

## <a name="authentication-methods-known-limitations"></a>驗證方法的已知限制

- **SecurityModule**驗證模式只支援對稱金鑰驗證。
- **裝置**驗證模式不支援 CA 簽署的憑證。

## <a name="security-agent-installation-parameters"></a>安全性代理程式安裝參數

[部署安全性代理程式](how-to-deploy-agent.md)時，必須提供驗證詳細資料做為引數。
這些引數記載于下表。

|Linux 參數名稱 | Windows 參數名稱 | 速記參數 |描述|選項。|
|---------------------|---------------|---------|---------------|---------------|
|驗證-身分識別|AuthenticationIdentity|aui|驗證身分識別| **SecurityModule**或**裝置**|
|authentication-method|AuthenticationMethod|aum|驗證方法|**SymmetricKey**或**SelfSignedCertificate**|
|檔案-路徑|FilePath|f|包含憑證或對稱金鑰之檔案的絕對完整路徑| |
|主機名稱|HostName|hn|IoT 中樞的 FQDN|範例： ContosoIotHub.azure-devices.net|
|裝置識別碼|DeviceId|di|裝置識別碼|範例： MyDevice1|
|憑證-位置類型|CertificateLocationKind|cl|憑證儲存位置|**LocalFile**或**Store**|
|

使用安裝安全性代理程式腳本時，會自動執行下列設定。 若要手動編輯安全性代理程式驗證，請編輯設定檔。

## <a name="change-authentication-method-after-deployment"></a>部署後變更驗證方法

使用安裝腳本部署安全性代理程式時，會自動建立設定檔。

若要在部署後變更驗證方法，則需要手動編輯設定檔。

### <a name="c-based-security-agent"></a>以 c # 為基礎的安全性代理程式

使用下列參數編輯_Authentication.config_ ：

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

使用下列參數編輯_的LocalConfiguration.js_ ：

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
