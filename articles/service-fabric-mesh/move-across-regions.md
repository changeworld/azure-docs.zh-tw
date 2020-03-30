---
title: 將服務結構網格應用程式移動到其他區域
description: 通過將當前範本的副本部署到新的 Azure 區域，可以移動服務結構網格資源。
author: erikadoyle
ms.author: edoyle
ms.topic: how-to
ms.date: 01/14/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 376808a6d8f61d4dc03d17061323a473d48053a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76908159"
---
# <a name="move-a-service-fabric-mesh-application-to-another-azure-region"></a>將服務結構網格應用程式移動到其他 Azure 區域

本文介紹如何將服務結構網格應用程式及其資源移動到其他 Azure 區域。 出於多種原因，您可以將資源移動到另一個區域。 例如，為了回應中斷，獲取僅在特定區域可用的功能或服務，以滿足內部策略和治理要求，或回應容量規劃要求。

 [服務結構網格不支援](../azure-resource-manager/management/region-move-support.md#microsoftservicefabricmesh)在 Azure 區域之間直接移動資源的能力。 但是，可以通過將當前 Azure 資源管理器範本的副本部署到新目的地區域，然後將入口流量和依賴項重定向到新創建的 Service Fabric Mesh 應用程式，間接移動資源。

## <a name="prerequisites"></a>Prerequisites

* 入口控制器（如[應用程式閘道](https://docs.microsoft.com/azure/application-gateway/)），作為用戶端和服務結構網格應用程式之間路由流量的中間
* 目標 Azure 區域中的服務結構網格（預覽）可用性`westus` `eastus`（ `westeurope`、 或 ）

## <a name="prepare"></a>準備

1. 通過匯出 Azure 資源管理器範本和來自最新部署的參數，獲取服務結構網格應用程式的目前狀態的"快照"。 為此，請使用 Azure 門戶在[部署後按照匯出範本](../azure-resource-manager/templates/export-template-portal.md#export-template-after-deployment)中的步驟操作。 您還可以使用 Azure [CLI、Azure](../azure-resource-manager/management/manage-resource-groups-cli.md#export-resource-groups-to-templates)[電源外殼](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)或[REST API](https://docs.microsoft.com/rest/api/resources/resourcegroups/exporttemplate)。

2. 如果適用，[將匯出同一資源組中的其他資源](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal#export-template-from-a-resource-group)，以便重新部署到目的地區域中。

3. 查看（如果需要）匯出的範本，以確保現有屬性值是要在目的地區域中使用的屬性值。 新的`location`（Azure 區域）是重新部署期間將提供的參數。

## <a name="move"></a>移動

1. 在目的地區域中創建新的資源組（或使用現有資源組）。

2. 使用匯出的範本，請按照使用 Azure 門戶[從自訂範本部署資源](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-portal#deploy-resources-from-custom-template)中的步驟進行操作。 您還可以使用 Azure [CLI、Azure](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-cli)[電源外殼](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-powershell)或[REST API](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-rest)。

3. 有關移動相關資源（如[Azure 存儲帳戶](../storage/common/storage-account-move.md)）的指導，請參閱主題"[跨區域移動 Azure 資源"](../azure-resource-manager/management/move-region.md)下列出的單個服務的指導。

## <a name="verify"></a>Verify

1. 部署完成後，測試應用程式終結點以驗證應用程式的功能。

2. 還可以通過檢查應用程式狀態[（az mesh 應用顯示](https://docs.microsoft.com/cli/azure/ext/mesh/mesh/app?view=azure-cli-latest#ext-mesh-az-mesh-app-show)）並使用[Azure 服務結構網格 CLI](https://docs.microsoft.com/azure/service-fabric-mesh/service-fabric-mesh-quickstart-deploy-container#set-up-service-fabric-mesh-cli)查看應用程式日誌和[（az 網格代碼包日誌](https://docs.microsoft.com/cli/azure/ext/mesh/mesh/code-package-log?view=azure-cli-latest)）命令來驗證應用程式的狀態。

## <a name="commit"></a>Commit

確認目的地區域中的 Service Fabric Mesh 應用程式的等效功能後，配置入口控制器（例如[應用程式閘道](../application-gateway/redirect-overview.md)），將流量重定向到新應用程式。

## <a name="clean-up-source-resources"></a>清理源資源

要完成服務結構網格應用程式的移動，[請刪除源應用程式和/或父資源組](../azure-resource-manager/management/delete-resource-group.md)。

## <a name="next-steps"></a>後續步驟

* [跨區域移動 Azure 資源](../azure-resource-manager/management/move-region.md)
* [支援跨區域移動 Azure 資源](../azure-resource-manager/management/region-move-support.md)
* [將資源移至新的資源群組或訂用帳戶](../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [資源的移動作業支援](../azure-resource-manager/management/move-support-resources.md
)
