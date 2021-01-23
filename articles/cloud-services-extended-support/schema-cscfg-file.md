---
title: Azure 雲端服務 (延伸支援) 定義架構 ( .cscfg 檔案) |Microsoft Docs
description: '雲端服務的定義架構相關資訊 (延伸支援) '
ms.topic: article
ms.service: cloud-services-extended-support
ms.date: 10/14/2020
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 3aac5ca8d146f0cd74f483846d22fdf0052f86f2
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744295"
---
# <a name="azure-cloud-services-extended-support-config-schema-cscfg-file"></a>Azure 雲端服務 (延伸支援) config schema (.cscfg 檔案) 

服務組態檔會指定要為服務中每個角色部署的角色執行個體數目、任何組態設定的值，以及與角色相關聯之任何憑證的指紋。 如果服務是虛擬網路的一部分，必須在服務組態檔以及虛擬網路組態檔中提供網路的組態資訊。 服務設定檔的預設副檔名為 .cscfg。

雲端服務會描述服務模型 [ (延伸支援) 定義架構](schema-csdef-file.md)。

根據預設，Azure 診斷組態結構描述檔案是安裝於 `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` 目錄中。 請使用安裝的 [Azure SDK](https://azure.microsoft.com/downloads/) 版本來取代 `<version>`。

如需有關在服務中設定角色的詳細資訊，請參閱[什麼是雲端服務模型](../cloud-services/cloud-services-model-and-package.md)。

## <a name="basic-service-configuration-schema"></a>基本服務設定架構
服務組態檔的基本格式如下所示。

```xml
<ServiceConfiguration serviceName="<service-name>" osFamily="<osfamily-number>" osVersion="<os-version>" schemaVersion="<schema-version>">

  <Role …>
    …
  </Role>

  <NetworkConfiguration>
    …
  </NetworkConfiguration>

</ServiceConfiguration>
```

## <a name="schema-definitions"></a>結構描述定義
下列主題說明 `ServiceConfiguration` 元素的結構描述：

- [角色結構描述](schema-cscfg-role.md)
- [NetworkConfiguration 結構描述](schema-cscfg-networkconfiguration.md)

## <a name="service-configuration-namespace"></a>服務設定命名空間
適用於服務組態檔的 XML 命名空間如下：`http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration`。

##  <a name="serviceconfiguration-element"></a><a name="ServiceConfiguration"></a> Serviceconfiguration.cscfg 元素
`ServiceConfiguration` 元素是服務組態檔的最上層元素。

下表說明 `ServiceConfiguration` 元素的屬性。 所有屬性值是字串類型。

| 屬性 | 描述 |
| --------- | ----------- |
|serviceName|必要。 雲端服務的名稱。 這裡指定的名稱必須符合在服務定義檔中指定的名稱。|
|osFamily|選擇性。 指定將在雲端服務中的角色實例上執行的來賓 OS。 如需支援之客體 OS 版本的詳細資訊，請參閱 [Azure 客體 OS 版本與 SDK 相容性比較表](../cloud-services/cloud-services-guestos-update-matrix.md)。<br /><br /> 如果您未包含 `osFamily` 值，且未將 `osVersion` 屬性設定為特定客體 OS 版本，則會使用預設值 1。|
|osVersion|選擇性。 指定將在雲端服務中的角色實例上執行的來賓 OS 版本。 如需客體 OS 版本的詳細資訊，請參閱 [Azure 客體 OS 版本與 SDK 相容性比較表](../cloud-services/cloud-services-guestos-update-matrix.md)。<br /><br /> 您可以指定客體 OS 應自動升級為最新版本。 若要這樣做，請將 `osVersion` 屬性的值設定為 `*`。 當設定為 `*` 時，會使用指定 OS 系列的最新版本客體 OS 來部署角色執行個體，並且在新版本的客體 OS 發行時自動升級。<br /><br /> 若要手動指定特定版本，請使用 `Configuration String`，位於 [Azure 客體 OS 版本與 SDK 相容性比較表](../cloud-services/cloud-services-guestos-update-matrix.md)之 **未來、目前和轉換客體 OS 版本** 區段的資料表中。<br /><br /> `osVersion` 屬性的預設值為 `*`。|
|schemaVersion|選擇性。 指定服務組態結構描述的版本。 如果已並行安裝一個以上的 SDK 版本，結構描述版本就可讓 Visual Studio 選取要用於結構描述驗證的正確 SDK 工具。 如需結構描述和版本相容性的詳細資訊，請參閱 [Azure 客體 OS 版本與 SDK 相容性比較表](../cloud-services/cloud-services-guestos-update-matrix.md)|

服務組態檔必須包含一個 `ServiceConfiguration` 元素。 `ServiceConfiguration` 元素可能包含任意數目的 `Role` 元素，以及零或 1 個 `NetworkConfiguration` 元素。

## <a name="see-also"></a>另請參閱

[Azure 雲端服務 (延伸支援) 定義架構 (已設定檔案) ](schema-csdef-file.md)