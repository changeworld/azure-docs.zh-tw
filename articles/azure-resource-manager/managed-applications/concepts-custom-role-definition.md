---
title: 自訂角色定義的概述
description: 描述為託管應用程式創建自訂角色定義的概念。
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 09/16/2019
ms.openlocfilehash: 88e42fd9626276f6c77b46b33c138407f91d06ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650756"
---
# <a name="custom-role-definition-artifact-in-azure-managed-applications"></a>Azure 託管應用程式中的自訂角色定義專案

自訂角色定義是託管應用程式中的可選項目。 它用於確定託管應用程式執行其功能所需的許可權。

本文概述了自訂角色定義專案及其功能。

## <a name="custom-role-definition-artifact"></a>自訂角色定義專案

您需要命名自訂角色定義專案自訂角色定義.json。 將其置於創建託管應用程式定義的 .zip 包中的 createUiDefinition.json 和 mainTemplate.json 相同的級別。 要瞭解如何創建 .zip 包併發布託管應用程式定義，請參閱[發佈託管應用程式定義。](publish-managed-app-definition-quickstart.md)

## <a name="custom-role-definition-schema"></a>自訂角色定義架構

自訂RoleDefinition.json檔具有一個頂級`roles`屬性，該屬性是角色陣列。 這些角色是託管應用程式需要運行的許可權。 目前，只允許內置角色，但您可以指定多個角色。 角色定義 ID 或角色名稱可以引用角色。

自訂角色定義的示例 JSON：

```json
{
    "contentVersion": "0.0.0.1",
    "roles": [
        {
            "properties": {
                "roleName": "Contributor"
            }
        },
        {
            "id": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
        },
        {
            "id": "/providers/Microsoft.Authorization/roledefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
        }
    ]
}
```

## <a name="roles"></a>角色

角色由 或`$.properties.roleName`組成。 `id`

```json
{
    "id": null,
    "properties": {
        "roleName": "Contributor"
    }
}
```

> [!NOTE]
> 可以使用 或`id``roleName`欄位。 只需要一個。 這些欄位用於查找應應用的角色定義。 如果同時提供兩者，`id`將使用該欄位。

|屬性|必要項？|描述|
|---------|---------|---------|
|id|是|內置角色的 ID。 您可以使用完整 ID 或僅使用 GUID。|
|角色名稱|是|內置角色的名稱。|