---
title: 使用 Azure 入口網站列出 Azure 資源的拒絕指派
description: 瞭解如何使用 Azure 入口網站，列出已被拒絕存取特定範圍的特定 Azure 資源動作的使用者、群組、服務主體和受控識別。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/10/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 4db76e5c6191457346ca1f95678cf73843334d3b
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137432"
---
# <a name="list-deny-assignments-for-azure-resources-using-the-azure-portal"></a>使用 Azure 入口網站列出 Azure 資源的拒絕指派

[拒絕指派](deny-assignments.md)會封鎖使用者執行特定的 Azure 資源動作，即使角色指派授予他們存取權也一樣。 本文說明如何使用 Azure 入口網站列出拒絕指派。

> [!NOTE]
> 您無法直接建立自己的拒絕指派。 如需如何建立拒絕指派的相關資訊，請參閱[拒絕指派](deny-assignments.md)。

## <a name="prerequisites"></a>Prerequisites

若要取得拒絕指派的相關資訊，您必須具有：

- `Microsoft.Authorization/denyAssignments/read` 的許可權，包含在[Azure 資源的大部分內建角色](built-in-roles.md)中。

## <a name="list-deny-assignments"></a>列出拒絕指派

請遵循下列步驟來列出訂用帳戶或管理群組範圍的拒絕指派。

1. 在 Azure 入口網站中，按一下 [所有服務]，然後按 [管理群組] 或 [訂用帳戶]。

1. 按一下您要列出的管理群組或訂用帳戶。

1. 按一下 [存取控制 (IAM)]。

1. 按一下 [拒絕指派] 索引標籤 (或是按一下 [檢視拒絕指派] 動態磚上的 [檢視] 按鈕)。

    若此範圍中有任何拒絕指派，或有任何拒絕指派繼承到此範圍，它們將會被列出。

    ![存取控制︰[拒絕指派] 索引標籤](./media/deny-assignments-portal/access-control-deny-assignments.png)

1. 若要顯示其他資料行，請按一下 [編輯資料行]。

    ![拒絕指派：資料行](./media/deny-assignments-portal/deny-assignments-columns.png)

    |  |  |
    | --- | --- |
    | **名稱** | 拒絕指派的名稱。 |
    | **主體類型** | 使用者、群組、系統定義的群組或服務主體。 |
    | **拒絕**  | 拒絕指派中涵蓋的安全性主體名稱。 |
    | **Id** | 拒絕指派的唯一識別碼。 |
    | **已排除主體** | 是否有拒絕指派排除在外的安全性主體。 |
    | **不會套用至子系** | 拒絕指派是否已繼承到子範圍內。 |
    | **受保護的系統** | 拒絕指派是否由 Azure 管理。 目前都是。 |
    | **範圍** | 管理群組、訂用帳戶、資源群組或資源。 |

1. 新增核取記號至任何已啟用的項目中，然後按一下 [確定] 顯示選取的資料行。

## <a name="list-details-about-a-deny-assignment"></a>列出拒絕指派的詳細資料

請遵循下列步驟來列出拒絕指派的其他詳細資料。

1. 如上一節所述，開啟 [拒絕指派] 窗格。

1. 按一下拒絕指派的名稱來開啟 [使用者] 刀鋒視窗。

    ![拒絕指派：使用者](./media/deny-assignments-portal/deny-assignment-users.png)

    [使用者] 刀鋒視窗包含下列兩個區段。

    |  |  |
    | --- | --- |
    | **拒絕指派會套用到**  | 拒絕指派套用的安全性主體。 |
    | **拒絕指派不包括** | 拒絕指派排除在外的安全性主體。 |

    **系統定義的主體**代表 Azure AD 目錄中的所有使用者、群組、服務主體及受控身分識別。

1. 若要查看被拒絕的權限清單，請按一下 [拒絕的權限]。

    ![拒絕指派：拒絕的權限](./media/deny-assignments-portal/deny-assignment-denied-permissions.png)

    | 動作類型 | 描述 |
    | --- | --- |
    | **動作**  | 拒絕的管理作業。 |
    | **NotActions** | 從拒絕的管理作業中排除的管理作業。 |
    | **DataActions**  | 拒絕的資料作業。 |
    | **NotDataActions** | 從拒絕的資料作業中排除的資料作業。 |

    若是先前螢幕擷取畫面所顯示的範例中，以下為有效的權限：

    - 資料層的所有儲存作業都會遭到拒絕，但計算作業除外。

1. 若要查看拒絕指派的屬性，請按一下 [屬性]。

    ![拒絕指派：屬性](./media/deny-assignments-portal/deny-assignment-properties.png)

    在 [屬性] 刀鋒視窗中，您可以看到拒絕指派名稱、ID、說明及範圍。 **不會套用至子系**參數會指示拒絕指派是否已繼承到子範圍內。 **受保護的系統**參數會指示此拒絕指派是否由 Azure 管理。 目前在所有情況下都**是**。

## <a name="next-steps"></a>後續步驟

* [了解 Azure 資源的拒絕指派](deny-assignments.md)
* [使用 Azure PowerShell 列出 Azure 資源的拒絕指派](deny-assignments-powershell.md)
