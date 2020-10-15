---
title: 教學課程 - 設定微服務應用程式的自動調整
description: 本文說明如何使用 Microsoft Azure 入口網站或 Azure CLI 為您的應用程式設定自動調整。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/22/2020
ms.custom: devx-track-java
ms.openlocfilehash: 5ca0c498e9fd03ce6397824465f9e1e006eeccc1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90905389"
---
# <a name="tutorial-set-up-autoscale-for-microservice-applications"></a>教學課程：設定微服務應用程式的自動調整

**本文適用於：** ✔️ Java ✔️ C#

自動調整是 Azure Spring Cloud 的內建功能，可協助微服務應用程式在需求變更時以最佳效能執行。 這包括修改虛擬 CPU、記憶體和應用程式執行個體的數目。 本文說明如何使用 Microsoft Azure 入口網站或 Azure CLI 為您的應用程式設定自動調整。

## <a name="prerequisites"></a>必要條件

若要遵循這些程序，您需要：

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 已部署的 Azure Spring Cloud 服務執行個體。 請遵循[關於如何透過 Azure CLI 來部署應用程式的快速入門](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli)來開始使用。
* 服務執行個體中至少已建立一個應用程式。

## <a name="navigate-to-the-autoscale-page-in-the-azure-portal"></a>在 Azure 入口網站中，瀏覽至 [自動調整] 頁面

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 移至 Azure Spring Cloud 的 [概觀] 頁面。
3. 選取包含服務的資源群組。
4. 在左側瀏覽窗格的功能表中，選取 [設定] 底下的 [應用程式] 索引標籤。
5. 選取您要設定自動調整的應用程式。 在此範例中，請選取名為 **demo** 的應用程式。 接著，您應該會看到應用程式的 [概觀] 頁面。
6. 在左側瀏覽窗格的功能表中，移至 [設定] 底下的 [擴增] 索引標籤。
7. 選取您想要設定自動調整的部署。 您應該會看到下列區段中出現自動調整的選項。


![自動調整功能表](./media/spring-cloud-autoscale/autoscale-menu.png)

## <a name="set-up-autoscale-settings-for-your-application-in-the-azure-portal"></a>在 Azure 入口網站中設定應用程式的自動調整設定

自動調整需求管理有兩個選項：

* 手動調整：維持固定的執行個體計數。 在標準層中，您可以擴增到最多 500 個執行個體。 此值會變更微服務應用程式個別執行中執行個體的數目。
* 自訂自動調整：依據任何計量，依任何排程調整規模。

在 Azure 入口網站中，選擇您想要調整的方式。  下圖顯示**自訂自動調整**選項和模式設定。

![自訂自動調整](./media/spring-cloud-autoscale/custom-autoscale.png)

## <a name="set-up-autoscale-settings-for-your-application-in-azure-cli"></a>在 Azure CLI 中設定應用程式的自動調整設定
您也可以使用 Azure CLI 來設定自動調整模式。  下列命令會建立自動調整設定和自動調整規則。

* 建立自動調整設定
  ```
  az monitor autoscale create -g demo-rg --resource /subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourcegroups/demo-rg/providers/Microsoft.AppPlatform/Spring/autoscale/apps/demo/deployments/default --name demo-setting --min-count 1 --max-count 5 --count 1
  ```
* 建立自動調整規則
  ```
  az monitor autoscale rule create -g demo-rg --autoscale-name demo-setting --scale out 1 --cooldown 1 --condition "tomcat.global.request.total.count > 100 avg 1m where AppName == demo and Deployment == default"
  ```

## <a name="upgrade-to-the-standard-tier"></a>升級至標準層

如果您是在基本層，且受限於其中一或多項限制，您可以升級至標準層。 若要這麼做，請移至 [定價層] 功能表，方法是先選取 [標準層] 資料行，然後按一下 [升級] 按鈕。

## <a name="next-steps"></a>後續步驟

* [Microsoft Azure 的自動調整概觀](https://docs.microsoft.com/azure/azure-monitor/platform/autoscale-overview)
* [Azure CLI 監視自動調整](https://docs.microsoft.com/cli/azure/monitor/autoscale?view=azure-cli-latest&preserve-view=true)