---
title: Azure 應用程式組態 REST API Azure Active Directory 授權
description: 使用 Azure Active Directory，以使用 REST API 的 Azure 應用程式組態進行授權
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: f144c1f09f87e0b915daf86ba0391c2934e60095
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932671"
---
# <a name="azure-active-directory-authorization---rest-api-reference"></a>Azure Active Directory 授權-REST API 參考

當您使用 Azure Active Directory (Azure AD) 驗證時，授權是由角色型存取控制 (RBAC) 處理。 RBAC 需要將使用者指派給角色，才能授與資源的存取權。 每個角色都包含一組指派給角色的使用者可以執行的動作。

## <a name="roles"></a>角色

Azure 訂用帳戶中預設會提供下列角色：

- **Azure 應用程式組態資料擁有** 者：此角色會提供所有作業的完整存取權。
- **Azure 應用程式組態資料讀取器**：此角色會啟用讀取作業。

## <a name="actions"></a>動作

角色包含指派給該角色的使用者可以執行的動作清單。 Azure 應用程式組態支援下列動作：

- `Microsoft.AppConfiguration/configurationStores/keyValues/read`：此動作可讓您讀取應用程式設定的索引鍵/值資源，例如/kv 和/labels。
- `Microsoft.AppConfiguration/configurationStores/keyValues/write`：此動作可讓您對應用程式設定的索引鍵/值資源進行寫入存取。
- `Microsoft.AppConfiguration/configurationStores/keyValues/delete`：此動作可讓您刪除應用程式設定的索引鍵/值資源。 請注意，刪除資源會傳回已刪除的索引鍵/值。

## <a name="error"></a>錯誤

```http
HTTP/1.1 403 Forbidden
```

**原因：** 提出要求的主體沒有執行所要求作業的必要許可權。
**解決方案：** 將執行要求的作業所需的角色指派給提出要求的主體。

## <a name="managing-role-assignments"></a>管理角色指派

您可以使用在所有 Azure 服務中都是標準的 [RBAC 程式](../role-based-access-control/overview.md) 來管理角色指派。 您可以透過 Azure CLI、PowerShell 和 Azure 入口網站來執行此動作。 如需詳細資訊，請參閱 [使用 Azure 入口網站新增或移除 Azure 角色指派](../role-based-access-control/role-assignments-portal.md)。