---
title: 入口網站窗格的檔案 CreateUiDefinition.js
description: 描述如何建立 Azure 入口網站的使用者介面定義。 定義 Azure 受控應用程式時使用。
author: tfitzmac
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: tomfitz
ms.openlocfilehash: 4ee489e8b596adf0767856e3358c9bdcb17fbb6a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87004355"
---
# <a name="createuidefinitionjson-for-azure-managed-applications-create-experience"></a>適用於 Azure 受控應用程式建立體驗的 CreateUiDefinition

本檔介紹檔案**上createUiDefinition.js**的核心概念。 Azure 入口網站在建立受控應用程式時，會使用這個檔案來定義使用者介面。

範本如下所示

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Azure.CreateUIDef",
    "version": "0.1.2-preview",
    "parameters": {
        "config": {
            "basics": { }
        },
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

處理常式應該一律是 `Microsoft.Azure.CreateUIDef` ，而最新支援的版本是 `0.1.2-preview` 。

parameters 屬性的結構描述取決於指定的處理常式和版本之組合。 針對受控應用程式，支援的屬性為 `basics` 、 `steps` 、 `outputs` 和 `config` 。 basics 和 steps屬性包含要在 Azure 入口網站中顯示的[元素](create-uidefinition-elements.md) - 如同文字方塊和下拉式清單。 [輸出] 屬性會用來將指定專案的輸出值對應至 Azure Resource Manager 範本的參數。 `config`只有當您需要覆寫步驟的預設行為時，才會使用 `basics` 。

建議包含 `$schema`，但是為選用。 如果指定，`version` 的值必須符合 `$schema` URI 內的版本。

您可以使用 JSON 編輯器來建立 createUiDefinition，然後在[CreateUiDefinition 沙箱](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade)中進行測試以進行預覽。 如需有關沙箱的詳細資訊，請參閱[測試入口網站介面的 Azure 受控應用程式](test-createuidefinition.md)。

## <a name="basics"></a>基本概念

**基本**步驟是 Azure 入口網站剖析檔案時所產生的第一個步驟。 根據預設，[基本] 步驟可讓使用者選擇要部署的訂用帳戶、資源群組和位置。

:::image type="content" source="./media/create-uidefinition-overview/basics.png" alt-text="基本概念預設":::

您可以在此區段中新增更多元素。 可能的話，請新增查詢全部署參數的元素，例如叢集名稱或系統管理員認證。

下列範例顯示已加入至預設元素的文字方塊。

```json
"basics": [
    {
        "name": "textBox1",
        "type": "Microsoft.Common.TextBox",
        "label": "Textbox on basics",
        "defaultValue": "my text value",
        "toolTip": "",
        "visible": true
    }
]
```

## <a name="config"></a>Config

當您需要覆寫基本步驟的預設行為時，請指定 config 元素。 下列範例會顯示可用的屬性。

```json
"config": {  
    "basics": {  
        "description": "Customized description with **markdown**, see [more](https://www.microsoft.com).",
        "subscription": {
            "constraints": {
                "validations": [
                    {
                        "isValid": "[expression for checking]",
                        "message": "Please select a valid subscription."
                    },
                    {
                        "permission": "<Resource Provider>/<Action>",
                        "message": "Must have correct permission to complete this step."
                    }
                ]
            },
            "resourceProviders": [ "<Resource Provider>" ]
        },
        "resourceGroup": {
            "constraints": {
                "validations": [
                    {
                        "isValid": "[expression for checking]",
                        "message": "Please select a valid resource group."
                    }
                ]
            },
            "allowExisting": true
        },
        "location": {  
            "label": "Custom label for location",  
            "toolTip": "provide a useful tooltip",  
            "resourceTypes": [ "Microsoft.Compute/virtualMachines" ],
            "allowedValues": [ "eastus", "westus2" ],  
            "visible": true  
        }  
    }  
},  
```

針對 `description` ，請提供可描述資源的已啟用 markdown 字串。 支援多行格式和連結。

針對 `location` ，請指定您想要覆寫之位置控制項的屬性。 未覆寫的任何屬性會設定為其預設值。 `resourceTypes`接受包含完整資源類型名稱的字串陣列。 [位置] 選項僅限於支援資源類型的區域。  `allowedValues`  接受區域字串的陣列。 只有那些區域才會出現在下拉式清單中。您可以同時設定 `allowedValues`   和  `resourceTypes` 。 結果是兩個清單的交集。 最後， `visible` 屬性可用來有條件地或完全停用 [位置] 下拉式清單。  

`subscription`和 `resourceGroup` 元素可讓您指定其他驗證。 指定驗證的語法等同于 [自訂驗證][文字方塊](microsoft-common-textbox.md)。 您也可以指定 `permission` 訂用帳戶或資源群組上的驗證。  

訂用帳戶控制項接受資源提供者命名空間的清單。 例如，您可以指定**Microsoft. Compute**。 當使用者選取不支援資源提供者的訂用帳戶時，它會顯示錯誤訊息。 當資源提供者未在該訂用帳戶上註冊，而且使用者沒有註冊資源提供者的許可權時，就會發生此錯誤。  

資源群組控制項具有的選項 `allowExisting` 。 當時 `true` ，使用者可以選取已有資源的資源群組。 此旗標最適用于解決方案範本，其中預設行為規定使用者必須選取新的或空白的資源群組。 在大部分的其他情況下，並不需要指定此屬性。  

## <a name="steps"></a>步驟

[步驟] 屬性包含零或多個要在基本概念後顯示的額外步驟。 每個步驟都包含一或多個元素。 請考慮針對每個角色或要進行部署的應用程式層新增步驟。 例如，新增主要節點輸入的步驟，以及叢集中背景工作節點的步驟。

```json
"steps": [
    {
        "name": "demoConfig",
        "label": "Configuration settings",
        "elements": [
          ui-elements-needed-to-create-the-instance
        ]
    }
]
```

## <a name="outputs"></a>輸出

Azure 入口網站會使用 `outputs` 屬性，將 `basics` 和 `steps` 的屬性對應至 Azure Resource Manager 部署範本的參數。 這個字典的金鑰是範本參數的名稱，而值則是所參照元素的輸出物件之屬性。

若要設定受控應用程式資源名稱，您必須在輸出屬性中包含名為 `applicationResourceName` 的值。 如果您未設定此值，應用程式會指派名稱的 GUID。 您可以在使用者介面中包含文字方塊，向使用者要求名稱。

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

若要將可用的位置篩選為僅支援要部署之資源類型的位置，請提供資源類型的陣列。 如果您提供一個以上的資源類型，則只會傳回支援所有資源類型的位置。 這是選擇性屬性。

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

## <a name="functions"></a>函數

CreateUiDefinition 提供函式來處理[元素的輸入](create-uidefinition-functions.md)和輸出，以及條件之類的功能。 這些函式在 Azure Resource Manager 範本函式的語法和功能上都很類似。

## <a name="next-steps"></a>接下來的步驟

createUiDefinition.json 檔案本身有簡單的結構描述。 它的實際深度來自於所有支援的元素和函式。 這些項目會在以下位置更詳細地描述：

- [元素](create-uidefinition-elements.md)
- [函式](create-uidefinition-functions.md)

下列位置會提供 createUiDefinition 的目前 JSON 結構描述：`https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json`。

如需使用者介面檔案範例，請參閱 [createUiDefinition.json](https://github.com/Azure/azure-managedapp-samples/blob/master/Managed%20Application%20Sample%20Packages/201-managed-app-using-existing-vnet/createUiDefinition.json)。
