---
title: 自訂角色定義的總覽
description: 說明為受控應用程式建立自訂角色定義的概念。
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 09/16/2019
ms.openlocfilehash: 7b7fff913c177703f959bfa103c8e310d01059e2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81391833"
---
# <a name="custom-role-definition-artifact-in-azure-managed-applications"></a>Azure 受控應用程式中的自訂角色定義成品

自訂角色定義是受控應用程式中的選擇性成品。 它可用來判斷受控應用程式執行其功能所需的許可權。

本文提供自訂角色定義成品及其功能的總覽。

## <a name="custom-role-definition-artifact"></a>自訂角色定義成品

您需要將自訂角色定義成品命名為 customRoleDefinition.js在上。 將它放在與上 createUiDefinition.js相同的層級，並在建立受控應用程式定義的 .zip 套件中 mainTemplate.js。 若要瞭解如何建立 .zip 套件併發布受控應用程式定義，請參閱[發佈受控應用程式定義。](publish-service-catalog-app.md)

## <a name="custom-role-definition-schema"></a>自訂角色定義架構

檔案上的 customRoleDefinition.js有最上層 `roles` 的屬性，也就是角色的陣列。 這些角色是受控應用程式必須運作的許可權。 目前只允許內建角色，但您可以指定多個角色。 角色定義的識別碼或角色名稱可以參考角色。

自訂角色定義的範例 JSON：

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

角色是由 `$.properties.roleName` 或組成 `id` ：

```json
{
    "id": null,
    "properties": {
        "roleName": "Contributor"
    }
}
```

> [!NOTE]
> 您可以使用 `id` 或 `roleName` 欄位。 只需要一個。 這些欄位是用來查閱應套用的角色定義。 如果同時提供這兩者，則 `id` 會使用欄位。

|屬性|必要項？|描述|
|---------|---------|---------|
|id|是|內建角色的識別碼。 您可以使用完整識別碼，或只是 GUID。|
|roleName|是|內建角色的名稱。|