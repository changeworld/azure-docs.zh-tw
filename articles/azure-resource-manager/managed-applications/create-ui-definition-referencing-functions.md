---
title: 建立參考函數的 UI 定義
description: 描述為參考其他物件的 Azure 入口網站，建立 UI 定義時要使用的函數。
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: ad21c5b34a58c35b2cef5e430be7cb8cd1296402
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87096233"
---
# <a name="createuidefinition-referencing-functions"></a>CreateUiDefinition 參考函數

從 CreateUiDefinition 檔的屬性或內容參考輸出時所使用的函數。

## <a name="basics"></a>basics

傳回 [基本](create-uidefinition-overview.md#basics) 步驟中定義之元素的輸出值。 將專案的名稱以參數的形式傳入此函式。

若要取得其他步驟中專案的輸出值，請使用 [ ( # B1 ](#steps) 函數的步驟。

下列範例會傳回 Basics 步驟中名為 `clusterName` 之元素的輸出︰

```json
"[basics('clusterName')]"
```

傳回的值會根據抓取的元素類型而有所不同。

## <a name="location"></a>location

傳回在 Basics 步驟或目前內容中選取的位置。

下列範例會傳回類似以下的值 `"westus"` ：

```json
"[location()]"
```

## <a name="resourcegroup"></a>resourceGroup

傳回在基本步驟或目前內容中所選取之 resourceGroup 的詳細資料。

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

您可以使用點標記法取得任何特定的值。

```json
"[resourceGroup().name]"
```

## <a name="steps"></a>steps

傳回指定步驟上的元素。 將步驟的名稱以參數的形式傳遞給此函式。 從傳回的元素中，您可以取得特定屬性值。

若要取得基本步驟中專案的輸出值，請使用 [基本概念 ( # B1 ](#basics) 函數。

下列範例會傳回名為的步驟 `vmParameters` 。 在該步驟中，名為的元素 `adminUsername` 。

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

## <a name="next-steps"></a>接下來的步驟

* 如需開發入口網站介面的簡介，請參閱 [ 適用于 Azure 受控應用程式的建立體驗的CreateUiDefinition.js](create-uidefinition-overview.md)。
