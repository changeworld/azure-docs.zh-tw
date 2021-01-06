---
title: 設定資源的部署順序
description: 說明如何在部署期間，將一個 Azure 資源設定為相依于另一個資源。 相依性可確保以正確的順序部署資源。
ms.topic: conceptual
ms.date: 12/21/2020
ms.openlocfilehash: f6b63b066da06a17c3a2e51ab0f3ab9bf521a144
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97934742"
---
# <a name="define-the-order-for-deploying-resources-in-arm-templates"></a>定義在 ARM 範本中部署資源的順序

部署資源時，您可能需要確保某些資源存在於其他資源之前。 例如，在部署資料庫之前，您需要邏輯 SQL server。 您可以藉由將一個資源標記為相依于其他資源，來建立此關聯性。 您 `dependsOn` 可以使用元素來定義明確的相依性。 使用 **參考** 或 **清單** 函數來定義隱含相依性。

Azure Resource Manager 會評估資源之間的相依性，並依其相依順序部署它們。 如果資源並未彼此相依，Resource Manager 就會平行部署資源。 您只需要針對部署在相同範本中的資源定義相依性。

## <a name="dependson"></a>dependsOn

在 (ARM 範本) 的 Azure Resource Manager 範本中， `dependsOn` 元素可讓您將一個資源定義為相依于一或多個資源。 其值為 JavaScript 物件標記法 (JSON) 字串陣列，其中每個字串都是資源名稱或識別碼。 陣列可包含有 [條件地部署](conditional-resource-deployment.md)的資源。 當條件式資源未部署時，Azure Resource Manager 會自動將它從必要的相依性中移除。

下列範例顯示相依于虛擬網路、網路安全性群組和公用 IP 位址的網路介面。 如需完整的範本，請參閱 [LINUX VM 的快速入門範本](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-simple-linux/azuredeploy.json)。

```json
{
    "type": "Microsoft.Network/networkInterfaces",
    "apiVersion": "2020-06-01",
    "name": "[variables('networkInterfaceName')]",
    "location": "[parameters('location')]",
    "dependsOn": [
      "[resourceId('Microsoft.Network/networkSecurityGroups/', parameters('networkSecurityGroupName'))]",
      "[resourceId('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]",
      "[resourceId('Microsoft.Network/publicIpAddresses/', variables('publicIpAddressName'))]"
    ],
    ...
}
```

雖然您可能會傾向于使用 `dependsOn` 來對應資源之間的關聯性，但請務必瞭解您的做法。 例如，若要記錄資源的互連方式， `dependsOn` 不是正確的方法。 您無法在部署之後查詢元素中定義了哪些資源 `dependsOn` 。 設定不必要的相依性會減緩部署時間，因為 Resource Manager 無法以平行方式部署這些資源。

## <a name="child-resources"></a>子資源

[子資源](child-resource-name-type.md)與父資源之間不會自動建立隱含的部署相依性。 如果您需要在父資源之後部署子資源，請設定 `dependsOn` 屬性。

下列範例顯示邏輯 SQL 伺服器和資料庫。 請注意，即使資料庫是伺服器的子系，還是會在資料庫和伺服器之間定義明確的相依性。

```json
"resources": [
  {
    "type": "Microsoft.Sql/servers",
    "apiVersion": "2020-02-02-preview",
    "name": "[parameters('serverName')]",
    "location": "[parameters('location')]",
    "properties": {
      "administratorLogin": "[parameters('administratorLogin')]",
      "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
    },
    "resources": [
      {
        "type": "databases",
        "apiVersion": "2020-08-01-preview",
        "name": "[parameters('sqlDBName')]",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Standard",
          "tier": "Standard"
          },
        "dependsOn": [
          "[resourceId('Microsoft.Sql/servers', concat(parameters('serverName')))]"
        ]
      }
    ]
  }
]
```

