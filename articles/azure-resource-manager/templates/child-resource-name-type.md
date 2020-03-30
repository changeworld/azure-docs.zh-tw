---
title: 範本中的子資源
description: 介紹如何在 Azure 資源管理器範本中設置子資源的名稱和類型。
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: 6de2b476fe19a057a62e4a54963dd8fde0d11579
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77207888"
---
# <a name="set-name-and-type-for-child-resources"></a>為子資源設置名稱和類型

子資源是僅存在於另一個資源上下文中的資源。 例如，沒有[虛擬機器](/azure/templates/microsoft.compute/2019-03-01/virtualmachines)，[虛擬機器擴展](/azure/templates/microsoft.compute/2019-03-01/virtualmachines/extensions)名就無法存在。 擴展資源是虛擬機器的子級。

在"資源管理器"範本中，可以在父資源內或父資源外部指定子資源。 下面的示例顯示父資源的資源屬性中包含的子資源。

```json
"resources": [
  {
    <parent-resource>
    "resources": [
      <child-resource>
    ]
  }
]
```

下一個示例顯示父資源外部的子資源。 如果父資源未部署在同一範本中，或者想要使用[副本](copy-resources.md)創建多個子資源，則可以使用此方法。

```json
"resources": [
  {
    <parent-resource>
  },
  {
    <child-resource>
  }
]
```

您為資源名稱和類型提供的值因數資源是父資源內部定義還是外部而有所不同。

## <a name="within-parent-resource"></a>在父資源中

在父資源類型中定義時，將類型和名稱值格式化為單個單詞，而不進行斜杠。

```json
"type": "{child-resource-type}",
"name": "{child-resource-name}",
```

下面的示例顯示了虛擬網路和子網。 請注意，子網包含在虛擬網路的資源陣列中。 名稱設置為**子網1，** 類型設置為**子網**。 子資源被標記為依賴于父資源，因為父資源必須存在，然後才能部署子資源。

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2018-10-01",
    "name": "VNet1",
    "location": "[parameters('location')]",
    "properties": {
      "addressSpace": {
        "addressPrefixes": [
          "10.0.0.0/16"
        ]
      }
    },
    "resources": [
      {
        "type": "subnets",
        "apiVersion": "2018-10-01",
        "name": "Subnet1",
        "location": "[parameters('location')]",
        "dependsOn": [
          "VNet1"
        ],
        "properties": {
          "addressPrefix": "10.0.0.0/24"
        }
      }
    ]
  }
]
```

完整的資源類型仍然是**微軟.網路/虛擬網路/子網**。 您不提供**Microsoft.Network/虛擬網路/** 因為它是從父資源類型假定的。

子資源名稱設置為**子網1，** 但全名包括父名稱。 您不提供**VNet1，** 因為它是從父資源假定的。

## <a name="outside-parent-resource"></a>外部父資源

在父資源外部定義時，將類型格式化，並採用斜杠來包括父類型和名稱。

```json
"type": "{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}",
"name": "{parent-resource-name}/{child-resource-name}",
```

下面的示例顯示了在根級別定義的虛擬網路和子網。 請注意，子網不包括在虛擬網路的資源陣列中。 名稱設置為**VNet1/子網1，** 類型設置為**Microsoft.Network/虛擬網路/子網**。 子資源被標記為依賴于父資源，因為父資源必須存在，然後才能部署子資源。

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2018-10-01",
    "name": "VNet1",
    "location": "[parameters('location')]",
    "properties": {
      "addressSpace": {
        "addressPrefixes": [
          "10.0.0.0/16"
        ]
      }
    }
  },
  {
    "type": "Microsoft.Network/virtualNetworks/subnets",
    "apiVersion": "2018-10-01",
    "location": "[parameters('location')]",
    "name": "VNet1/Subnet1",
    "dependsOn": [
      "VNet1"
    ],
    "properties": {
      "addressPrefix": "10.0.0.0/24"
    }
  }
]
```

## <a name="next-steps"></a>後續步驟

* 若要了解如何建立 Azure 資源管理員範本，請參閱 [撰寫範本](template-syntax.md)。

* 要瞭解引用資源時資源名稱的格式，請參閱[引用函數](template-functions-resource.md#reference)。
