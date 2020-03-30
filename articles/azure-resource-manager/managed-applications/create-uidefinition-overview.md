---
title: 為門戶窗格創建 UiDefinition.json 檔
description: 介紹如何為 Azure 門戶創建使用者介面定義。 定義 Azure 託管應用程式時使用。
author: tfitzmac
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: tomfitz
ms.openlocfilehash: 2956c76f5bec353639b39228b982db21b6932deb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294900"
---
# <a name="createuidefinitionjson-for-azure-managed-applications-create-experience"></a>適用於 Azure 受控應用程式建立體驗的 CreateUiDefinition

本文檔介紹了 Azure 門戶在創建託管應用程式時用於定義使用者介面的**createUiDefine.json**檔的核心概念。

範本如下

```json
{
   "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
   "handler": "Microsoft.Azure.CreateUIDef",
   "version": "0.1.2-preview",
   "parameters": {
      "basics": [ ],
      "steps": [ ],
      "outputs": { },
      "resourceTypes": [ ]
   }
}
```

CreateUiDefinition 一律會包含三個屬性︰ 

* handler (處理常式)
* version
* 參數

處理常式應始終為`Microsoft.Azure.CreateUIDef`，並且支援的最新版本為`0.1.2-preview`。

parameters 屬性的結構描述取決於指定的處理常式和版本之組合。 針對受控應用程式，支援的屬性為 `basics`、`steps` 和 `outputs`。 basics 和 steps屬性包含要在 Azure 入口網站中顯示的[元素](create-uidefinition-elements.md) - 如同文字方塊和下拉式清單。 outputs 屬性可用來將指定元素的輸出值對應至 Azure Resource Manager 部署範本的參數。

建議包含 `$schema`，但是為選用。 如果指定，`version` 的值必須符合 `$schema` URI 內的版本。

您可以使用 JSON 編輯器創建 createUi定義，然後在[createUi定義沙箱](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade)中測試它以預覽它。 有關沙箱的詳細資訊，請參閱為 Azure[託管應用程式測試門戶介面](test-createuidefinition.md)。

## <a name="basics"></a>基本概念

基礎知識是 Azure 門戶分析檔時生成的第一步。 除了顯示 `basics` 中指定的元素之外，入口網站會插入元素，以供使用者選擇部署的訂用帳戶、資源群組和位置。 如果可能，查詢部署範圍參數的元素（如群集或管理員憑據的名稱）應執行此步驟。

## <a name="steps"></a>步驟

steps 屬性可以包含零個或多個要在 basics 之後顯示的額外步驟，其中都各包含一個或多個元素。 請考慮針對每個角色或要進行部署的應用程式層新增步驟。 例如，為主節點輸入添加步驟，為群集中的輔助節點添加步驟。

## <a name="outputs"></a>輸出

Azure 入口網站會使用 `outputs` 屬性，將 `basics` 和 `steps` 的屬性對應至 Azure Resource Manager 部署範本的參數。 這個字典的金鑰是範本參數的名稱，而值則是所參照元素的輸出物件之屬性。

若要設定受控應用程式資源名稱，您必須在輸出屬性中包含名為 `applicationResourceName` 的值。 如果未設置此值，應用程式將為其名稱分配 GUID。 您可以在使用者介面中包含文字方塊，向使用者要求名稱。

```json
"outputs": {
    "vmName": "[steps('appSettings').vmName]",
    "trialOrProduction": "[steps('appSettings').trialOrProd]",
    "userName": "[steps('vmCredentials').adminUsername]",
    "pwd": "[steps('vmCredentials').vmPwd.password]",
    "applicationResourceName": "[steps('appSettings').vmName]"
}
```

## <a name="resource-types"></a>資源類型

要將可用位置篩選為僅支援要部署的資源類型的位置，請提供資源類型的陣列。 如果提供多個資源類型，則僅返回支援所有資源類型的位置。 這是選用屬性。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Azure.CreateUIDef",
    "version": "0.1.2-preview",
    "parameters": {
      "resourceTypes": ["Microsoft.Compute/disks"],
      "basics": [
        ...
```  

## <a name="functions"></a>函式

CreateUi[定義提供了用於](create-uidefinition-functions.md)處理元素的輸入和輸出以及條件等功能的功能。 這些函數在語法和功能上與 Azure 資源管理器範本函數相似。

## <a name="next-steps"></a>後續步驟

createUiDefinition.json 檔案本身有簡單的結構描述。 它的實際深度來自於所有支援的元素和函式。 這些項目會在以下位置更詳細地描述：

- [元素](create-uidefinition-elements.md)
- [函式](create-uidefinition-functions.md)

下列位置會提供 createUiDefinition 的目前 JSON 結構描述：`https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json`。

如需使用者介面檔案範例，請參閱 [createUiDefinition.json](https://github.com/Azure/azure-managedapp-samples/blob/master/Managed%20Application%20Sample%20Packages/201-managed-app-using-existing-vnet/createUiDefinition.json)。
