---
title: 使用 Azure 門戶為數據訪問分配 RBAC 角色
titleSuffix: Azure Storage
description: 瞭解如何使用 Azure 門戶將許可權分配給具有基於角色的存取控制 （RBAC） 的 Azure 活動目錄安全主體。 Azure 存儲支援內置和自訂 RBAC 角色，以便通過 Azure AD 進行身份驗證。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/10/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: ec32990513d9199c4aaccf1bcfcbf76f348f877b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75867510"
---
# <a name="use-the-azure-portal-to-assign-an-rbac-role-for-access-to-blob-and-queue-data"></a>使用 Azure 門戶分配 RBAC 角色以訪問 Blob 和佇列資料

Azure Active Directory (Azure AD) 會透過[角色型存取控制 (RBAC)](../../role-based-access-control/overview.md)，來授與存取受保護資源的權限。 Azure 存儲定義一組內置 RBAC 角色，這些角色包含用於訪問 Blob 或佇列資料的常見許可權集。

將 RBAC 角色指派給 Azure AD 安全主體時，Azure 會授予對該安全主體的這些資源的存取權限。 存取權的範圍可以包括訂用帳戶、資源群組、儲存體帳戶或個別的容器或佇列層級。 Azure AD 安全主體可以是 Azure 資源的使用者、組、應用程式服務主體或[託管標識](../../active-directory/managed-identities-azure-resources/overview.md)。

本文介紹如何使用 Azure 門戶分配 RBAC 角色。 Azure 門戶提供了一個簡單的介面，用於分配 RBAC 角色和管理對存儲資源的訪問。 您還可以使用 Azure 命令列工具或 Azure 存儲管理 API 為 Blob 和佇列資源配置 RBAC 角色。 有關存儲資源的 RBAC 角色的詳細資訊，請參閱[使用 Azure 活動目錄 對 Azure blob 和佇列進行身份驗證訪問](storage-auth-aad.md)。 

## <a name="rbac-roles-for-blobs-and-queues"></a>適用於 Blob 和佇列的 RBAC 角色

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>確定資源範圍 

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="assign-rbac-roles-using-the-azure-portal"></a>使用 Azure 門戶分配 RBAC 角色

確定角色指派的適當範圍後，在 Azure 門戶中導航到該資源。 顯示資源的**存取控制 （IAM）** 設置，並按照以下說明管理角色指派：

1. 分配適當的 Azure 存儲 RBAC 角色以授予對 Azure AD 安全主體的存取權限。

