---
title: 使用適用於 StorSimple 的角色型存取控制 | Microsoft Docs
description: 說明如何在 StorSimple 內容中使用 Azure 角色型存取控制 (Azure RBAC) 。
services: storsimple
documentationcenter: ''
author: alkohli
manager: jconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/11/2017
ms.author: alkohli
ms.openlocfilehash: 38500edeca2241bfa9ab093e037af18159994b02
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87920394"
---
# <a name="role-based-access-control-for-storsimple"></a>適用於 StorSimple 的角色型存取控制

本文提供 Azure 角色型存取控制 (Azure RBAC) 如何用於 StorSimple 裝置的簡短描述。 RBAC 可提供細部的 Azure 存取管理能力。 使用 RBAC 授與 StorSimple 使用者適量的存取權以執行其工作，而非讓所有人不受限制地存取。 如需有關 Azure 存取權管理的基本資訊，請參閱[在 Azure 入口網站中開始使用角色型存取控制](../role-based-access-control/overview.md)。

本文僅適用於 Azure 入口網站中執行 3.0 更新版本或以上版本的 StorSimple 8000 系列裝置。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-roles-for-storsimple"></a>適用于 StorSimple 的 Azure 角色

您可以根據角色指派 RBAC。 該角色會以環境中可用的資源作為基礎來確保特定權限等級。 StorSimple 使用者可以選擇兩種角色類型：內建或自訂。

* **內建角色** - 內建角包可能是擁有者、參與者、讀者或使用者存取管理員。 如需詳細資訊，請參閱 [Azure 角色型存取控制的內建角色](../role-based-access-control/built-in-roles.md)。

* **自訂角色** -如果內建角色不符合您的需求，您可以建立適用于 StorSimple 的 Azure 自訂角色。 若要建立 Azure 自訂角色，請從內建角色開始，進行編輯，然後將它重新匯入到環境中。 系統會使用 Azure PowerShell 或 Azure CLI 管理角色的上傳和下載。 如需詳細資訊，請參閱[建立角色型存取控制的自訂角色](../role-based-access-control/custom-roles.md)。

若要在 Azure 入口網站中檢視適用於 StorSimple 裝置使用者的不同角色，請移至 StorSimple 裝置管理員服務，然後至 [存取控制 (IAM)] > [角色]****。


## <a name="create-a-custom-role-for-storsimple-infrastructure-administrator"></a>建立適用於 StorSimple 基礎結構管理員的自訂角色

以下範例會從內建角色的**讀者**開始，該角色可讓使用者檢視所有資源範圍，但無法編輯或建立新資源。 然後我們會擴充此角色，以建立新的自訂角色 StorSimple 基礎結構管理員。此角色會指派給可管理 StorSimple 裝置基礎結構的使用者。

1. 以系統管理理員身分執行 Windows PowerShell。

2. 登入 Azure。

    `Connect-AzAccount`

3. 在電腦上將讀者角色匯出為 JSON 範本。

    ```powershell
    Get-AzRoleDefinition -Name "Reader"

    Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\ssrbaccustom.json
    ```

4. 在 Visual Studio 中開啟 JSON 檔案。 您會看到一般的 Azure 角色是由三個主要區段、 **動作**、 **NotActions**和 **>assignablescopes**所組成。

    在 [Actions]**** 區段中，會列出此角色所有允許的作業。 每個動作都是從資源提供者加以指派。 如果是 StorSimple 基礎結構管理員，請使用 `Microsoft.StorSimple` 資源提供者。

    使用 PowerShell 可查看所有可用且已在訂用帳戶中登錄的資源提供者。

    `Get-AzResourceProvider`

    您也可以檢查所有可用的 PowerShell Cmdlet 以管理資源提供者。

    在 **NotActions** 區段中，會列出特定 Azure 角色的所有限制動作。 在此範例中，沒有動作受到限制。
    
    在 [AssignableScopes]**** 下，會列出訂用帳戶識別碼。 請確定 Azure 角色包含使用它的明確訂用帳戶識別碼。 如果未指定正確的訂用帳戶識別碼，系統不會讓您匯入訂用帳戶中的角色。

    編輯檔案時，請記住上述考量事項。

    ```json
    {
        "Name":  "StorSimple Infrastructure Admin",
        "Id":  "<guid>",
        "IsCustom":  true,
        "Description":  "Lets you view everything, but not make any changes except for Clear alerts, Clear settings, install, download etc.",
        "Actions":  [
                        "Microsoft.StorSimple/managers/alerts/read",
                        "Microsoft.StorSimple/managers/devices/volumeContainers/read",
                        "Microsoft.StorSimple/managers/devices/jobs/read",
                        "Microsoft.StorSimple/managers/devices/alertSettings/read",
                        "Microsoft.StorSimple/managers/devices/alertSettings/write",
                        "Microsoft.StorSimple/managers/clearAlerts/action",
                        "Microsoft.StorSimple/managers/devices/networkSettings/read",
                        "Microsoft.StorSimple/managers/devices/publishSupportPackage/action",
                        "Microsoft.StorSimple/managers/devices/scanForUpdates/action",
                        "Microsoft.StorSimple/managers/devices/metrics/read"

                    ],
        "NotActions":  [

                    ],
        "AssignableScopes":  [
                                "/subscriptions/<subscription_ID>/"
                            ]
    }
    ```

