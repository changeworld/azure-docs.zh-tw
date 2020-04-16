---
title: 新增和刪除管理單位(預覽) - Azure 活動目錄 |微軟文件
description: 使用管理單位限制 Azure 活動目錄中的角色權限範圍
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 351cbee9cbc7cc02c7f20553b6fc8c3f861541df
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428156"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>管理 Azure 活動目錄中的管理單位

要在 Azure 活動目錄 (Azure AD) 中進行更精細的管理控制,可以將使用者分配給範圍僅限於一個或多個管理單位 (A) 的 Azure AD 角色。

## <a name="getting-started"></a>開始使用

1. 要透過[圖形資源管理員](https://aka.ms/ge)從以下說明執行查詢,請確保執行以下操作:

    1. 轉到門戶中的 Azure AD,然後在應用程式中選擇圖形資源管理器,並提供圖形資源管理員的管理員同意。

        ![選擇圖形資源管理員並在此頁面上提供管理員同意](./media/roles-admin-units-manage/select-graph-explorer.png)

    1. 在圖形資源管理器中,請確保選擇測試版。

        ![在 POST 操作之前選擇測試版](./media/roles-admin-units-manage/select-beta-version.png)

1. 請使用 Azure AD 電源外殼的預覽版本。 詳細說明在這裡。

## <a name="add-an-administrative-unit"></a>新增管理單位

### <a name="azure-portal"></a>Azure 入口網站

1. 轉到門戶中的"活動目錄",然後選擇左側面板中的"管理單位"。

    ![瀏覽到 Azure 活動目錄中的管理單位](./media/roles-admin-units-manage/nav-to-admin-units.png)

1. 選擇 **「新增**」 並提供管理單位的名稱,可以選擇為管理單位添加說明。

    ![選擇「新增」,然後輸入管理單位的名稱](./media/roles-admin-units-manage/add-new-admin-unit.png)

1. 選擇 **「新增」** 以最終確定管理單元。

### <a name="powershell"></a>PowerShell

在試著執行以下操作之前,請安裝 Azure AD 電源外殼(預覽版本):

    Connect-AzureAD
    New-AzureADAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"

可以根據需要修改上面突出顯示的值。

### <a name="microsoft-graph"></a>Microsoft Graph

    Http Request
    POST /administrativeUnits
    Request body
    {
        "displayName": "North America Operations",
        "description": "North America Operations administration"
    }

## <a name="remove-an-administrative-unit"></a>移除管理單位

在 Azure 活動目錄 (Azure AD) 中,可以刪除不再需要作為管理角色範圍單元的管理員單元。

### <a name="azure-portal"></a>Azure 入口網站

跳到**Azure AD >门户中的管理单位**。 選擇要刪除的管理單位,然後選擇 **「刪除**」。。 確認**為「是」** 後,行政單位將被刪除。

![選擇要移除的管理單位](./media/roles-admin-units-manage/select-admin-unit-to-delete.png)

### <a name="powershell"></a>PowerShell

    $delau = Get-AzureADAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
    Remove-AzureADAdministrativeUnit -ObjectId $delau.ObjectId

突出顯示的部分可以根據需要更改特定環境。

### <a name="graph-api"></a>圖形 API

    HTTP request
    DELETE /administrativeUnits/{Admin id}
    Request body
    {}

## <a name="next-steps"></a>後續步驟

[管理行政單位](roles-admin-units-add-manage-users.md)
[管理群組](roles-admin-units-add-manage-groups.md)中的使用者