如需完整的範本，請參閱 [Azure SQL Database 的快速入門範本](https://github.com/Azure/azure-quickstart-templates/blob/master/101-sql-database/azuredeploy.json)。

## <a name="reference-and-list-functions"></a>reference 和 list 函式

[reference 函式](template-functions-resource.md#reference) 可讓運算式從其他 JSON 名稱和值組或執行階段資源衍生其值。 [list* 函式](template-functions-resource.md#list)會從清單作業傳回資源的值。

參考和清單運算式會隱含地宣告某一個資源相依于另一個資源。 請盡可能使用隱含的參考，以避免新增不必要的相依性。

若要強制執行隱含相依性，請依名稱（而非資源識別碼）參考資源。 如果您將資源識別碼傳遞到 reference 或 list 函式，就不會建立隱含參考。

函數的一般格式 `reference` 為：

```json
reference('resourceName').propertyPath
```

函數的一般格式 `listKeys` 為：

```json
listKeys('resourceName', 'yyyy-mm-dd')
```

在下列範例中，CDN 端點明確相依於 CDN 設定檔，並隱含相依於 Web 應用程式。

```json
{
    "name": "[variables('endpointName')]",
    "apiVersion": "2016-04-02",
    "type": "endpoints",
    "location": "[resourceGroup().location]",
    "dependsOn": [
      "[variables('profileName')]"
    ],
    "properties": {
      "originHostHeader": "[reference(variables('webAppName')).hostNames[0]]",
      ...
    }
```

若要深入了解，請參閱 [reference 函數](template-functions-resource.md#reference)。

## <a name="depend-on-resources-in-a-loop"></a>依迴圈中的資源而定

若要部署相依于 [複製迴圈](copy-resources.md)中資源的資源，您有兩個選項。 您可以在迴圈或整個迴圈中設定個別資源的相依性。

> [!NOTE]
> 在大部分的情況下，您應該在複製迴圈內設定個別資源的相依性。 只有當您需要迴圈中的所有資源都存在，才能建立下一個資源時，才會相依于整個迴圈。 設定整個迴圈的相依性會大幅展開相依性圖形，特別是當那些迴圈的資源相依于其他資源時。 擴充的相依性讓部署變得很難有效率地完成。

下列範例顯示如何部署多個虛擬機器。 範本會建立相同數目的網路介面。 每部虛擬機器都相依于一個網路介面，而不是整個迴圈。

```json
{
  "type": "Microsoft.Network/networkInterfaces",
  "apiVersion": "2020-05-01",
  "name": "[concat(variables('nicPrefix'),'-',copyIndex())]",
  "location": "[parameters('location')]",
  "copy": {
    "name": "nicCopy",
    "count": "[parameters('vmCount')]"
  },
  ...
},
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2020-06-01",
  "name": "[concat(variables('vmPrefix'),copyIndex())]",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[resourceId('Microsoft.Network/networkInterfaces',concat(variables('nicPrefix'),'-',copyIndex()))]"
  ],
  "copy": {
    "name": "vmCopy",
    "count": "[parameters('vmCount')]"
  },
  "properties": {
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(variables('nicPrefix'),'-',copyIndex()))]",
          "properties": {
            "primary": "true"
          }
        }
      ]
    },
    ...
  }
}
```

下列範例顯示如何在部署虛擬機器之前部署三個儲存體帳戶。 請注意， `copy` 元素已 `name` 設定為 `storagecopy` ，而且 `dependsOn` 虛擬機器的專案也設定為 `storagecopy` 。

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

## <a name="circular-dependencies"></a>循環相依性

Resource Manager 範本會在驗證期間識別循環相依性。 如果您收到迴圈相依性的錯誤，請評估您的範本，以查看是否可以移除任何相依性。 如果移除相依性沒有作用，您可以藉由將某些部署作業移至子資源，以避免迴圈相依性。 在具有迴圈相依性的資源之後部署子資源。 例如，假設您要部署兩部虛擬機器，但是您必須分別在上面設定互相參考的屬性。 您可以採取下列順序部署︰

1. vm1
2. vm2
3. vm1 的擴充相依於 vm1 和 vm2。 擴充在 vm1 上設定從 vm2 取得的值。
4. vm2 的擴充相依於 vm1 和 vm2。 擴充在 vm2 上設定從 vm1 取得的值。

如需評估部署順序及解決相依性錯誤的相關資訊，請參閱[使用 Azure Resource Manager 針對常見的 Azure 部署錯誤進行疑難排解](common-deployment-errors.md)。

## <a name="next-steps"></a>後續步驟

* 若要進行教學課程，請參閱 [教學課程：建立具有相依資源的 ARM 範本](template-tutorial-create-templates-with-dependent-resources.md)。
* 如需涵蓋資源相依性的 Microsoft Learn 模組，請參閱 [使用 ADVANCED ARM 範本功能管理複雜的雲端部署](/learn/modules/manage-deployments-advanced-arm-template-features/)。
* 如需設定相依性時的建議，請參閱 [ARM 範本的最佳做法](template-best-practices.md)。
* 若要了解在部署期間如何對相依性進行疑難排解，請參閱[使用 Azure Resource Manager 針對常見的 Azure 部署錯誤進行疑難排解](common-deployment-errors.md)。
* 若要瞭解如何建立 Azure Resource Manager 範本，請參閱 [瞭解 ARM 範本的結構和語法](template-syntax.md)。
* 如需範本中可用函式的清單，請參閱 [ARM 範本函數](template-functions.md)。
