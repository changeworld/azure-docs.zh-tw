---
title: 使用 Azure 入口網站來指派用於資料存取的 RBAC 角色
titleSuffix: Azure Storage
description: 瞭解如何使用 Azure 入口網站，以角色型存取控制（RBAC）將許可權指派給 Azure Active Directory 的安全性主體。 Azure 儲存體支援內建和 Azure 自訂角色，以透過 Azure AD 進行驗證。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/16/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: e0278329ff6b6946c4107ea048367753eaccbfd8
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2020
ms.locfileid: "87124981"
---
# <a name="use-the-azure-portal-to-assign-an-rbac-role-for-access-to-blob-and-queue-data"></a>使用 Azure 入口網站來指派用於存取 blob 和佇列資料的 RBAC 角色

Azure Active Directory (Azure AD) 會透過[角色型存取控制 (RBAC)](../../role-based-access-control/overview.md)，來授與存取受保護資源的權限。 Azure 儲存體會定義一組內建的 RBAC 角色，其中包含用來存取 blob 或佇列資料的常用許可權集。

當 RBAC 角色指派給 Azure AD 安全性主體時，Azure 會為該安全性主體授與這些資源的存取權。 存取權的範圍可以包括訂用帳戶、資源群組、儲存體帳戶或個別的容器或佇列層級。 Azure AD 的安全性主體可以是使用者、群組、應用程式服務主體，或[適用于 Azure 資源的受控識別](../../active-directory/managed-identities-azure-resources/overview.md)。

本文說明如何使用 Azure 入口網站來指派 RBAC 角色。 Azure 入口網站提供一個簡單的介面，用於指派 RBAC 角色及管理儲存體資源的存取權。 您也可以使用 Azure 命令列工具或 Azure 儲存體管理 Api，為 blob 和佇列資源指派 RBAC 角色。 如需儲存體資源之 RBAC 角色的詳細資訊，請參閱[使用 Azure Active Directory 來驗證 Azure blob 和佇列的存取權](storage-auth-aad.md)。

## <a name="rbac-roles-for-blobs-and-queues"></a>適用於 Blob 和佇列的 RBAC 角色

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>判斷資源範圍

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="assign-rbac-roles-using-the-azure-portal"></a>使用 Azure 入口網站指派 RBAC 角色

在您決定角色指派的適當範圍之後，請流覽至 Azure 入口網站中的該資源。 顯示資源的 [**存取控制（IAM）** ] 設定，並遵循下列指示來管理角色指派：

1. 指派適當的 Azure 儲存體 RBAC 角色，以授與 Azure AD 安全性主體的存取權。

