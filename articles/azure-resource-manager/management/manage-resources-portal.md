---
title: 管理資源 - Azure 門戶
description: 使用 Azure 門戶和 Azure 資源管理器管理資源。 演示如何部署和刪除資源。
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 00d260a74807774d5bf226c3ec00a6b84f93b8d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248342"
---
# <a name="manage-azure-resources-by-using-the-azure-portal"></a>使用 Azure 門戶管理 Azure 資源

瞭解如何將 Azure[門戶](https://portal.azure.com)與[Azure 資源管理器](overview.md)一起管理 Azure 資源。 有關管理資源組，請參閱[使用 Azure 門戶管理 Azure 資源組](manage-resource-groups-portal.md)。

有關管理資源的其他文章：

- [使用 Azure CLI 管理 Azure 資源](manage-resources-cli.md)
- [使用 Azure PowerShell 來管理 Azure 資源](manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="deploy-resources-to-a-resource-group"></a>將資源部署到資源組

創建資源管理器範本後，可以使用 Azure 門戶部署 Azure 資源。 有關創建範本，請參閱[快速入門：使用 Azure 門戶創建和部署 Azure 資源管理器範本](../templates/quickstart-create-templates-use-the-portal.md)。 有關使用門戶部署範本，請參閱[使用資源管理器範本和 Azure 門戶部署資源](../templates/deploy-portal.md)。

## <a name="open-resources"></a>開放資源

Azure 資源按 Azure 服務和資源組組織。 以下過程演示如何打開名為**mystorage0207**的存儲帳戶。 虛擬機器駐留在名為**mystorage0207rg**的資源組中。

要按服務類型打開資源：：

1. 登錄到 Azure[門戶](https://portal.azure.com)。
2. 在左側窗格中，選擇 Azure 服務。 在這種情況下，**存儲帳戶**。  如果未看到列出的服務，請選擇 **"所有服務**"，然後選擇服務類型。

    ![在門戶中打開 Azure 資源](./media/manage-resources-portal/manage-azure-resources-portal-open-service.png)

3. 選擇要打開的資源。

    ![在門戶中打開 Azure 資源](./media/manage-resources-portal/manage-azure-resources-portal-open-resource.png)

    存儲帳戶如下所示：

    ![在門戶中打開 Azure 資源](./media/manage-resources-portal/manage-azure-resources-portal-open-resource-storage.png)

要按資源組打開資源：：

1. 登錄到 Azure[門戶](https://portal.azure.com)。
2. 在左側窗格中，選擇 **"資源組**"以列出組中的資源。
3. 選擇要打開的資源。 

## <a name="manage-resources"></a>管理資源

在入口網站中檢視資源時，您會看到用於管理特定資源的選項。

![管理 Azure 資源](./media/manage-resources-portal/manage-azure-resources-portal-manage-resource.png)

螢幕截圖顯示了 Azure 虛擬機器的管理選項。 您可以執行啟動、重新開機和停止虛擬機器等操作。

## <a name="delete-resources"></a>刪除資源

1. 打開門戶中的資源。 有關這些步驟，請參閱[打開資源](#open-resources)。
2. 選擇 **"刪除**"。 以下螢幕截圖顯示了虛擬機器的管理選項。

    ![刪除 Azure 資源](./media/manage-resources-portal/manage-azure-resources-portal-delete-resource.png)
3. 鍵入資源的名稱以確認刪除，然後選擇 **"刪除**"。

有關 Azure 資源管理器如何命令刪除資源的詳細資訊，請參閱 Azure[資源管理器資源組刪除](delete-resource-group.md)。

## <a name="move-resources"></a>移動資源

1. 打開門戶中的資源。 有關這些步驟，請參閱[打開資源](#open-resources)。
2. 選取 [移動]****。 以下螢幕截圖顯示了存儲帳戶的管理選項。

    ![移動 Azure 資源](./media/manage-resources-portal/manage-azure-resources-portal-move-resource.png)
3. 選擇 **"移動到其他資源組****"或"移動到其他訂閱**"，具體取決於您的需要。

如需詳細資訊，請參閱 [將資源移動到新的資源群組或訂用帳戶](move-resource-group-and-subscription.md)。

## <a name="lock-resources"></a>鎖定資源

鎖定可防止組織中的其他使用者意外刪除或修改關鍵資源，如 Azure 訂閱、資源組或資源。 

1. 打開門戶中的資源。 有關這些步驟，請參閱[打開資源](#open-resources)。
2. 選擇**鎖**。 以下螢幕截圖顯示了存儲帳戶的管理選項。

    ![鎖定 Azure 資源](./media/manage-resources-portal/manage-azure-resources-portal-lock-resource.png)
3. 選擇 **"添加**"，然後指定鎖定屬性。

有關詳細資訊，請參閱使用[Azure 資源管理器鎖定資源](lock-resources.md)。

## <a name="tag-resources"></a>標記資源

標記有助於以邏輯方式組織資源組和資源。 

1. 打開門戶中的資源。 有關這些步驟，請參閱[打開資源](#open-resources)。
2. 選取 [標記]****。 以下螢幕截圖顯示了存儲帳戶的管理選項。

    ![標記 azure 資源](./media/manage-resources-portal/manage-azure-resources-portal-tag-resource.png)
3. 指定標記屬性，然後選擇 **"保存**"。

有關詳細資訊，請參閱[使用標記來組織 Azure 資源](tag-resources.md#portal)。

## <a name="monitor-resources"></a>監視資源

打開資源時，門戶會顯示用於監視該資源類型的預設圖形和表。 以下螢幕截圖顯示了虛擬機器的圖形：

![監視 Azure 資源](./media/manage-resources-portal/manage-azure-resources-portal-monitor-resource.png)

您可以選擇圖形右上角的針腳圖示，將圖形固定到儀表板。 若要了解如何使用儀表板，請參閱 [Creating and sharing dashboards in the Azure portal (在 Azure 入口網站建立和共用儀表板)](../../azure-portal/azure-portal-dashboards.md)。

## <a name="manage-access-to-resources"></a>管理對資源的訪問

[基於角色的存取控制 （RBAC）](../../role-based-access-control/overview.md)是管理對 Azure 中資源的訪問的方式。 如需詳細資料，請參閱[使用 RBAC 和 Azure 入口網站來管理存取權](../../role-based-access-control/role-assignments-portal.md)。

## <a name="next-steps"></a>後續步驟

- 要瞭解 Azure 資源管理器，請參閱[Azure 資源管理器概述](overview.md)。
- 要瞭解資源管理器範本語法，請參閱瞭解[Azure 資源管理器範本的結構和語法](../templates/template-syntax.md)。
- 要瞭解如何開發範本，請參閱分[步教程](/azure/azure-resource-manager/)。
- 要查看 Azure 資源管理器範本架構，請參閱[範本引用](/azure/templates/)。
