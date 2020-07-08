---
title: 擴大功能和容量
description: 了解如何擴大 Azure App Service 中的應用程式。 取得更多 CPU、記憶體、磁碟空間和額外的功能。
ms.assetid: f7091b25-b2b6-48da-8d4a-dcf9b7baccab
ms.topic: article
ms.date: 08/19/2019
ms.custom: seodec18
ms.openlocfilehash: b8545c4f8c0dc86d83e0711147a1bda608c2897f
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2020
ms.locfileid: "84020161"
---
# <a name="scale-up-an-app-in-azure-app-service"></a>在 Azure App Service 中擴大應用程式

本文將說明如何在 Azure App Service 中相應增加應用程式的規模。 有兩個工作流程適合用來擴大和擴增規模，而本文說明擴大工作流程。

* [擴大](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling)：取得更多的 CPU、記憶體、磁碟空間和額外的功能，例如專用虛擬機器 (VM)、自訂網域和憑證、預備位置，以及自動調整等等。 您可以藉由變更應用程式所屬的 App Service 方案定價層來擴大。
* [擴增](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling)：增加執行您的應用程式的 VM 執行個體數目。
  視您的定價層而定，最多可以擴增至 30 個執行個體。 **隔離**層中的 [App Service 環境](environment/intro.md)，進一步將您的相應放大計數增加到 100 個執行個體。 如需擴增的詳細資訊，請參閱[手動或自動調整執行個體計數](../monitoring-and-diagnostics/insights-how-to-scale.md)。 您可以在該文章中了解如何使用自動調整，也就是根據預先定義的規則與排程，自動調整執行個體計數。

這些調整設定只需幾秒鐘便能套用，且影響範圍遍及 [App Service 方案](../app-service/overview-hosting-plans.md)內的所有應用程式。
在此過程中，您不需要變更程式碼或重新部署應用程式。

如需各 App Service 方案價格資訊及功能的詳細資訊，請參閱 [App Service 價格詳細資料](https://azure.microsoft.com/pricing/details/web-sites/)。  

> [!NOTE]
> 從 **免費** 層切換 App Service 方案之前，您必須先適當地移除 Azure 訂用帳戶的 [消費限制](https://azure.microsoft.com/pricing/spending-limits/) 。 若要檢視或變更 Microsoft Azure App Service 訂用帳戶的選項，請參閱 [Microsoft Azure 訂訂用帳戶][azuresubscriptions]。
> 
> 

<a name="scalingsharedorbasic"></a>
<a name="scalingstandard"></a>

## <a name="scale-up-your-pricing-tier"></a>擴大您的定價層

> [!NOTE]
> 若要擴大 **PremiumV2** 層，請參閱[設定 App Service 的 PremiumV2 層](app-service-configure-premium-tier.md)。
>

1. 在瀏覽器中，開啟 [Azure 入口網站][portal]。

1. 在 App Service 應用程式頁面的左側功能表中，選取 [擴大 (App Service 方案)]。
   
3. 選擇您的定價層，然後選取 [套用]。 選取不同類別 (例如 [生產]) 以及 [查看其他選項] 以顯示更多定價層。
   
    ![瀏覽以擴大您的 Azure 應用程式規模。][ChooseWHP]

    當作業完成時，您會看到具有綠色成功核取記號的通知快顯視窗。

<a name="ScalingSQLServer"></a>

## <a name="scale-related-resources"></a>調整相關資源
如果您的應用程式相依於其他服務 (如 Azure SQL Database 或 Azure 儲存體)，您可以單獨擴大這些資源。 這些資源不受 App Service 方案管理。

1. 在應用程式的 [概觀] 頁面中，選取 [資源群組] 連結。
   
    ![擴大 Azure 應用程式的相關資源](./media/web-sites-scale/RGEssentialsLink.png)

2. 在 [資源群組] 頁面的 [摘要] 組件中，選取您想要擴縮的資源。 下列螢幕擷取畫面顯示 SQL Database 資源。
   
    ![巡覽至資源群組頁面以擴大 Azure 應用程式的規模](./media/web-sites-scale/ResourceGroup.png)

    若要擴大相關資源，請參閱特定資源類型的文件。 例如，若要擴大單一 SQL Database，請參閱[在 Azure SQL Database 中擴縮單一資料庫資源](../azure-sql/database/single-database-scale.md)。 若要擴大適用於 MySQL 的 Azure 資料庫資源，請參閱[擴縮 MySQL 資源](../mysql/concepts-pricing-tiers.md#scale-resources)。

<a name="OtherFeatures"></a>
<a name="devfeatures"></a>

## <a name="compare-pricing-tiers"></a>比較定價層

如需詳細資訊，例如每個定價層的 VM 大小，請參閱 [App Service 定價詳細資料](https://azure.microsoft.com/pricing/details/app-service)。

如需服務限制、配額及條件約束的表格，以及每層支援的功能，請參閱 [App Service 限制](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)。

<a name="Next Steps"></a>

## <a name="more-resources"></a>其他資源

[手動或自動調整執行個體計數](../monitoring-and-diagnostics/insights-how-to-scale.md)  
[設定 App Service 的 PremiumV2 層](app-service-configure-premium-tier.md)

<!-- LINKS -->
[vmsizes]:https://azure.microsoft.com/pricing/details/app-service/
[SQLaccountsbilling]:https://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]:https://account.windowsazure.com/subscriptions
[portal]: https://portal.azure.com/

<!-- IMAGES -->
[ChooseWHP]: ./media/web-sites-scale/scale1ChooseWHP.png
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png
