---
title: CreateUiDefinition 成品
description: 說明如何建立 Azure 受控應用程式的 createUiDefinition 成品。 檔案的名稱為 createUiDefinition.js開啟。
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 07/11/2019
ms.openlocfilehash: 9be1cb02c419314a33b9487bf183ef3659b2b549
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75651484"
---
# <a name="reference-user-interface-elements-artifact"></a>參考：使用者介面專案成品

本文是 Azure 受控應用程式中成品*createUiDefinition.js*的參考。 如需撰寫使用者介面元素的詳細資訊，請參閱[建立使用者介面元素](create-uidefinition-elements.md)。

## <a name="user-interface-elements"></a>使用者介面元素

下列 JSON 顯示 Azure 受控應用程式的檔案*createUiDefinition.js*範例：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "applicationSettings",
        "label": "Application Settings",
        "subLabel": {
          "preValidation": "Configure your application settings",
          "postValidation": "Done"
        },
        "bladeTitle": "Application Settings",
        "elements": [
          {
            "name": "funcname",
            "type": "Microsoft.Common.TextBox",
            "label": "Name of the function to be created",
            "toolTip": "Name of the function to be created",
            "visible": true,
            "constraints": {
              "required": true
            }
          },
          {
            "name": "storagename",
            "type": "Microsoft.Common.TextBox",
            "label": "Name of the storage to be created",
            "toolTip": "Name of the storage to be created",
            "visible": true,
            "constraints": {
              "required": true
            }
          },
          {
            "name": "zipFileBlobUri",
            "type": "Microsoft.Common.TextBox",
            "defaultValue": "https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip/functionpackage.zip",
            "label": "The Uri to the uploaded function zip file",
            "toolTip": "The Uri to the uploaded function zip file",
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "funcname": "[steps('applicationSettings').funcname]",
      "storageName": "[steps('applicationSettings').storagename]",
      "zipFileBlobUri": "[steps('applicationSettings').zipFileBlobUri]"
    }
  }
}
```

## <a name="next-steps"></a>後續步驟

- [教學課程：使用自訂動作和資源建立受控應用程式](tutorial-create-managed-app-with-custom-provider.md)
- [參考：部署範本成品](reference-main-template-artifact.md)
- [參考：視圖定義成品](reference-view-definition-artifact.md)