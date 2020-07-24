---
title: 建立 UI 定義參考函數
description: 描述針對參考其他物件的 Azure 入口網站來建立 UI 定義時，所要使用的函數。
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: ad21c5b34a58c35b2cef5e430be7cb8cd1296402
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87096233"
---
# <a name="createuidefinition-referencing-functions"></a>CreateUiDefinition 參考函式

從 CreateUiDefinition 檔的屬性或內容參考輸出時，所要使用的函式。

## <a name="basics"></a>basics

傳回在[基本](create-uidefinition-overview.md#basics)步驟中定義之元素的輸出值。 將元素的名稱當做參數傳入此函式。

若要取得其他步驟中元素的輸出值，請使用[步驟（）](#steps)函式。

下列範例會傳回 Basics 步驟中名為 `clusterName` 之元素的輸出︰

```json
"[basics('clusterName')]"
```

傳回的值會根據所抓取的元素類型而有所不同。

## <a name="location"></a>location

傳回在 Basics 步驟或目前內容中選取的位置。

下列範例會傳回如下的值 `"westus"` ：

```json
"[location()]"
```

## <a name="resourcegroup"></a>resourceGroup

傳回在 [基本] 步驟或目前內容中所選取之 resourceGroup 的詳細資料。

下列範例將：

```json
"[resourceGroup()]"
```

傳回下列屬性：

```json
{
    "mode": "New" or "Existing",
    "name": "{resourceGroupName}",
    "location": "{resourceGroupLocation}"
}
```

您可以使用點標記法來取得任何特定值。

```json
"[resourceGroup().name]"
```

## <a name="steps"></a>steps

傳回指定步驟上的元素。 傳入步驟的名稱，做為此函式的參數。 從傳回的元素中，您可以取得特定的屬性值。

若要取得基本步驟中元素的輸出值，請使用[基本（）](#basics)函數。

下列範例會傳回名為的步驟 `vmParameters` 。 在該步驟中，是名為的元素 `adminUsername` 。

```json
"[steps('vmParameters').adminUsername]"
```

## <a name="subscription"></a>訂用帳戶

傳回在基本步驟或目前內容中選取之訂用帳戶的屬性。

下列範例將：

```json
"[subscription()]"
```

傳回下列屬性：

```json
{
    "id": "/subscriptions/{subscription-id}",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}",
    "displayName": "{name-of-subscription}"
}
```

## <a name="next-steps"></a>後續步驟

* 如需開發入口網站介面的簡介，請參閱[適用于 Azure 受控應用程式的建立體驗的CreateUiDefinition.js](create-uidefinition-overview.md)。
