---
title: 公共Ip位址Combo UI元素
description: 描述 Azure 入口網站的 Microsoft.Network.PublicIpAddressCombo UI 元素。
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 0393673663df8f3ca580ff34e16bee910b955f8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651913"
---
# <a name="microsoftnetworkpublicipaddresscombo-ui-element"></a>Microsoft.Network.PublicIpAddressCombo UI 元素

選取新的或現有公用 IP 位址的控制項群組。

## <a name="ui-sample"></a>UI 範例

![Microsoft.Network.PublicIpAddressCombo](./media/managed-application-elements/microsoft.network.publicipaddresscombo.png)

- 如果使用者的公用 IP 位址選擇 [無]，網域名稱標籤文字方塊就會加以隱藏。
- 如果使用者選擇現有的公用 IP 位址，網域名稱標籤文字方塊就會加以停用。 其值為所選取 IP 位址的網域名稱標籤。
- 網域名稱尾碼 (例如，westus.cloudapp.azure.com) 會根據選取的位置自動更新。

## <a name="schema"></a>結構描述

```json
{
  "name": "element1",
  "type": "Microsoft.Network.PublicIpAddressCombo",
  "label": {
    "publicIpAddress": "Public IP address",
    "domainNameLabel": "Domain name label"
  },
  "toolTip": {
    "publicIpAddress": "",
    "domainNameLabel": ""
  },
  "defaultValue": {
    "publicIpAddressName": "ip01",
    "domainNameLabel": "mydomain"
  },
  "constraints": {
    "required": {
      "domainNameLabel": true
    }
  },
  "options": {
    "hideNone": false,
    "hideDomainNameLabel": false,
    "hideExisting": false,
    "zone": 3
  },
  "visible": true
}
```

## <a name="sample-output"></a>範例輸出

如果使用者未選取公用 IP 位址，則控制項會傳回下列輸出︰

```json
{
  "newOrExistingOrNone": "none"
}
```

如果使用者選取全新或現有 IP 位址，則控制項會傳回下列輸出︰

```json
{
  "name": "ip01",
  "resourceGroup": "rg01",
  "domainNameLabel": "mydomain",
  "publicIPAllocationMethod": "Dynamic",
  "sku": "Basic",
  "newOrExistingOrNone": "new"
}
```

- 當`options.hideNone`指定為**true**`newOrExistingOrNone`時，將僅具有**新**值或**現有**值。
- 當`options.hideDomainNameLabel`指定為**true**`domainNameLabel`時，則未聲明。

## <a name="remarks"></a>備註

- 如果將 `constraints.required.domainNameLabel` 設為 **true**，使用者在建立新的公用 IP 位址時，就必須提供網域名稱標籤。 無法選取沒有標籤的現有公用 IP 位址。
- 如果將 `options.hideNone` 設為 **true**，公用 IP 位址的 [無]**** 選項就會加以隱藏。 預設值為 **false**。
- 如果將 `options.hideDomainNameLabel` 設為 **true**，網域名稱標籤的文字方塊就會加以隱藏。 預設值為 **false**。
- 如果 `options.hideExisting` 為 true，使用者就無法選擇現有的公用 IP 位址。 預設值為 **false**。
- 對於 `zone`，只能使用指定區域的公用 IP 位址或區域復原公用 IP 位址。

## <a name="next-steps"></a>後續步驟

* 如需建立 UI 定義的簡介，請參閱[開始使用 CreateUiDefinition](create-uidefinition-overview.md)。
* 如需 UI 元素中通用屬性的說明，請參閱 [CreateUiDefinition 元素](create-uidefinition-elements.md)。
