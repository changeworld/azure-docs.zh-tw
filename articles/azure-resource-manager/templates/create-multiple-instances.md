---
title: 部署資源的多個實例
description: 使用「Azure 資源管理員」範本中的複製作業和陣列，並在部署資源時多次逐一執行。
ms.topic: conceptual
ms.date: 09/27/2019
ms.openlocfilehash: 0250f5ee64c91d8d75ad246271ab31324a2553f8
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76836924"
---
# <a name="resource-property-or-variable-iteration-in-azure-resource-manager-templates"></a>Azure Resource Manager 範本中的資源、屬性或變數反復專案

本文說明如何在您的 Azure Resource Manager 範本中，建立一個以上的資源、變數或屬性實例。 若要建立多個實例，請將 `copy` 物件新增至您的範本。

與資源搭配使用時，複製物件的格式如下：

```json
"copy": {
  "name": "<name-of-loop>",
  "count": <number-of-iterations>,
  "mode": "serial" <or> "parallel",
  "batchSize": <number-to-deploy-serially>
}
```

與變數或屬性搭配使用時，複製物件的格式如下：

```json
"copy": [
  {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "input": <values-for-the-property-or-variable>
  }
]
```

這兩個用法在本文中會有更詳細的說明。 如需教學課程，請參閱[教學課程：使用 Resource Manager 範本建立多個資源執行個體](template-tutorial-create-multiple-instances.md)。

若需要指定是否要部署資源，請參閱[條件元素](conditional-resource-deployment.md)。

## <a name="copy-limits"></a>複製限制

若要指定反覆運算次數，請提供 count 屬性的值。 計數不能超過800。

計數不可為負數。 如果您部署具有 Azure PowerShell 2.6 或更新版本的範本、Azure CLI 2.0.74 或更新版本，或 REST API **2019-05-10**版或更新版本，您可以將 count 設定為零。 舊版的 PowerShell、CLI 和 REST API 不支援 count 的零。

請小心使用[完整模式部署](deployment-modes.md)搭配 copy。 如果您使用完整模式重新部署至資源群組，則會刪除在解析複製迴圈後未在範本中指定的任何資源。

無論使用的是資源、變數或屬性，計數的限制都相同。

## <a name="resource-iteration"></a>資源反覆項目

如果您想要在部署中建立多個資源實例，請將 `copy` 元素新增至資源類型。 在 copy 元素中，指定反覆運算次數和這個迴圈的名稱。

要多次建立的資源會採用下列格式：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-01-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "copy": {
        "name": "storagecopy",
        "count": 3
      },
      "properties": {}
    }
  ],
  "outputs": {}
}
```

請注意，每個資源的名稱均包含 `copyIndex()` 函式，並會傳回目前的反覆項目迴圈。 `copyIndex()`是以零為基礎。 因此，下列範例：

```json
"name": "[concat('storage', copyIndex())]",
```

會建立這些名稱︰

* storage0
* storage1
* storage2.

若要位移索引值，您可以傳遞 copyIndex() 函式中的值。 反復專案的數目仍指定于 copy 元素中，但 copyIndex 的值是以指定的值位移。 因此，下列範例：

```json
"name": "[concat('storage', copyIndex(1))]",
```

會建立這些名稱︰

* storage1
* storage2
* storage3

使用陣列時，複製作業會有幫助，因為您可以逐一查看陣列中的每個項目。 使用陣列上的 `length` 函式指定反覆運算的計數，並使用 `copyIndex` 來擷取陣列中目前的索引。 因此，下列範例：

```json
"parameters": {
  "org": {
    "type": "array",
    "defaultValue": [
      "contoso",
      "fabrikam",
      "coho"
    ]
  }
},
"resources": [
  {
    "name": "[concat('storage', parameters('org')[copyIndex()])]",
    "copy": {
      "name": "storagecopy",
      "count": "[length(parameters('org'))]"
    },
    ...
  }
]
```

會建立這些名稱︰

* storagecontoso
* storagefabrikam
* storagecoho

根據預設，Resource Manager 會以平行方式建立資源。 除了範本中800資源的總限制以外，不會對平行部署的資源數目套用任何限制。 不保證資源會循序建立。

不過，建議您指定將資源部署在序列中。 例如，在更新生產環境時，您可以錯開更新，因此任何一次就只會更新特定數目。 若要以序列方式部署資源的多個執行個體，請將 `mode` 設定為 **serial** 並將 `batchSize` 設為一次要部署的執行個體數目。 透過序列模式，Resource Manager 會在迴圈中較早的執行個體上建立相依性，因此在前一批次完成之前，它不會啟動一個批次。

例如，若要以序列方式一次部署兩個儲存體帳戶，請使用：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-01-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "copy": {
        "name": "storagecopy",
        "count": 4,
        "mode": "serial",
        "batchSize": 2
      },
      "properties": {}
    }
  ],
  "outputs": {}
}
```

