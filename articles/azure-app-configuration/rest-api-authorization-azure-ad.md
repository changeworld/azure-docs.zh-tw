---
title: Azure 應用程式組態 REST API Azure Active Directory 授權
description: 使用 Azure Active Directory 針對使用 REST API 的 Azure 應用程式組態進行授權
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: bebab7c06062726f7b5c7868f984cadda3b4c98e
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "93423940"
---
# <a name="azure-active-directory-authorization---rest-api-reference"></a>Azure Active Directory 授權-REST API 參考

使用 Azure Active Directory (Azure AD) authentication 時，授權是由 Azure 角色型存取控制 (RBAC) 處理。 Azure RBAC 需要將使用者指派給角色，才能授與資源的存取權。 每個角色都包含一組指派給角色的使用者可以執行的動作。

## <a name="roles"></a>角色

下列角色是 Azure 訂用帳戶中預設可用的內建角色：

- **Azure 應用程式組態資料擁有** 者：此角色會提供所有作業的完整存取權。
- **Azure 應用程式組態資料讀取器** ：此角色會啟用讀取作業。

## <a name="actions"></a>動作

角色包含指派給該角色的使用者可以執行的動作清單。 Azure 應用程式組態支援下列動作：

- `Microsoft.AppConfiguration/configurationStores/keyValues/read`：此動作可讓您讀取應用程式設定的索引鍵/值資源，例如/kv 和/labels。
- `Microsoft.AppConfiguration/configurationStores/keyValues/write`：此動作可讓您對應用程式設定的索引鍵/值資源進行寫入存取。
- `Microsoft.AppConfiguration/configurationStores/keyValues/delete`：此動作可讓您刪除應用程式設定的索引鍵/值資源。 請注意，刪除資源會傳回已刪除的索引鍵/值。

## <a name="errors"></a>Errors

```http
HTTP/1.1 403 Forbidden
```

**原因：** 提出要求的主體沒有執行所要求作業的必要許可權。
**解決方案：** 將執行要求的作業所需的角色指派給提出要求的主體。

## <a name="managing-role-assignments"></a>管理角色指派

管理角色指派是使用 [AZURE RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview) 程式來完成，這些程式在所有 azure 服務中都是標準的。 您可以透過 Azure CLI、PowerShell、Azure 入口網站等來執行此動作。 您可以在 [這裡](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)找到如何進行角色指派的官方檔。
