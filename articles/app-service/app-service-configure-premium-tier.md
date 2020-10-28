---
title: 設定 PremiumV3 層
description: 藉由調整為新的 PremiumV3 定價層，瞭解如何為 Azure App Service 中的 web、行動裝置和 API 應用程式提供更佳的效能。
keywords: App Service, Azure App Service, 級別, 可調整, App Service方案, App Service 成本
ms.assetid: ff00902b-9858-4bee-ab95-d3406018c688
ms.topic: article
ms.date: 10/01/2020
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: e6b8c7d54cf24d810a1f32082d816c908966f63c
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92739698"
---
# <a name="configure-premiumv3-tier-for-azure-app-service"></a>設定 Azure App Service 的 PremiumV3 層

新的 **PremiumV3** 定價層提供更快速的處理器、SSD 儲存體和四倍的現有定價層的記憶體與核心比率 ( **>premiumv2** 層) 。 有此效能優勢，您可在較少的執行個體上執行應用程式來節省成本。 在本文中，您將瞭解如何在 **PremiumV3** 層中建立應用程式，或將應用程式擴大至 **PremiumV3** 層。

## <a name="prerequisites"></a>必要條件

若要將應用程式相應增加至 **PremiumV3** ，您必須擁有在定價層中執行低於 **PremiumV3** 的 Azure App Service 應用程式，且應用程式必須在支援 PremiumV3 的 App Service 部署中執行。

<a name="availability"></a>

## <a name="premiumv3-availability"></a>PremiumV3 可用性

**PremiumV3** 層適用于原生和容器應用程式，包括 Windows 容器和 Linux 容器。

> [!NOTE]
> 在預覽期間，于 **Premium 容器** 層中執行的任何 Windows 容器都會繼續正常運作，但高階 **容器** 層仍會保持預覽狀態。 **PremiumV3** 層是 **Premium 容器** 層的官方取代。 

**PremiumV3** 可在某些 Azure 區域中使用，且會持續新增其他區域中的可用性。 若要查看您的區域是否可以使用，請在 [Azure Cloud Shell](../cloud-shell/overview.md) 中執行下列 Azure CLI 命令：

```azurecli-interactive
az appservice list-locations --sku P1V3
```

<a name="create"></a>

## <a name="create-an-app-in-premiumv3-tier"></a>在 PremiumV3 層中建立應用程式

App Service 應用程式的定價層會定義在其執行所在的 [App Service 方案](overview-hosting-plans.md)中。 您可以單獨建立 App Service 方案，或在應用程式建立期間加以建立。

在  。 

選取 [ **生產** ]，然後選取 [ **P1V3** ]、[ **P2V3** ] 或 [ **P3V3** ]， **然後按一下 [** 套用]。

![螢幕擷取畫面，顯示您應用程式的建議定價層。](media/app-service-configure-premium-tier/scale-up-tier-select.png)

