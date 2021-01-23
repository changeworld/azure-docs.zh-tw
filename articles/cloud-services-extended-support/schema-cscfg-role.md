---
title: Azure 雲端服務 (延伸支援) 角色架構 |Microsoft Docs
description: '雲端服務的角色架構相關資訊 (延伸支援) '
ms.topic: article
ms.service: cloud-services-extended-support
ms.date: 10/14/2020
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 2567f5bb817a34f6274d5e265a266d67a9c81413
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744301"
---
# <a name="azure-cloud-services-extended-support-config-role-schema"></a>Azure 雲端服務 (延伸支援) config 角色架構

組態檔的 `Role` 元素會指定要為服務中每個角色部署的角色執行個體數目、任何組態設定的值，以及與角色相關聯之任何憑證的指紋。

如需有關 Azure 服務設定架構的詳細資訊，請參閱 [雲端服務 (延伸支援) 設定架構](schema-cscfg-file.md)。 如需有關 Azure 服務定義架構的詳細資訊，請參閱 [雲端服務 (延伸支援) 定義架構](schema-csdef-file.md)。

##  <a name="role-element"></a><a name="Role"></a> role 元素
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
[雲端服務 () 設定架構的延伸支援](schema-cscfg-file.md)。