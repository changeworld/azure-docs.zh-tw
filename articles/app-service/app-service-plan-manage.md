---
title: 管理 App Service 計畫
description: 瞭解如何執行不同的工作來管理 App Service 方案，例如建立、移動、調整和刪除。
keywords: App Service, Azure App Service, 級別, App Service方案, 變更, 建立, 管理, 管控
ms.assetid: 4859d0d5-3e3c-40cc-96eb-f318b2c51a3d
ms.topic: article
ms.date: 10/24/2019
ms.custom: seodec18
ms.openlocfilehash: 68cabbd00dd0b738590109cc39d8df82f5b7362d
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79280751"
---
# <a name="manage-an-app-service-plan-in-azure"></a>在 Azure 建立管理 App Service 方案

[Azure App Service 方案](overview-hosting-plans.md)提供 App Service 應用程式執行所需的資源。 本指南說明如何管理 App Service 方案。

## <a name="create-an-app-service-plan"></a>建立應用程式服務方案

> [!TIP]
> 如果您有 App Service 環境，請參閱[在 App Service 環境中建立 App Service 方案](environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan)。

您可建立空白的 App Service 方案，或是在應用程式建立期間建立方案。

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取 [建立資源]。

   ![在 Azure 入口網站中建立資源。][createResource] 

1. 選取 [**新增** > **Web 應用程式**] 或其他類型的 app service 應用程式。

   ![在 Azure 入口網站中建立應用程式。][createWebApp] 

2. 請先設定 [**實例詳細資料**] 區段，再設定 App Service 計畫。 **發佈**和**作業系統**等設定可以變更 App Service 方案的可用定價層。 **區域**決定您的 App Service 方案的建立位置。 
   
3. 在 [ **App Service 計畫**] 區段中，選取現有的方案，或選取 **[新建]** 來建立方案。

   ![建立 App Service 方案。][createASP] 

4. 在建立方案時，您可以選取新方案的定價層。 在 [ **Sku 和大小**] 中，選取 [**變更大小**] 以變更定價層。 

<a name="move"></a>

## <a name="move-an-app-to-another-app-service-plan"></a>將應用程式移到不同的 App Service 方案

您也可以將應用程式移到另一個 App Service 方案，只要來源方案和目標方案位於「相同的資源群組和地理區域」即可。

> [!NOTE]
> Azure 會將每個新的 App Service 方案部署到部署單位 (在內部稱為網路空間)。 每個區域可以有許多網路空間，但您的應用程式只能在相同網路空間中建立的方案之間移動。 App Service 環境是隔離的網路空間，因此可以在相同 App Service Environment 中的方案之間移動應用程式，但無法在不同 App Service 環境中的方案之間移動。
>
> 您無法在建立方案時指定您想要的網路空間，但這可確保方案建立於與現有方案相同的網路空間中。 簡單地說，所有使用相同資源群組和區域組合建立的方案都會部署到相同的網路空間中。 比方說，如果您在資源群組 A 和區域 B 中建立方案，則您後續在資源群組 A 和區域 B 中建立的方案都會部署到相同網路空間中。 請注意，方案建立之後便不能移動網路空間，所以您無法藉由將方案移至另一個資源群組，將其移到與另一個方案「相同的網路空間」中。
> 

1. 在  [Azure 入口網站](https://portal.azure.com)中，搜尋並選取 **應用程式服務**，然後選取您想要移動的應用程式。

2. 從左側功能表中，選取 [**變更 App Service 方案**]。

3. 在 [ **App Service 方案**] 下拉式清單中，選取要將應用程式移至其中的現有方案。 下拉式清單只會顯示與目前 App Service 方案位於相同資源群組和地理區域中的方案。 如果沒有這類計畫，它可讓您依預設建立計畫。 您也**可以選取 [新建]** ，以手動方式建立新的方案。

4. 如果您建立方案，則可以選取新方案的定價層。 在 [**定價層**] 中，選取要變更的現有層。 
   
   > [!IMPORTANT]
   > 如果您要將應用程式從較高層級的方案移至較低層的方案（例如從**D1**到**F1**），應用程式可能會遺失目標方案中的某些功能。 例如，如果您的應用程式使用 SSL 憑證，您可能會看到此錯誤訊息：
   >
   > `Cannot update the site with hostname '<app_name>' because its current SSL configuration 'SNI based SSL enabled' is not allowed in the target compute mode. Allowed SSL configuration is 'Disabled'.`

5. 完成後，選取 [確定]。
   
   ![App Service 方案選取器。][change] 

## <a name="move-an-app-to-a-different-region"></a>將應用程式移到不同的區域

您的應用程式執行所在的區域，就是 App Service 方案所在的區域。 不過，您無法變更 App Service 方案區域。 如果您想在不同的區域執行應用程式，有一個替代方法是複製應用程式。 複製會在任何區域中的新或現有 App Service 方案中產生您應用程式的複本。

您可以在功能表的 [開發工具] 區段中找到 [複製應用程式]。

> [!IMPORTANT]
> 複製有一些限制。 您可在 [Azure App Service 應用程式複製](app-service-web-app-cloning.md)中閱讀相關限制。

## <a name="scale-an-app-service-plan"></a>調整 App Service 方案

若要相應增加 App Service 方案的定價層，請參閱[在 Azure 中相應增加應用程式](manage-scale-up.md)。

若要相應縮小方案的執行個體計數，請參閱[手動或自動調整執行個體計數](../monitoring-and-diagnostics/insights-how-to-scale.md)。

<a name="delete"></a>

## <a name="delete-an-app-service-plan"></a>刪除 App Service 方案

為避免非預期費用，當您刪除 App Service 方案中的最後一個應用程式時，App Service 預設也會刪除此方案。 如果您選擇改為保留方案，您應該將方案變更為**免費**層，以避免產生費用。

> [!IMPORTANT]
> 沒有相關聯應用程式的 App Service 方案仍會產生費用，因為它們會繼續保留已設定的 VM 執行個體。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [在 Azure 中相應增加應用程式的規模](manage-scale-up.md)

[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
[createResource]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-a-resource.png