1. 將 Azure 資源管理器[讀取器](../../role-based-access-control/built-in-roles.md#reader)角色指派給需要使用 Azure AD 憑據通過 Azure 門戶訪問容器或佇列的使用者。 

以下各節將更詳細地介紹這些步驟。

> [!NOTE]
> 身為 Azure 儲存體帳戶擁有者的您，並不會自動收到指派的資料存取權限。 您必須明確地將 Azure 儲存體的 RBAC 角色指派給自己。 您可以在訂用帳戶、資源群組、儲存體帳戶或容器/佇列層級上指派此角色。
>
> 如果存儲帳戶啟用了階層命名空間，則無法將範圍為容器或佇列的角色指派。

### <a name="assign-a-built-in-rbac-role"></a>分配內置 RBAC 角色

在將角色指派給安全主體之前，請務必考慮要授予的許可權的範圍。 查看["確定資源範圍](#determine-resource-scope)"部分以決定適當的範圍。

這裡顯示的程序會對容器範圍指派角色，但是您可以依照相同的步驟，對佇列範圍指派角色： 

1. 在[Azure 門戶](https://portal.azure.com)中，轉到存儲帳戶並顯示帳戶的 **"概述**"。
1. 在 [服務] 下，選取 [Blob]****。 
1. 找出您要為其指派角色的容器，並顯示容器的設定。 
1. 選取 [存取控制 (IAM)]****，以顯示容器的存取控制設定。 選取 [角色指派]**** 索引標籤，以查看角色指派的清單。

    ![顯示容器存取控制設置的螢幕截圖](media/storage-auth-aad-rbac-portal/portal-access-control-for-storage.png)

1. 按一下 [新增角色指派]**** 按鈕新增角色。
1. 在 **"添加角色指派"** 視窗中，選擇要分配的 Azure 存儲角色。 然後搜索以查找要為其分配該角色的安全主體。

    ![顯示如何指派 RBAC 角色的螢幕擷取畫面](media/storage-auth-aad-rbac-portal/add-rbac-role.png)

1. 按一下 [儲存]****。 您對其指派角色的身分識別會出現在該角色下方。 如下圖所示，新增的使用者現在有權對名為 sample-container** 容器讀取資料。

    ![顯示分配給角色的使用者清單的螢幕截圖](media/storage-auth-aad-rbac-portal/container-scoped-role.png)

您可以按照類似的步驟為存儲帳戶、資源組或訂閱分配範圍的角色。

### <a name="assign-the-reader-role-for-portal-access"></a>為門戶訪問分配讀取器角色

將 Azure 存儲的內置或自訂角色指派給安全主體時，將授予該安全主體對存儲帳戶中的資料執行操作的許可權。 內置**資料讀取器**角色為容器或佇列中的資料提供讀取權限，而內置**資料參與者**角色為容器或佇列提供讀取、寫入和刪除許可權。 許可權範圍為指定的資源。  
例如，如果將存儲 Blob**資料參與者**角色指派給名為**示例容器**的容器級別的使用者 Mary，則 Mary 被授予對該容器中所有 Blob 的讀取、寫入和刪除存取權限。

但是，如果 Mary 想在 Azure 門戶中查看 Blob，則**存儲 Blob 資料參與者**角色本身不會提供足夠的許可權，以便通過門戶導航到 Blob 以查看它。 需要其他 Azure AD 許可權才能在門戶中導航並查看那裡可見的其他資源。

如果使用者需要能夠訪問 Azure 門戶中的 Blob，則在存儲帳戶或以上級別向這些使用者分配額外的 RBAC 角色，[即 Reader](../../role-based-access-control/built-in-roles.md#reader)角色。 **Reader**角色是 Azure 資源管理器角色，允許使用者查看存儲帳戶資源，但不能修改它們。 它不為 Azure 存儲中的資料提供讀取權限，而只向帳戶管理資源提供讀取權限。

按照以下步驟分配**Reader**角色，以便使用者可以從 Azure 門戶訪問 Blob。 在此示例中，分配範圍為存儲帳戶：

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至您的儲存體帳戶。
1. 選取 [存取控制 (IAM)]****，以顯示儲存體帳戶的存取控制設定。 選取 [角色指派]**** 索引標籤，以查看角色指派的清單。
1. 在 **"添加角色指派"** 視窗中，選擇 **"讀取器"** 角色。 
1. 在 [存取權指派對象為]**** 欄位中，選取 [Azure AD 使用者、群組或服務主體]****。
1. 搜索以查找要為其分配角色的安全主體。
1. 儲存角色指派。

僅對於需要使用 Azure 門戶訪問 Blob 或佇列的使用者，才有必要分配**Reader**角色。

> [!IMPORTANT]
> Azure 門戶中的存儲資源管理器預覽版本不支援使用 Azure AD 憑據查看和修改 Blob 或佇列資料。 Azure 門戶中的存儲資源管理器始終使用帳戶金鑰訪問資料。 要在 Azure 門戶中使用存儲資源管理器，必須為角色指派包括**Microsoft.存儲/存儲帳戶/清單鍵/操作**。

## <a name="next-steps"></a>後續步驟

- 有關存儲資源的 RBAC 角色的詳細資訊，請參閱[使用 Azure 活動目錄 對 Azure blob 和佇列進行身份驗證訪問](storage-auth-aad.md)。 
- 若要深入了解 RBAC，請參閱[什麼是角色型存取控制 (RBAC)？](../../role-based-access-control/overview.md)。
- 若要了解如何使用 Azure PowerShell、Azure CLI 或 REST API 來指派和管理 RBAC 角色指派，請參閱下列文章：
    - [使用 Azure PowerShell 管理角色型存取控制 (RBAC)](../../role-based-access-control/role-assignments-powershell.md)
    - [使用 Azure CLI 管理角色型存取控制 (RBAC)](../../role-based-access-control/role-assignments-cli.md)
    - [使用 REST API 管理角色型存取控制 (RBAC)](../../role-based-access-control/role-assignments-rest.md)
- 若要深入了解如何從儲存體應用程式內授權容器和佇列的存取權，請參閱[使用 Azure AD 與 Azure 儲存體應用程式](storage-auth-aad-app.md)。