6. 將 Azure 自訂角色匯入回環境。

    `New-AzRoleDefinition -InputFile "C:\ssrbaccustom.json"`


此角色現在應該會顯示於 [存取控制]**** 刀鋒視窗中的角色清單。

![查看 Azure 角色](./media/storsimple-8000-role-based-access-control/rbac-role-types.png)

如需詳細資訊，請移至[自訂角色](../role-based-access-control/custom-roles.md)。

### <a name="sample-output-for-custom-role-creation-via-the-powershell"></a>透過 PowerShell 建立自訂角色的範例輸出

```powershell
Connect-AzAccount
```

```Output
Environment           : AzureCloud
Account               : john.doe@contoso.com
TenantId              : <tenant_ID>
SubscriptionId        : <subscription_ID>
SubscriptionName      : Internal Consumption
CurrentStorageAccount :
```

```powershell
Get-AzRoleDefinition -Name "Reader"
```

```Output
Name             : Reader
Id               : <guid>
IsCustom         : False
Description      : Lets you view everything, but not make any changes.
Actions          : {*/read}
NotActions       : {}
AssignableScopes : {/}
```

```powershell
Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\ssrbaccustom.json
New-AzRoleDefinition -InputFile "C:\ssrbaccustom.json"
```

```Output
Name             : StorSimple Infrastructure Admin
Id               : <tenant_ID>
IsCustom         : True
Description      : Lets you view everything, but not make any changes except for Clear alerts, Clear settings, install,
                   download etc.
Actions          : {Microsoft.StorSimple/managers/alerts/read,
                   Microsoft.StorSimple/managers/devices/volumeContainers/read,
                   Microsoft.StorSimple/managers/devices/jobs/read,
                   Microsoft.StorSimple/managers/devices/alertSettings/read...}
NotActions       : {}
AssignableScopes : {/subscriptions/<subscription_ID>/}
```

## <a name="add-users-to-the-custom-role"></a>將使用者新增至自訂角色

您可從角色指派範圍內的資源、資源群組或訂用帳戶授與存取權。 提供存取權時，請記住子節點將繼承授與父節點的存取權。 如需詳細資訊，請移至 [azure 角色型存取控制 (AZURE RBAC) ](../role-based-access-control/overview.md)。

1. 移至 [ **存取控制] (IAM) **。 在 [存取控制] 刀鋒視窗上，按一下 [+ 新增]****。

    ![新增 Azure 角色的存取權](./media/storsimple-8000-role-based-access-control/rbac-add-role.png)

2. 選取要指派的角色，在例中為 [StorSimple 基礎結構管理員]****。

3. 選取目錄中您要授與存取權的使用者、群組或應用程式。 您可以使用顯示名稱、電子郵件地址和物件識別碼來搜尋目錄。

4. 選取 [儲存]**** 以建立指派。

    ![將許可權新增至 Azure 角色](./media/storsimple-8000-role-based-access-control/rbac-create-role-infra-admin.png)

[正在新增使用者]**** 通知會追蹤進度。 成功新增使用者之後，系統會更新存取控制中的使用者清單。

## <a name="view-permissions-for-the-custom-role"></a>檢視自訂角色的權限

建立此角色後，您可以在 Azure 入口網站中檢視與此角色相關聯的權限。

1. 若要查看與此角色相關聯的許可權，請移至 [ **存取控制] (IAM) > 角色 > StorSimple 基礎結構管理員**。此角色中的使用者清單隨即顯示。

2. 選取 [StorSimple 基礎結構管理員使用者]，並按一下 [權限]****。

    ![檢視 StorSimple 基礎結構管理員角色的權限](./media/storsimple-8000-role-based-access-control/rbac-roles-view-permissions.png)

3. 系統會顯示此角色相關聯的權限。

    ![檢視 StorSimple 基礎結構管理員角色中的使用者](./media/storsimple-8000-role-based-access-control/rbac-infra-admin-permissions1.png)


## <a name="next-steps"></a>後續步驟

了解如何[為內部及外部使用者指派自訂角色](../role-based-access-control/role-assignments-external-users.md)。
