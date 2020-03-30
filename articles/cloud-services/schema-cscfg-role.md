---
title: Azure 雲端服務角色結構描述 | Microsoft Docs
description: 服務設定檔的角色元素指定要為每個角色、配置值和證書指紋部署多少個角色實例。
ms.custom: ''
ms.date: 12/07/2016
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 12
author: tgore03
ms.author: tagore
ms.openlocfilehash: b64f9d27e382a39b132593502fed32c565af473a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528416"
---
# <a name="azure-cloud-services-config-role-schema"></a>Azure 雲端服務設定角色結構描述

組態檔的 `Role` 元素會指定要為服務中每個角色部署的角色執行個體數目、任何組態設定的值，以及與角色相關聯之任何憑證的指紋。

如需關於 Azure 組態結構描述的詳細資訊，請參閱[雲端服務 (傳統) 組態結構描述](schema-cscfg-file.md)。 如需關於 Azure 服務定義結構描述的詳細資訊，請參閱[雲端服務 (傳統) 定義結構描述](schema-csdef-file.md)。

##  <a name="role-element"></a><a name="Role"></a> Role 元素
下列範例說明 `Role` 元素和其子元素。

```xml 
<ServiceConfiguration>
  <Role name="<role-name>" vmName="<vm-name>">
    <Instances count="<number-of-instances>"/>
    <ConfigurationSettings>
      <Setting name="<setting-name>" value="<setting-value>" />
    </ConfigurationSettings>
    <Certificates>
      <Certificate name="<certificate-name>" thumbprint="<certificate-thumbprint>" thumbprintAlgorithm="<algorithm>"/>
    </Certificates>
  </Role>
</ServiceConfiguration>
```

下表說明 `Role` 元素的屬性。

| 屬性 | 描述 |
| --------- | ----------- |
| NAME   | 必要。 指定角色的名稱。 名稱必須符合為服務定義檔中之角色提供的名稱。|
| vmName | 選擇性。 指定虛擬機器的 DNS 名稱。 名稱長度必須等於或小於 10 個字元。|

下表說明 `Role` 元素的子項目。

| 元素 | 描述 |
| ------- | ----------- |
| 執行個體 | 必要。 指定要為角色部署的執行個體數目。 執行個體數目是由 `count` 屬性的整數所定義。|
| 設定   | 選擇性。 指定角色設定集合中的設定名稱和值。 設定名稱是由 `name` 屬性的字串所定義，而設定值是由 `value` 屬性的字串所定義。|
| 憑證 | 選擇性。 指定名稱、指紋和要與角色相關聯之服務憑證的演算法。 憑證名稱是由 `name` 屬性的字串所定義。 憑證指紋是由十六進位數字的字串所定義，該字串針對 `thumbprint` 屬性不包含空格。 十六進位數字必須使用數字和大寫英文字元來表示。 憑證演算法是由 `thumbprintAlgorithm` 屬性的字串所定義。|

## <a name="see-also"></a>另請參閱
[雲端服務 (傳統) 組態結構描述](schema-cscfg-file.md)