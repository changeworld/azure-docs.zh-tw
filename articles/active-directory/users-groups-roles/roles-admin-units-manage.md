---
title: 新增和刪除管理單位(預覽) - Azure 活動目錄 |微軟文件
description: 使用管理單位限制 Azure 活動目錄中的角色許可權範圍。
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
ms.openlocfilehash: 7a4a0dfaeda18b3f68ddc3c7cc7333b8c994d174
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684905"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>管理 Azure 活動目錄中的管理單位

要在 Azure 活動目錄 (Azure AD) 中進行更精細的管理控制,可以將使用者分配給範圍為一個或多個管理單位 (A) 的 Azure AD 角色。

## <a name="get-started"></a>開始使用

1. 要透過[圖形資源管理員](https://aka.ms/ge)從以下說明執行查詢,執行以下操作:

    a. 在 Azure 入口網站中，移至 Azure AD。 在應用程式清單中,選擇**圖形資源管理員**,然後選擇**授予管理員同意圖形資源管理員**。

    ![顯示指向「授予管理員同意」連結的螢幕截圖](./media/roles-admin-units-manage/select-graph-explorer.png)

    b. 在圖形資源管理員中,選擇**測試版**。

    ![顯示選取的測試版的螢幕擷圖](./media/roles-admin-units-manage/select-beta-version.png)

1. 使用 Azure AD 電源外殼的預覽版本。

## <a name="add-an-administrative-unit"></a>新增管理單位

### <a name="use-the-azure-portal"></a>使用 Azure 入口網站

1. 在 Azure 門戶中,轉到 Azure AD,然後在左側窗格中選擇 **「管理單位**」。。

    ![Azure AD 中管理單元(預覽)連結的螢幕截圖](./media/roles-admin-units-manage/nav-to-admin-units.png)

1. 選擇 **'添加**",然後輸入管理單位的名稱。 或者,添加管理單位的說明。

    ![輸入管理單位名稱的「新增」按鈕與文字框的螢幕截圖](./media/roles-admin-units-manage/add-new-admin-unit.png)

1. 選擇 **「新增」** 以最終確定管理單元。

### <a name="use-powershell"></a>使用 PowerShell

在試著執行以下指令之前,請安裝 Azure AD 電源外殼(預覽):

    Connect-AzureAD
    New-AzureADAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"

您可以根據需要修改引號中包含的值。

### <a name="use-microsoft-graph"></a>使用微軟圖形

    Http Request
    POST /administrativeUnits
    Request body
    {
        "displayName": "North America Operations",
        "description": "North America Operations administration"
    }

## <a name="remove-an-administrative-unit"></a>移除管理單位

在 Azure AD 中,可以刪除不再需要作為管理角色範圍單元的管理單位。

### <a name="use-the-azure-portal"></a>使用 Azure 入口網站

1. 在 Azure 門戶中,轉到**Azure AD** > **管理單位**。 
1. 選擇要刪除的管理單位,然後選擇 **「刪除**」。。 
1. 要確認要刪除管理單位,請選擇"**是**"。 行政單位將被刪除。

![管理單位移除按鈕與確認視窗的螢幕擷取](./media/roles-admin-units-manage/select-admin-unit-to-delete.png)

### <a name="use-powershell"></a>使用 PowerShell

    $delau = Get-AzureADAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
    Remove-AzureADAdministrativeUnit -ObjectId $delau.ObjectId

您可以根據需要修改引號中包含的值。

### <a name="use-the-graph-api"></a>使用圖形 API

    HTTP request
    DELETE /administrativeUnits/{Admin id}
    Request body
    {}

## <a name="next-steps"></a>後續步驟

* [管理管理儲存的使用者](roles-admin-units-add-manage-users.md)
* [管理管理儲存中](roles-admin-units-add-manage-groups.md)
