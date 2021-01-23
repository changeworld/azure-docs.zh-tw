---
title: Azure 雲端服務 (傳統) NetworkTrafficRules 架構 |Microsoft Docs
description: 瞭解 NetworkTrafficRules，這會限制可存取角色內部端點的角色。 它會結合服務定義檔中的角色。
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 2c8ab53068b71652d03d03bf79a224fe5e34dff3
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98739763"
---
# <a name="azure-cloud-services-classic-definition-networktrafficrules-schema"></a>Azure 雲端服務 (傳統) 定義 NetworkTrafficRules 架構

> [!IMPORTANT]
> [Azure 雲端服務 (延伸支援) ](../cloud-services-extended-support/overview.md) 是 Azure 雲端服務產品的新 Azure Resource Manager 型部署模型。透過這種變更，在以 Azure Service Manager 為基礎的部署模型上執行的 Azure 雲端服務，已重新命名為雲端服務 (傳統) ，而且所有新的部署都應該使用 [雲端服務 (延伸支援) ](../cloud-services-extended-support/overview.md)。

`NetworkTrafficRules` 節點是服務定義檔中的選擇性元素，它指定角色如何彼此通訊。 它會限制哪些角色可以存取特定角色的內部端點。 `NetworkTrafficRules` 不是獨立元素；它會與服務定義檔中的兩個或多個角色結合。

服務定義檔的預設副檔名為 .csdef。

> [!NOTE]
>  在使用 Azure SDK 1.3 版或更新版本時，才能使用 `NetworkTrafficRules` 節點。

## <a name="basic-service-definition-schema-for-the-network-traffic-rules"></a>網路流量規則的基本服務定義結構描述
包含網路流量定義之服務定義檔的基本格式如下所示。

```xml
<ServiceDefinition …>
   <NetworkTrafficRules>
      <OnlyAllowTrafficTo>
         <Destinations>
            <RoleEndpoint endpointName="<name-of-the-endpoint>" roleName="<name-of-the-role-containing-the-endpoint>"/>
         </Destinations>
         <AllowAllTraffic/>
         <WhenSource matches="[AnyRule]">
            <FromRole roleName="<name-of-the-role-to-allow-traffic-from>"/>
         </WhenSource>
      </OnlyAllowTrafficTo>
   </NetworkTrafficRules>
</ServiceDefinition>
```

## <a name="schema-elements"></a>結構描述元素
服務定義檔的 `NetworkTrafficRules` 節點包含下列元素，本主題的後續幾節會有這些元素的詳細說明：

[NetworkTrafficRules 元素](#NetworkTrafficRules)

[OnlyAllowTrafficTo 元素](#OnlyAllowTrafficTo)

[目的地元素](#Destinations)

[RoleEndpoint 元素](#RoleEndpoint)

AllowAllTraffic 元素

[WhenSource 元素](#WhenSource)

[FromRole 元素](#FromRole)

##  <a name="networktrafficrules-element"></a><a name="NetworkTrafficRules"></a> NetworkTrafficRules 元素
`NetworkTrafficRules` 元素會指定哪些角色可以與另一個角色上的哪個端點進行通訊。 服務可以包含一個 `NetworkTrafficRules` 定義。

##  <a name="onlyallowtrafficto-element"></a><a name="OnlyAllowTrafficTo"></a> OnlyAllowTrafficTo 元素
`OnlyAllowTrafficTo` 元素描述目的地端點和可與其通訊之角色的集合。 您可以指定多個 `OnlyAllowTrafficTo` 節點。

##  <a name="destinations-element"></a><a name="Destinations"></a> Destinations 元素
`Destinations` 元素描述可與之通訊的 RoleEndpoints 集合。

##  <a name="roleendpoint-element"></a><a name="RoleEndpoint"></a> RoleEndpoint 元素
`RoleEndpoint` 元素描述允許與其通訊之角色上的端點。 如果角色上有多個端點，您可以指定多個 `RoleEndpoint` 元素。

| 屬性      | 類型     | 描述 |
| -------------- | -------- | ----------- |
| `endpointName` | `string` | 必要。 要允許流量的端點名稱。|
| `roleName`     | `string` | 必要。 要允許與其通訊的 Web 角色名稱。|

## <a name="allowalltraffic-element"></a>AllowAllTraffic 元素
`AllowAllTraffic` 元素是一個規則，允許所有角色與 `Destinations` 節點中定義的端點通訊。

##  <a name="whensource-element"></a><a name="WhenSource"></a> WhenSource 元素
`WhenSource` 元素描述可以與 `Destinations` 節點中定義的端點通訊之角色集合。

| 屬性 | 類型     | 描述 |
| --------- | -------- | ----------- |
| `matches` | `string` | 必要。 指定允許通訊時要套用的規則。 唯一支援的值為 `AnyRule`。|
  
##  <a name="fromrole-element"></a><a name="FromRole"></a> FromRole 元素
`FromRole` 元素指定可以與 `Destinations` 節點中定義的端點通訊之角色。 如果有多個角色可以與端點通訊，您可以指定多個 `FromRole` 元素。

| 屬性  | 類型     | 描述 |
| ---------- | -------- | ----------- |
| `roleName` | `string` | 必要。 要允許其進行通訊的角色名稱。|

## <a name="see-also"></a>另請參閱
[雲端服務 (傳統) 定義結構描述](schema-csdef-file.md)




