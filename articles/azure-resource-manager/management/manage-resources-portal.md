---
title: 管理資源-Azure 入口網站
description: 使用 Azure 入口網站和 Azure Resource Manager 來管理您的資源。 說明如何部署和刪除資源。
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 00d260a74807774d5bf226c3ec00a6b84f93b8d1
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79248342"
---
# <a name="manage-azure-resources-by-using-the-azure-portal"></a>使用 Azure 入口網站來管理 Azure 資源

瞭解如何搭配[Azure Resource Manager](overview.md)使用[Azure 入口網站](https://portal.azure.com)來管理您的 Azure 資源。 如需管理資源群組，請參閱[使用 Azure 入口網站來管理 Azure 資源群組](manage-resource-groups-portal.md)。

關於管理資源的其他文章：

- [使用 Azure CLI 來管理 Azure 資源](manage-resources-cli.md)
- [使用 Azure PowerShell 來管理 Azure 資源](manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="deploy-resources-to-a-resource-group"></a>將資源部署至資源群組

建立 Resource Manager 範本之後，您可以使用 Azure 入口網站來部署您的 Azure 資源。 如需建立範本，請參閱[快速入門：使用 Azure 入口網站建立和部署 Azure Resource Manager 範本](../templates/quickstart-create-templates-use-the-portal.md)。 如需使用入口網站部署範本，請參閱使用[Resource Manager 範本和 Azure 入口網站部署資源](../templates/deploy-portal.md)。

## <a name="open-resources"></a>開啟資源

Azure 資源是依 Azure 服務和資源群組來組織。 下列程式說明如何開啟名為**mystorage0207**的儲存體帳戶。 虛擬機器位於名為**mystorage0207rg**的資源群組中。

若要依服務類型開啟資源：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在左窗格中，選取 [Azure 服務]。 在此情況下為**儲存體帳戶**。  如果您沒有看到列出的服務，請選取 [**所有服務**]，然後選取 [服務類型]。

    ![在入口網站中開啟 azure 資源](./media/manage-resources-portal/manage-azure-resources-portal-open-service.png)

3. 選取您想要開啟的資源。

    ![在入口網站中開啟 azure 資源](./media/manage-resources-portal/manage-azure-resources-portal-open-resource.png)

    儲存體帳戶看起來像這樣：

    ![在入口網站中開啟 azure 資源](./media/manage-resources-portal/manage-azure-resources-portal-open-resource-storage.png)

若要依資源群組開啟資源：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在左窗格中，選取 [**資源群組**] 以列出群組中的資源。
3. 選取您想要開啟的資源。 

## <a name="manage-resources"></a>管理資源

在入口網站中檢視資源時，您會看到用於管理特定資源的選項。

![管理 Azure 資源](./media/manage-resources-portal/manage-azure-resources-portal-manage-resource.png)

螢幕擷取畫面顯示 Azure 虛擬機器的管理選項。 您可以執行啟動、重新開機和停止虛擬機器等作業。

## <a name="delete-resources"></a>刪除資源

1. 在入口網站中開啟資源。 如需相關步驟，請參閱[開啟資源](#open-resources)。
2. 選取 [刪除]。 下列螢幕擷取畫面顯示虛擬機器的管理選項。

    ![刪除 azure 資源](./media/manage-resources-portal/manage-azure-resources-portal-delete-resource.png)
3. 輸入資源的名稱以確認刪除，然後選取 [**刪除**]。

如需 Azure Resource Manager 如何排序資源刪除的詳細資訊，請參閱[Azure Resource Manager 資源群組刪除](delete-resource-group.md)。

## <a name="move-resources"></a>移動資源

1. 在入口網站中開啟資源。 如需相關步驟，請參閱[開啟資源](#open-resources)。
2. 選取 [移動]。 下列螢幕擷取畫面顯示儲存體帳戶的管理選項。

    ![移動 azure 資源](./media/manage-resources-portal/manage-azure-resources-portal-move-resource.png)
3. 選取 **移至另一個資源群組** 或 **移至另一個訂**用帳戶，視您的需求而定

如需詳細資訊，請參閱 [將資源移動到新的資源群組或訂用帳戶](move-resource-group-and-subscription.md)。

## <a name="lock-resources"></a>鎖定資源

鎖定可防止您組織中的其他使用者不小心刪除或修改重要資源，例如 Azure 訂用帳戶、資源群組或資源。 

1. 在入口網站中開啟資源。 如需相關步驟，請參閱[開啟資源](#open-resources)。
2. 選取 [**鎖定**]。 下列螢幕擷取畫面顯示儲存體帳戶的管理選項。

    ![鎖定 azure 資源](./media/manage-resources-portal/manage-azure-resources-portal-lock-resource.png)
3. 選取 [**新增**]，然後指定鎖定屬性。

如需詳細資訊，請參閱[使用 Azure Resource Manager 來鎖定資源](lock-resources.md)。

## <a name="tag-resources"></a>標記資源

標記可協助您以邏輯方式組織資源群組和資源。 

1. 在入口網站中開啟資源。 如需相關步驟，請參閱[開啟資源](#open-resources)。
2. 選取 [標記]。 下列螢幕擷取畫面顯示儲存體帳戶的管理選項。

    ![標記 azure 資源](./media/manage-resources-portal/manage-azure-resources-portal-tag-resource.png)
3. 指定標記屬性，然後選取 [**儲存**]。

如需相關資訊，請參閱[使用標記來組織您的 Azure 資源](tag-resources.md#portal)。

## <a name="monitor-resources"></a>監視資源

當您開啟資源時，入口網站會顯示預設圖形和資料表，以便監視該資源類型。 下列螢幕擷取畫面顯示虛擬機器的圖形：

![監視 azure 資源](./media/manage-resources-portal/manage-azure-resources-portal-monitor-resource.png)

您可以選取圖表右上角的釘選圖示，將圖形釘選到儀表板。 若要了解如何使用儀表板，請參閱 [Creating and sharing dashboards in the Azure portal (在 Azure 入口網站建立和共用儀表板)](../../azure-portal/azure-portal-dashboards.md)。

## <a name="manage-access-to-resources"></a>管理對資源的存取

[角色型存取控制 (RBAC)](../../role-based-access-control/overview.md) 是您對 Azure 中的資源存取進行管理的機制。 如需詳細資料，請參閱[使用 RBAC 和 Azure 入口網站來管理存取權](../../role-based-access-control/role-assignments-portal.md)。

## <a name="next-steps"></a>後續步驟

- 若要瞭解 Azure Resource Manager，請參閱[Azure Resource Manager 總覽](overview.md)。
- 若要瞭解 Resource Manager 範本語法，請參閱[瞭解 Azure Resource Manager 範本的結構和語法](../templates/template-syntax.md)。
- 若要瞭解如何開發範本，請參閱[逐步教學](/azure/azure-resource-manager/)課程。
- 若要查看 Azure Resource Manager 範本架構，請參閱[範本參考](/azure/templates/)。