mode 屬性也接受**平行**，這是預設值。

如需搭配使用複製與嵌套範本的詳細資訊，請參閱[使用複製](linked-templates.md#using-copy)。

## <a name="property-iteration"></a>屬性反覆運算

若要在資源上為某個屬性建立多個值，請在 properties 元素中新增 `copy` 陣列。 此陣列包含物件，且每個物件具有下列屬性：

* name - 要建立數個值的屬性名稱
* count - 要建立的值數目。
* input - 包含要指派給屬性之值的物件

下列範例示範如何將 `copy` 套用至虛擬機器的 dataDisks 屬性：

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2017-03-30",
  "name": "examplevm",
  "properties": {
    "storageProfile": {
      "copy": [{
        "name": "dataDisks",
        "count": 3,
        "input": {
          "lun": "[copyIndex('dataDisks')]",
          "createOption": "Empty",
          "diskSizeGB": "1023"
        }
      }],
      ...
```

請注意，在屬性反覆運算內使用 `copyIndex` 時，您必須提供反覆運算的名稱。 搭配資源反覆運算使用時，您不必提供名稱。

> [!NOTE]
> 屬性反復專案也支援 offset 引數。 位移必須位於反復專案的名稱之後，例如 copyIndex （' dataDisks '，1）。
>

Resource Manager 會在部署期間展開 `copy` 陣列。 陣列名稱會變成屬性名稱。 輸入值會變成物件屬性。 已部署的範本會變成：

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2017-03-30",
  "properties": {
    "storageProfile": {
      "dataDisks": [
        {
          "lun": 0,
          "createOption": "Empty",
          "diskSizeGB": "1023"
        },
        {
          "lun": 1,
          "createOption": "Empty",
          "diskSizeGB": "1023"
        },
        {
          "lun": 2,
          "createOption": "Empty",
          "diskSizeGB": "1023"
        }
      ],
      ...
```

copy 元素為一個陣列，因此，您可以針對資源指定一個以上的屬性。 針對每個要建立的屬性新增物件。

```json
{
  "name": "string",
  "type": "Microsoft.Network/loadBalancers",
  "apiVersion": "2017-10-01",
  "properties": {
    "copy": [
      {
        "name": "loadBalancingRules",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      },
      {
        "name": "probes",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      }
    ]
  }
}
```

您可以一起使用資源和屬性反覆運算。 依名稱參考屬性反覆運算。

```json
{
  "type": "Microsoft.Network/virtualNetworks",
  "apiVersion": "2018-04-01",
  "name": "[concat(parameters('vnetname'), copyIndex())]",
  "copy":{
    "count": 2,
    "name": "vnetloop"
  },
  "location": "[resourceGroup().location]",
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "[parameters('addressPrefix')]"
      ]
    },
    "copy": [
      {
        "name": "subnets",
        "count": 2,
        "input": {
          "name": "[concat('subnet-', copyIndex('subnets'))]",
          "properties": {
            "addressPrefix": "[variables('subnetAddressPrefix')[copyIndex('subnets')]]"
          }
        }
      }
    ]
  }
}
```

## <a name="variable-iteration"></a>變數反覆項目

若要建立變數的多個執行個體，請在變數區段中使用 `copy` 屬性。 您可以建立一個由 `input` 屬性中的值建構的元素陣列。 您可以在變數中使用 `copy` 屬性，也可以在變數部分的最上層使用。 在變數反覆項目內使用 `copyIndex` 時，您必須提供反覆項目的名稱。

> [!NOTE]
> 變數反復專案也支援 offset 引數。 位移必須位於反復專案的名稱之後，例如 copyIndex （' diskNames '，1）。
>

如需建立字串值陣列的簡單範例，請參閱[複製陣列範本](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/copy-array/azuredeploy.json)。

下列範例示範使用動態建構的元素建立陣列變數的幾種不同方式。 它示範如何在變數內使用複製來建立物件和字串的陣列。 它也會示範如何在最上層使用複製來建立物件、字串和整數的陣列。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "disk-array-on-object": {
      "copy": [
        {
          "name": "disks",
          "count": 5,
          "input": {
            "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
            "diskSizeGB": "1",
            "diskIndex": "[copyIndex('disks')]"
          }
        },
        {
          "name": "diskNames",
          "count": 5,
          "input": "[concat('myDataDisk', copyIndex('diskNames', 1))]"
        }
      ]
    },
    "copy": [
      {
        "name": "top-level-object-array",
        "count": 5,
        "input": {
          "name": "[concat('myDataDisk', copyIndex('top-level-object-array', 1))]",
          "diskSizeGB": "1",
          "diskIndex": "[copyIndex('top-level-object-array')]"
        }
      },
      {
        "name": "top-level-string-array",
        "count": 5,
        "input": "[concat('myDataDisk', copyIndex('top-level-string-array', 1))]"
      },
      {
        "name": "top-level-integer-array",
        "count": 5,
        "input": "[copyIndex('top-level-integer-array')]"
      }
    ]
  },
  "resources": [],
  "outputs": {
    "exampleObject": {
      "value": "[variables('disk-array-on-object')]",
      "type": "object"
    },
    "exampleArrayOnObject": {
      "value": "[variables('disk-array-on-object').disks]",
      "type" : "array"
    },
    "exampleObjectArray": {
      "value": "[variables('top-level-object-array')]",
      "type" : "array"
    },
    "exampleStringArray": {
      "value": "[variables('top-level-string-array')]",
      "type" : "array"
    },
    "exampleIntegerArray": {
      "value": "[variables('top-level-integer-array')]",
      "type" : "array"
    }
  }
}
```

所建立之變數的類型取決於輸入物件。 例如，上述範例中名為**最上層物件陣列**的變數會傳回：

```json
[
  {
    "name": "myDataDisk1",
    "diskSizeGB": "1",
    "diskIndex": 0
  },
  {
    "name": "myDataDisk2",
    "diskSizeGB": "1",
    "diskIndex": 1
  },
  {
    "name": "myDataDisk3",
    "diskSizeGB": "1",
    "diskIndex": 2
  },
  {
    "name": "myDataDisk4",
    "diskSizeGB": "1",
    "diskIndex": 3
  },
  {
    "name": "myDataDisk5",
    "diskSizeGB": "1",
    "diskIndex": 4
  }
]
```

而且，名為**最上層字串陣列**的變數會傳回：

```json
[
  "myDataDisk1",
  "myDataDisk2",
  "myDataDisk3",
  "myDataDisk4",
  "myDataDisk5"
]
```

## <a name="depend-on-resources-in-a-loop"></a>依迴圈中的資源而定

您可以透過使用 `dependsOn` 元素，讓某個資源在另一個資源之後才部署。 若要部署相依於迴圈中資源集合的資源時，請在 dependsOn 元素中提供複製迴圈的名稱。 下列範例示範如何在部署虛擬機器之前部署三個儲存體帳戶。 不會顯示完整的虛擬機器定義。 請注意，複製元素將名稱設定為 `storagecopy`，並將虛擬機器的 dependsOn 元素設定為 `storagecopy`。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-01-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "copy": {
        "name": "storagecopy",
        "count": 3
      },
      "properties": {}
    },
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2015-06-15",
      "name": "[concat('VM', uniqueString(resourceGroup().id))]",
      "dependsOn": ["storagecopy"],
      ...
    }
  ],
  "outputs": {}
}
```

<a id="looping-on-a-nested-resource" />

## <a name="iteration-for-a-child-resource"></a>子系資源反覆項目
您無法為子資源使用複製迴圈。 若要為通常會在另一個資源內定義為巢狀的資源建立多個執行個體，您必須改為將該資源建立為最上層資源。 您可以透過類型和名稱屬性，定義和父資源之間的關聯性。

例如，假設您通常將資料集定義為 Data Factory 中的子資源。

```json
"resources": [
{
  "type": "Microsoft.DataFactory/datafactories",
  "name": "exampleDataFactory",
  ...
  "resources": [
    {
      "type": "datasets",
      "name": "exampleDataSet",
      "dependsOn": [
        "exampleDataFactory"
      ],
      ...
    }
  ]
```

若要建立多個資料集，請將它移出資料處理站。 資料集必須位於與資料處理站相同的等級，但它仍是資料處理站的子資源。 您可以透過類型和名稱屬性保留資料集與資料處理站之間的關聯性。 由於無法再從類型位於範本中的位置來推斷類型，您必須以此格式提供完整的類型︰`{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`。

若要建立與 Data Factory 執行個體的父/子關聯性，請提供包含父資源名稱之資料集的名稱。 使用格式︰`{parent-resource-name}/{child-resource-name}`。

下列範例顯示實作：

```json
"resources": [
{
  "type": "Microsoft.DataFactory/datafactories",
  "name": "exampleDataFactory",
  ...
},
{
  "type": "Microsoft.DataFactory/datafactories/datasets",
  "name": "[concat('exampleDataFactory', '/', 'exampleDataSet', copyIndex())]",
  "dependsOn": [
    "exampleDataFactory"
  ],
  "copy": {
    "name": "datasetcopy",
    "count": "3"
  },
  ...
}]
```

## <a name="example-templates"></a>範本的範例

下列範例顯示為資源或屬性建立多個執行個體的常見案例。

|範本  |說明  |
|---------|---------|
|[複製儲存體](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) |利用名稱中的索引編號來部署多個儲存體帳戶。 |
|[序列複製儲存體](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) |一次一個部署數個儲存體帳戶。 名稱包含索引編號。 |
|[以陣列複製儲存體](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) |部署數個儲存體帳戶。 名稱包含陣列中的值。 |
|[以可變的資料磁碟數目部署 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |利用虛擬機器部署數個資料磁碟。 |
|[複製變數](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) |示範逐一查看變數的不同方式。 |
|[多個安全性規則](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |將數個安全性規則部署至網路安全性群組。 從參數建構安全性規則。 如需參數，請參閱[多個 NSG 參數檔案](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json)。 |

## <a name="next-steps"></a>後續步驟

* 如須逐步瀏覽教學課程，請參閱[教學課程：使用 Resource Manager 範本建立多個資源執行個體](template-tutorial-create-multiple-instances.md)。

* 若要了解範本區段的相關資訊，請參閱[編寫 Azure Resource Manager 範本](template-syntax.md)。
* 若要了解如何部署範本，請參閱 [使用 Azure 資源管理員範本部署應用程式](deploy-powershell.md)。

