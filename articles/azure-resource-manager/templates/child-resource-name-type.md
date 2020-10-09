---
title: 範本中的子資源
description: 描述如何在 Azure Resource Manager 範本中設定子資源的名稱和類型。
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: 3a69829e674925982c618807f49433a033d8c5f9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "80743844"
---
# <a name="set-name-and-type-for-child-resources"></a>設定子資源的名稱和類型

子資源是只存在於另一個資源內容中的資源。 例如， [虛擬機器延伸](/azure/templates/microsoft.compute/2019-03-01/virtualmachines/extensions) 模組無法在沒有 [虛擬機器](/azure/templates/microsoft.compute/2019-03-01/virtualmachines)的情況下存在。 擴充功能資源是虛擬機器的子系。

在 Resource Manager 範本中，您可以在父資源或父資源外部指定子資源。 下列範例顯示父資源的 resources 屬性中所包含的子資源。

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

下一個範例會顯示父資源外部的子資源。 如果父資源不是部署在相同的範本中，或者想要使用 [複製](copy-resources.md) 來建立多個子資源，您可以使用此方法。

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

您為資源名稱和類型提供的值會根據子資源是在父資源內部或外部定義而有所不同。

## <a name="within-parent-resource"></a>在父資源內

在父資源類型中定義時，您會將類型和名稱值格式化為單一單字，而不使用斜線。

```json
"type": "{child-resource-type}",
"name": "{child-resource-name}",
```

下列範例顯示虛擬網路和子網。 請注意，子網會包含在虛擬網路的資源陣列中。 名稱會設定為 **Subnet1** ，且類型會設定為 **子網**。 子資源會標示為相依于父資源，因為父資源必須存在才能部署子資源。

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

完整的資源類型仍為 [ **Microsoft]/[virtualNetworks]/[子網**]。 您未提供 **Microsoft/virtualNetworks/** ，因為它是從父資源類型假設。

子資源名稱設定為 **Subnet1** ，但完整名稱包含父系名稱。 您不會提供 **VNet1** ，因為它是從父資源所假設。

## <a name="outside-parent-resource"></a>外部父資源

在父資源外部定義時，您會將類型格式化，並使用斜線來包含父類型和名稱。

```json
"type": "{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}",
"name": "{parent-resource-name}/{child-resource-name}",
```

下列範例會顯示在根層級定義的虛擬網路和子網。 請注意，子網未包含在虛擬網路的資源陣列中。 名稱會設定為 **VNet1/Subnet1** ，而類型會設定為 [ **Microsoft]/[virtualNetworks]/[子網**]。 子資源會標示為相依于父資源，因為父資源必須存在才能部署子資源。

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

* 若要瞭解參考資源時資源名稱的格式，請參閱 [reference](template-functions-resource.md#reference)函式。
