---
title: ArmApiControl UI 元素
description: 描述 Azure 入口網站的 ArmApiControl UI 元素。 用於呼叫 API 作業。
author: tfitzmac
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: tomfitz
ms.openlocfilehash: bbe36e072d10b81c421331b2212d8b161afd2693
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87096229"
---
# <a name="microsoftcommonarmapicontrol-ui-element"></a>ArmApiControl UI 元素

ArmApiControl 可讓您取得 Azure Resource Manager API 作業的結果。 使用結果來填入其他控制項中的動態內容。

## <a name="ui-sample"></a>UI 範例

此控制項沒有任何 UI。

## <a name="schema"></a>結構描述

下列範例顯示此控制項的架構：

```json
{
    "name": "testApi",
    "type": "Microsoft.Solutions.ArmApiControl",
    "request": {
        "method": "{HTTP-method}",
        "path": "{path-for-the-URL}",
        "body": {
            "key1": "val1",
            "key2": "val2"
        }
    }
}
```

## <a name="sample-output"></a>範例輸出

控制項的輸出不會顯示給使用者。 相反地，作業的結果會用於其他控制項中。

## <a name="remarks"></a>備註

- `request.method`屬性會指定 HTTP 方法。 只 `GET` `POST` 允許或。
- `request.path`屬性會指定 URL 的相對路徑。 它可以是靜態路徑，或藉由參考其他控制項的輸出值來動態地加以結構化。
- `request.body` 是選用屬性。 使用它來指定與要求一起傳送的 JSON 主體。 主體可以是靜態內容，或藉由參考其他控制項的輸出值，以動態方式加以結構化。

## <a name="example"></a>範例

在下列範例中， `providersApi` 元素會呼叫 API 以取得提供者物件的陣列。

`allowedValues`元素的屬性 `providersDropDown` 會設定為取得提供者的名稱。 它會顯示在下拉式清單中。

```json
{
    "name": "providersApi",
    "type": "Microsoft.Solutions.ArmApiControl",
    "request": {
        "method": "GET",
        "path": "[concat(subscription().id, '/providers/Microsoft.Network/expressRouteServiceProviders?api-version=2019-02-01')]"
    }
},
{
    "name": "providerDropDown",
    "type": "Microsoft.Common.DropDown",
    "label": "Provider",
    "toolTip": "The provider that offers the express route connection.",
    "constraints": {
        "allowedValues": "[map(steps('settings').providersApi.value, (item) => parse(concat('{\"label\":\"', item.name, '\",\"value\":\"', item.name, '\"}')))]",
        "required": true
    },
    "visible": true
}
```

如需使用 ArmApiControl 檢查資源名稱可用性的範例，請參閱[Microsoft Common. TextBox](microsoft-common-textbox.md)。

## <a name="next-steps"></a>後續步驟

* 如需建立 UI 定義的簡介，請參閱[開始使用 CreateUiDefinition](create-uidefinition-overview.md)。
* 如需 UI 元素中通用屬性的說明，請參閱 [CreateUiDefinition 元素](create-uidefinition-elements.md)。