1. 將 Azure Resource Manager[讀取](../../role-based-access-control/built-in-roles.md#reader)者角色指派給需要透過 Azure 入口網站使用其 Azure AD 認證來存取容器或佇列的使用者。 

下列各節會更詳細地說明每個步驟。

> [!NOTE]
> 身為 Azure 儲存體帳戶擁有者的您，並不會自動收到指派的資料存取權限。 您必須明確地將 Azure 儲存體的 RBAC 角色指派給自己。 您可以在訂用帳戶、資源群組、儲存體帳戶或容器/佇列層級上指派此角色。
>
> 如果您的儲存體帳戶已啟用階層命名空間，則您無法指派範圍為容器或佇列的角色。

### <a name="assign-a-built-in-rbac-role"></a>指派內建 RBAC 角色

將角色指派給安全性主體之前，請務必考慮您授與的許可權範圍。 請參閱[判斷資源範圍](#determine-resource-scope)一節，以決定適當的範圍。

這裡顯示的程序會對容器範圍指派角色，但是您可以依照相同的步驟，對佇列範圍指派角色：

1. 在 [ [Azure 入口網站](https://portal.azure.com)中，移至您的儲存體帳戶，並顯示帳戶的**總覽**。
1. 在 [服務] 下，選取 [Blob]****。
1. 找出您要為其指派角色的容器，並顯示容器的設定。
1. 選取 [存取控制 (IAM)]****，以顯示容器的存取控制設定。 選取 [角色指派] 索引標籤，以查看角色指派的清單。

    ![顯示容器存取控制設定的螢幕擷取畫面](media/storage-auth-aad-rbac-portal/portal-access-control-for-storage.png)

1. 按一下 [新增角色指派]**** 按鈕新增角色。
1. 在 [**新增角色指派**] 視窗中，選取您要指派的 Azure 儲存體角色。 然後搜尋以找出您要指派該角色的安全性主體。

    ![顯示如何指派 RBAC 角色的螢幕擷取畫面](media/storage-auth-aad-rbac-portal/add-rbac-role.png)

1. 按一下 [檔案] 。 您對其指派角色的身分識別會出現在該角色下方。 如下圖所示，新增的使用者現在有權對名為 sample-container** 容器讀取資料。

    ![顯示指派給角色的使用者清單的螢幕擷取畫面](media/storage-auth-aad-rbac-portal/container-scoped-role.png)

您可以依照類似的步驟，指派範圍限於儲存體帳戶、資源群組或訂用帳戶的角色。

### <a name="assign-the-reader-role-for-portal-access"></a>指派入口網站存取的讀取者角色

當您將 Azure 儲存體的內建或自訂角色指派給安全性主體時，您會授與許可權給該安全性主體，以對您儲存體帳戶中的資料執行作業。 內建的**資料讀取器**角色提供容器或佇列中資料的讀取權限，而內建的**資料參與者**角色則提供容器或佇列的讀取、寫入和刪除許可權。 許可權的範圍設定為指定的資源。  
例如，如果您將「**儲存體 Blob 資料參與者**」角色指派給容器層級的「使用者 Mary」 **，則**會將該容器中所有 blob 的讀取、寫入和刪除許可權授與 mary。

不過，如果 Mary 想要在 Azure 入口網站中查看 blob，則**儲存體 Blob 資料參與者**角色本身將不會提供足夠的許可權，可透過入口網站流覽至 blob 以進行查看。 需要額外的 Azure AD 許可權才能流覽入口網站，以及查看在該處顯示的其他資源。

如果您的使用者需要能夠存取 Azure 入口網站中的 blob，請在儲存體帳戶層級（或以上）將額外的 RBAC 角色、[讀取](../../role-based-access-control/built-in-roles.md#reader)者角色指派給這些使用者。 「**讀者**」角色是一種 Azure Resource Manager 角色，可讓使用者查看儲存體帳戶資源，但不能加以修改。 它不會提供 Azure 儲存體中資料的讀取權限，而只是用於帳戶管理資源。

請遵循下列步驟來指派「**讀者**」角色，讓使用者可以從 Azure 入口網站存取 blob。 在此範例中，指派的範圍設定為儲存體帳戶：

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至您的儲存體帳戶。
1. 選取 [存取控制 (IAM)]，以顯示儲存體帳戶的存取控制設定。 選取 [角色指派] 索引標籤，以查看角色指派的清單。
1. 在 [**新增角色指派**] 視窗中，選取 [**讀取**者] 角色。 
1. 在 [存取權指派對象為] 欄位中，選取 [Azure AD 使用者、群組或服務主體]。
1. 搜尋以找出您要為其指派角色的安全性主體。
1. 儲存角色指派。

只有需要使用 Azure 入口網站來存取 blob 或佇列的使用者，才需要指派「**讀者**」角色。

> [!IMPORTANT]
> Azure 入口網站中儲存體總管的預覽版本不支援使用 Azure AD 認證來查看和修改 blob 或佇列資料。 Azure 入口網站中的儲存體總管一律會使用帳戶金鑰來存取資料。 若要使用 Azure 入口網站中的儲存體總管，您必須獲指派包含**Microsoft storageAccounts/listkeys/action**的角色。

## <a name="next-steps"></a>後續步驟

- 如需儲存體資源之 RBAC 角色的詳細資訊，請參閱[使用 Azure Active Directory 來驗證 Azure blob 和佇列的存取權](storage-auth-aad.md)。 
- 若要深入瞭解 RBAC，請參閱[什麼是 Azure 角色型存取控制（AZURE RBAC）？](../../role-based-access-control/overview.md)。
- 若要了解如何使用 Azure PowerShell、Azure CLI 或 REST API 來指派和管理 RBAC 角色指派，請參閱下列文章：
    - [使用 Azure PowerShell 管理角色型存取控制 (RBAC)](../../role-based-access-control/role-assignments-powershell.md)
    - [使用 Azure CLI 管理角色型存取控制 (RBAC)](../../role-based-access-control/role-assignments-cli.md)
    - [使用 REST API 管理角色型存取控制 (RBAC)](../../role-based-access-control/role-assignments-rest.md)
- 若要深入了解如何從儲存體應用程式內授權容器和佇列的存取權，請參閱[使用 Azure AD 與 Azure 儲存體應用程式](storage-auth-aad-app.md)。