> [!IMPORTANT] 
> 如果您沒有看到 [ **P1V3** ]、[ **P2V3** ] 和 [ **P3V3** ] 選項，或選項呈現灰色，則可能無法在包含 App Service 方案的基礎 App Service 部署中使用 **PremiumV3** 。 如需詳細資訊，請參閱[從不支援的資源群組與區域組合中相應增加](#unsupported)。

## <a name="scale-up-an-existing-app-to-premiumv3-tier"></a>將現有應用程式擴大至 PremiumV3 層

將現有的應用程式調整至 **PremiumV3** 層之前，請確定 **PremiumV3** 可供使用。 如需詳細資訊，請參閱 [PremiumV3 可用性](#availability)。 如果無法使用，請參閱[從不支援的資源群組與區域組合中相應增加](#unsupported)。

視裝載環境而定，相應增加可能需要執行額外的步驟。 

在 <a href="https://portal.azure.com" target="_blank">Azure 入口網站</a>中，開啟您的 App Service 應用程式頁面。

在 App Service 應用程式頁面的左側導覽中，選取 [相應增加 (App Service 方案)]  。

![顯示如何擴大 app service 方案的螢幕擷取畫面。](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

選取 [ **生產** ]，然後選取 [ **P1V3** ]、[ **P2V3** ] 或 [ **P3V3** ]， **然後按一下 [** 套用]。

![螢幕擷取畫面，顯示您應用程式的建議定價層。](media/app-service-configure-premium-tier/scale-up-tier-select.png)

如果您的作業成功完成，您的應用程式總覽頁面會顯示它現在位於 **PremiumV3** 層中。

![螢幕擷取畫面，顯示應用程式總覽頁面上的 PremiumV3 定價層。](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>如果您收到錯誤

如果基礎 App Service 部署不支援 PremiumV3，某些 App Service 方案無法擴大為 PremiumV3 層。 如需詳細資訊，請參閱[從不支援的資源群組與區域組合中相應增加](#unsupported)。

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-resource-group-and-region-combination"></a>從不支援的資源群組與區域組合中相應增加

如果您的應用程式在無法使用 **PremiumV3** 的 App Service 部署中執行，或如果您的應用程式在目前不支援 **PremiumV3** 的區域中執行，則需要重新部署應用程式，以利用 **PremiumV3** 。  您有兩個選項：

- 在新的資源群組中建立應用程式，並使用新的 App Service 方案。 建立 App Service 方案時，請選取 **PremiumV3** 層。 此步驟可確保 App Service 方案部署至支援 **PremiumV3** 的部署單位。 然後，將您的應用程式程式碼重新部署到新建立的應用程式。 即使您將 App Service 方案調整為較低的層級來節省成本，但您一律可以調整為 **PremiumV3** ，因為部署單元支援。
- 如果您的應用程式已 **在現有的** 進階層中執行，則您可以將應用程式與所有應用程式設定、連接字串和部署設定複製到使用 **PremiumV3** 的新 app service 方案。

    ![顯示如何複製您的應用程式的螢幕擷取畫面。](media/app-service-configure-premium-tier/clone-app.png)

    在 [ **複製應用程式** ] 頁面中，您可以在所需的區域中使用 **PremiumV3** 來建立 App Service 方案，並指定您要複製的應用程式設定和設定。

## <a name="moving-from-premium-container-to-premium-v3-sku"></a>從 Premium 容器移至 Premium V3 SKU

如果您的應用程式使用預覽版 Premium 容器 SKU，而您想要移至新的 Premium V3 SKU，則需要重新部署應用程式，以利用 **PremiumV3** 。 若要這樣做，請參閱[從不支援的資源群組和區域組合擴大](#scale-up-from-an-unsupported-resource-group-and-region-combination)的第一個選項

## <a name="automate-with-scripts"></a>使用指令碼進行自動化

您可以使用 [Azure CLI](/cli/azure/install-azure-cli)或 [Azure PowerShell](/powershell/azure/)，以腳本在 **PremiumV3** 層中自動建立應用程式。

### <a name="azure-cli"></a>Azure CLI

下列命令會在 P1V2  建立 App Service 方案。 您可以在 Cloud Shell 中執行該命令。 的選項為 `--sku` P1V3、 _P2V3_ 和 _P3V3_ 。

```azurecli-interactive
az appservice plan create \
    --resource-group <resource_group_name> \
    --name <app_service_plan_name> \
    --sku P1V3
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

下列命令會在 _P1V3_ 中建立 App Service 方案。 `-WorkerSize` 選項包括 [小型]  、[中型]  和 [大型]  。

```powershell
New-AzAppServicePlan -ResourceGroupName <resource_group_name> `
    -Name <app_service_plan_name> `
    -Location <region_name> `
    -Tier "PremiumV3" `
    -WorkerSize "Small"
```

## <a name="more-resources"></a>其他資源

[在 Azure](manage-scale-up.md) 
 中擴大應用程式[手動或自動調整實例計數](../azure-monitor/platform/autoscale-get-started.md)
