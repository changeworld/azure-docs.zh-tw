---
title: 將 Service Fabric 網格應用程式移至另一個區域
description: 您可以將目前範本的複本部署到新的 Azure 區域，以移動 Service Fabric 的網格資源。
author: erikadoyle
ms.author: edoyle
ms.topic: how-to
ms.date: 01/14/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 376808a6d8f61d4dc03d17061323a473d48053a6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "76908159"
---
# <a name="move-a-service-fabric-mesh-application-to-another-azure-region"></a>將 Service Fabric 網格應用程式移至另一個 Azure 區域

本文說明如何將您的 Service Fabric 網格應用程式和其資源移至不同的 Azure 區域。 您可能會因為許多原因而將您的資源移至另一個區域。 例如，為了回應中斷，您只能取得特定區域提供的功能或服務，以符合內部原則和治理需求，或是為了回應容量規劃需求。

 [Service Fabric 網格不支援](../azure-resource-manager/management/region-move-support.md#microsoftservicefabricmesh)在 Azure 區域之間直接移動資源的能力。 不過，您可以將目前 Azure Resource Manager 範本的複本部署到新的目的地區域，然後將輸入流量和相依性重新導向至新建立的 Service Fabric 網狀應用程式，以間接移動資源。

## <a name="prerequisites"></a>必要條件

* 輸入控制器（例如[應用程式閘道](https://docs.microsoft.com/azure/application-gateway/)）做為路由傳送用戶端和 Service Fabric 網狀應用程式之間流量的媒介
* 目標 Azure 區域中的 Service Fabric 網格（預覽）可用性（ `westus` 、 `eastus` 或 `westeurope` ）

## <a name="prepare"></a>準備

1. 從最新的部署中匯出 Azure Resource Manager 範本和參數，以製作 Service Fabric 網格應用程式目前狀態的「快照集」。 若要這麼做，請遵循使用 Azure 入口網站在[部署後匯出範本](../azure-resource-manager/templates/export-template-portal.md#export-template-after-deployment)中的步驟。 您也可以使用[Azure CLI](../azure-resource-manager/management/manage-resource-groups-cli.md#export-resource-groups-to-templates)、 [Azure PowerShell](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)或[REST API](https://docs.microsoft.com/rest/api/resources/resourcegroups/exporttemplate)。

2. 如果適用的話，請[匯出相同資源群組中的其他資源](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal#export-template-from-a-resource-group)，以在目的地區域中重新部署。

3. 檢查（並視需要編輯）匯出的範本，以確保現有的屬性值是您想要在目的地區域中使用的內容。 新的 `location` （Azure 區域）是您將在重新部署期間提供的參數。

## <a name="move"></a>移動

1. 在目的地區域中建立新的資源群組（或使用現有的）。

2. 使用匯出的範本，依照使用 Azure 入口網站[從自訂範本部署資源](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-portal#deploy-resources-from-custom-template)中的步驟進行。 您也可以使用[Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-cli)、 [Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-powershell)或[REST API](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-rest)。

3. 如需有關移動相關資源（例如[Azure 儲存體帳戶](../storage/common/storage-account-move.md)）的指引，請參閱[跨區域移動 Azure 資源](../azure-resource-manager/management/move-region.md)主題底下所列之個別服務的指導方針。

## <a name="verify"></a>確認

1. 當部署完成時，請測試應用程式端點，以確認應用程式的功能。

2. 您也可以藉由檢查應用程式狀態（[az 網狀應用程式顯示](https://docs.microsoft.com/cli/azure/ext/mesh/mesh/app?view=azure-cli-latest#ext-mesh-az-mesh-app-show)），並使用[AZURE Service Fabric 網格 CLI](https://docs.microsoft.com/azure/service-fabric-mesh/service-fabric-mesh-quickstart-deploy-container#set-up-service-fabric-mesh-cli)來查看應用程式記錄檔和（[az code package-log](https://docs.microsoft.com/cli/azure/ext/mesh/mesh/code-package-log?view=azure-cli-latest)）命令，以確認應用程式的狀態。

## <a name="commit"></a>Commit

一旦您在目的地區域中確認了 Service Fabric 網狀應用程式的對等功能之後，請設定輸入控制器（例如[應用程式閘道](../application-gateway/redirect-overview.md)），將流量重新導向至新的應用程式。

## <a name="clean-up-source-resources"></a>清除來源資源

若要完成 Service Fabric 網格應用程式的移動，請[刪除來源應用程式和/或父資源群組](../azure-resource-manager/management/delete-resource-group.md)。

## <a name="next-steps"></a>後續步驟

* [跨區域移動 Azure 資源](../azure-resource-manager/management/move-region.md)
* [跨區域移動 Azure 資源的支援](../azure-resource-manager/management/region-move-support.md)
* [將資源移至新的資源群組或訂用帳戶](../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [資源的移動作業支援](../azure-resource-manager/management/move-support-resources.md
)
