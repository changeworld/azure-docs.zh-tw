---
title: IoT® Azure 安全中心的身份驗證方法*微軟文檔
description: 瞭解使用 Azure IoT 服務安全中心時可用的不同身份驗證方法。
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
ms.openlocfilehash: 16f7f91e02d118d9f9a295ebb79a6cd0187dd9fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "68596462"
---
# <a name="security-agent-authentication-methods"></a>安全代理身份驗證方法 

本文介紹了與 AzureIoT 安全代理一起使用的不同身份驗證方法，以便對 IoT 中心進行身份驗證。

對於在 IoT 中心中連接到 Azure IoT IoT 安全中心的每個設備，都需要一個安全模組。 要對設備進行身份驗證，IoT 的 Azure 安全中心可以使用兩種方法之一。 選擇最適合現有 IoT 解決方案的方法。 

> [!div class="checklist"]
> * 安全模組選項
> * 設備選項

## <a name="authentication-methods"></a>驗證方法

AzureIoT 安全代理執行身份驗證的兩種方法：

 - **安全模組**身份驗證模式<br>
   代理使用獨立于設備標識的安全模組標識進行身份驗證。
   如果您希望安全代理通過安全模組（僅限對稱金鑰）使用專用身份驗證方法，請使用此身份驗證類型。
        
 - **設備**身份驗證模式<br>
    在此方法中，安全代理首先使用設備標識進行身份驗證。 初始身份驗證後，IoT 代理的 Azure 安全中心使用 REST API 對 IoT 中心執行**REST**調用，並帶有設備的身份驗證資料。 然後，IoT 代理的 Azure 安全中心從 IoT 中心請求安全模組身份驗證方法和資料。 在最後一步中，IoT 代理的 Azure 安全中心對 IoT 模組的 Azure 安全中心執行身份驗證。
    
    如果希望安全代理重用現有設備身份驗證方法（自簽章憑證或對稱金鑰），請使用此身份驗證類型。 

請參閱[安全代理安裝參數](#security-agent-installation-parameters)，瞭解如何配置。
                                
## <a name="authentication-methods-known-limitations"></a>身份驗證方法已知限制

- **安全模組**身份驗證模式僅支援對稱金鑰身份驗證。
- **設備**身份驗證模式不支援 CA 簽章憑證。  

## <a name="security-agent-installation-parameters"></a>安全代理安裝參數

[部署安全代理時](how-to-deploy-agent.md)，身份驗證詳細資訊必須作為參數提供。
下表中記錄了這些參數。


|Linux 參數名稱 | 視窗參數名稱 | 速記參數 |描述|選項。|
|---------------------|---------------|---------|---------------|---------------|
|身份驗證標識|身份驗證標識|艾伊|身份驗證標識| **安全模組**或**設備**|
|authentication-method|身份驗證方法|阿姆|驗證方法|**對稱金鑰**或**自簽章憑證**|
|檔路徑|FilePath|f|包含證書或對稱鍵的檔的絕對完整路徑| |
|主機名稱|HostName|Hn|物聯網中心 FQDN|示例：ContosoIotHub.azure-devices.net|
|設備 id|deviceId|地|裝置識別碼|示例：我的裝置1|
|證書位置類型|證書位置金德|Cl|憑證存放區位置|**本地檔**或**存儲**|
|


使用安裝安全代理腳本時，將自動執行以下配置。 要手動編輯安全代理身份驗證，請編輯設定檔。 

## <a name="change-authentication-method-after-deployment"></a>部署後更改身份驗證方法

使用安裝腳本部署安全代理時，將自動創建設定檔。

要在部署後更改身份驗證方法，需要手動編輯設定檔。


### <a name="c-based-security-agent"></a>基於 C# 的安全代理

編輯_身份驗證.配置_與以下參數：

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

### <a name="c-based-security-agent"></a>基於 C 的安全代理

使用以下參數編輯_本地配置.json：_

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
- [安全代理概述](security-agent-architecture.md)
- [部署安全代理](how-to-deploy-agent.md)
- [存取未經處理的安全性資料](how-to-security-data-access.md)
