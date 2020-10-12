---
title: 部署多個資源實例
description: 使用 Azure Resource Manager 範本中的複製作業和陣列來部署資源類型多次。
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 411c92061826a6e8bc59380d0440fb69816557a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91293963"
---
# <a name="resource-iteration-in-arm-templates"></a>ARM 範本中的資源反復專案

本文說明如何在 Azure Resource Manager (ARM) 範本中建立多個資源的實例。 藉由將 **複製** 元素新增至範本的資源區段，您可以動態設定要部署的資源數目。 您也可以避免重複範本語法。

您也可以使用具有 [屬性](copy-properties.md)、 [變數](copy-variables.md)和 [輸出](copy-outputs.md)的 copy。

若需要指定是否要部署資源，請參閱[條件元素](conditional-resource-deployment.md)。

## <a name="syntax"></a>語法

Copy 元素具有下列一般格式：

```json
"copy": {
  "name": "<name-of-loop>",
  "count": <number-of-iterations>,
  "mode": "serial" <or> "parallel",
  "batchSize": <number-to-deploy-serially>
}
```

**Name**屬性是任何可識別迴圈的值。 **Count**屬性會指定您想要的資源類型反覆運算次數。

使用 **mode** 和 **batchSize** 屬性來指定是否以平行方式或依順序部署資源。 這些屬性會以 [序列或平行](#serial-or-parallel)方式描述。

## <a name="copy-limits"></a>複製限制

計數不能超過800。

計數不可為負數。 如果您使用最新版本的 Azure CLI、PowerShell 或 REST API 來部署範本，則可以為零。 具體而言，您必須使用：

* Azure PowerShell **2.6** 或更新版本
* Azure CLI **2.0.74 版** 或更新版本
* REST API **2019-05-10** 版或更新版本
* [連結的部署](linked-templates.md) 必須使用 API **2019-05-10** 版或更新版本，才能進行部署資源類型

舊版 PowerShell、CLI 和 REST API 不支援 count 的零。

使用具有複製的 [完整模式部署](deployment-modes.md) 時，請小心。 如果您以完整模式重新部署至資源群組，則會刪除在解析複製迴圈之後未在範本中指定的任何資源。

## <a name="resource-iteration"></a>資源反覆項目

下列範例會建立在 **storageCount** 參數中指定的儲存體帳戶數目。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageCount": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": "[parameters('storageCount')]"
            }
        }
    ],
    "outputs": {}
}
```

請注意，每個資源的名稱均包含 `copyIndex()` 函式，並會傳回目前的反覆項目迴圈。 `copyIndex()` 以零為起始。 因此，下列範例：

```json
"name": "[concat('storage', copyIndex())]",
```

會建立這些名稱︰

* storage0
* storage1
* storage2.

若要位移索引值，您可以傳遞 copyIndex() 函式中的值。 在 copy 元素中仍會指定反復專案的數目，但 copyIndex 的值是以指定的值來位移。 因此，下列範例：

```json
"name": "[concat('storage', copyIndex(1))]",
```

會建立這些名稱︰

* storage1
* storage2
* storage3

使用陣列時，複製作業會有幫助，因為您可以逐一查看陣列中的每個項目。 使用陣列上的 `length` 函式指定反覆運算的計數，並使用 `copyIndex` 來擷取陣列中目前的索引。

下列範例會為參數中提供的每個名稱建立一個儲存體帳戶。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageNames": {
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
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(parameters('storageNames')[copyIndex()], uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": "[length(parameters('storageNames'))]"
      }
    }
  ],
  "outputs": {}
}
```

如果您想要從已部署的資源傳回值，您可以 [在 [輸出] 區段中使用 [複製](copy-outputs.md)]。

## <a name="serial-or-parallel"></a>序列或平行

根據預設，Resource Manager 會以平行方式建立資源。 它不會對以平行方式部署的資源數目套用限制，除了範本中800資源的總限制之外。 不保證資源會循序建立。

不過，建議您指定將資源部署在序列中。 例如，在更新生產環境時，您可以錯開更新，因此任何一次就只會更新特定數目。 若要以序列方式部署資源的多個執行個體，請將 `mode` 設定為 **serial** 並將 `batchSize` 設為一次要部署的執行個體數目。 透過序列模式，Resource Manager 會在迴圈中較早的執行個體上建立相依性，因此在前一批次完成之前，它不會啟動一個批次。

的值 `batchSize` 不能超過 `count` 複製元素中的值。

例如，若要以序列方式一次部署兩個儲存體帳戶，請使用：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
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

## <a name="depend-on-resources-in-a-loop"></a>依迴圈中的資源而定

您可以透過使用 `dependsOn` 元素，讓某個資源在另一個資源之後才部署。 若要部署相依於迴圈中資源集合的資源時，請在 dependsOn 元素中提供複製迴圈的名稱。 下列範例顯示如何在部署虛擬機器之前部署三個儲存體帳戶。 不會顯示完整的虛擬機器定義。 請注意，copy 元素的名稱是設定為 `storagecopy` ，而虛擬機器的 dependsOn 元素也會設定為 `storagecopy` 。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
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

|範本  |描述  |
|---------|---------|
|[複製儲存體](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) |利用名稱中的索引編號來部署多個儲存體帳戶。 |
|[序列複製儲存體](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) |一次一個部署數個儲存體帳戶。 名稱包含索引編號。 |
|[以陣列複製儲存體](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) |部署數個儲存體帳戶。 名稱包含陣列中的值。 |
|[以可變的資料磁碟數目部署 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |利用虛擬機器部署數個資料磁碟。 |
|[多個安全性規則](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |將數個安全性規則部署至網路安全性群組。 從參數建構安全性規則。 如需參數，請參閱[多個 NSG 參數檔案](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json)。 |

## <a name="next-steps"></a>後續步驟

* 若要進行教學課程，請參閱 [教學課程：使用 ARM 範本建立多個資源實例](template-tutorial-create-multiple-instances.md)。
* 如需複製元素的其他用途，請參閱：
  * [ARM 範本中的屬性反復專案](copy-properties.md)
  * [ARM 範本中的變數反復專案](copy-variables.md)
  * [ARM 範本中的輸出反復專案](copy-outputs.md)
* 如需搭配使用複製與嵌套範本的詳細資訊，請參閱 [使用複製](linked-templates.md#using-copy)。
* 如果您想要瞭解範本的區段，請參閱 [撰寫 ARM 範本](template-syntax.md)。
* 若要瞭解如何部署您的範本，請參閱 [使用 ARM 範本部署應用程式](deploy-powershell.md)。

