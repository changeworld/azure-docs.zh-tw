---
title: " (入口網站管理端點和路由) "
titleSuffix: Azure Digital Twins
description: 請參閱如何使用 Azure 入口網站來設定及管理 Azure 數位 Twins 資料的端點和事件路由。
author: baanders
ms.author: baanders
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 87b674a9c6b7f7d591b39f1baf54c4d54082f306
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "87987302"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins-portal"></a>在 Azure 數位 Twins 中管理端點和路由 (入口網站) 

[!INCLUDE [digital-twins-route-selector.md](../../includes/digital-twins-route-selector.md)]

在 Azure 數位 Twins 中，您可以將[事件通知](how-to-interpret-event-data.md)路由傳送至下游服務或連線的計算資源。 這是藉由先設定可接收事件的**端點**來完成。 接著，您可以建立[**事件路由**](concepts-route-events.md)，以指定 Azure 數位 Twins 所產生的哪些事件會傳遞給哪些端點。

本文會逐步引導您使用[Azure 入口網站](https://portal.azure.com)建立端點和路由。

您也可以使用[EventRoutes api](how-to-use-apis-sdks.md)、 [.Net (c # ) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)或[Azure 數位 Twins CLI](how-to-use-cli.md)來管理端點和路由。 如需本文中使用這些機制（而不是入口網站）的版本，請參閱[*如何：管理端點和路由 (api 和 CLI) *](how-to-manage-routes-apis-cli.md)。

## <a name="prerequisites"></a>必要條件

* 您將需要一個**Azure 帳戶** (您可以在[這裡](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)免費設定一個) 
* 您將需要 azure 訂用帳戶中的**Azure 數位 Twins 實例**。 如果您還沒有實例，您可以使用[*如何：設定實例和驗證*](how-to-set-up-instance-scripted.md)中的步驟來建立一個。 請備妥安裝程式中的下列值，以供本文稍後使用：
    - 執行個體名稱
    - 資源群組

設定實例之後，您可以在[Azure 入口網站](https://portal.azure.com)中找到這些詳細資料。 登入入口網站，並在入口網站的搜尋列中搜尋您的實例名稱。
 
:::image type="content" source="media/how-to-manage-routes-portal/search-field-portal.png" alt-text="Azure 入口網站搜尋列的螢幕擷取畫面。":::

從結果中選取您的實例，以查看您實例的詳細資料頁面：

:::image type="content" source="media/how-to-manage-routes-portal/instance-details.png" alt-text="ADT 實例詳細資料的螢幕擷取畫面。" border="false":::

## <a name="create-an-endpoint-for-azure-digital-twins"></a>建立 Azure 數位 Twins 的端點

以下是您可以為您的實例建立的支援端點類型：
* [Event Grid](../event-grid/overview.md) 
* [事件中樞](../event-hubs/event-hubs-about.md)
* [服務匯流排](../service-bus-messaging/service-bus-messaging-overview.md)

如需不同端點類型的詳細資訊，請參閱在[*Azure 訊息服務之間進行選擇*](../event-grid/compare-messaging-services.md)。

若要將端點連結至 Azure 數位 Twins，您所使用的事件方格主題、事件中樞或服務匯流排必須已經存在。 

### <a name="create-an-event-grid-endpoint"></a>建立事件方格端點

必要條件 **：遵循**事件方格*自訂事件*快速入門的[*建立自訂主題*一節](../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)中的步驟，建立事件方格主題。

建立主題之後，您可以從 [ [Azure 入口網站](https://portal.azure.com)中的 Azure 數位 Twins 實例頁面將它連結到 Azure 數位 Twins (您可以在入口網站的搜尋列) 中輸入實例的名稱，藉以找到該實例。

從 [實例] 功能表中，選取 [_端點_]。 然後從接下來的 [*端點*] 頁面中，選取 [ *+ 建立端點*]。 

在開啟的 [*建立端點*] 頁面上，您可以藉由選取對應的選項按鈕來建立_事件方格_類型的端點。 完成其他詳細資料：在 [_名稱_] 欄位中輸入端點的名稱，從下拉式清單中選擇您的_訂_用帳戶，然後從第三個下拉式清單中選擇預先建立的_事件方格主題_。

然後，藉由點擊 [_儲存_] 來建立您的端點。

:::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-grid.png" alt-text="建立事件方格類型之端點的螢幕擷取畫面。":::

您可以藉由檢查頂端 Azure 入口網站列中的通知圖示，確認已成功建立端點： 

:::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-notifications.png" alt-text="確認端點建立的通知螢幕擷取畫面。" border="false":::

您也可以在 Azure 數位 Twins 實例的 [*端點*] 頁面上，查看已建立的端點。

如果端點建立失敗，請觀察錯誤訊息，然後在幾分鐘後再試一次。

現在，事件方格主題會以 [_名稱_] 欄位中指定的名稱，以 Azure 數位 Twins 內的端點形式提供。 您通常會使用該名稱做為**事件路由**的目標，這將[在本文稍後](#event-routes)建立。

### <a name="create-an-event-hubs-endpoint"></a>建立事件中樞端點

**必要條件**： 
* 您將需要_事件中樞命名空間_和_事件中樞_。 遵循事件中樞[*建立事件中樞*](../event-hubs/event-hubs-create.md)快速入門中的步驟來建立這兩個專案。
* 您將需要_授權規則_。 若要建立此專案，請參閱[*使用共用存取簽章事件中樞授權存取事件中樞資源*](../event-hubs/authorize-access-shared-access-signature.md)一文。

在[Azure 入口網站](https://portal.azure.com)中，移至您的 Azure 數位 Twins 實例的詳細資料頁面 (您可以在入口網站的搜尋列) 中輸入其名稱來尋找它。

從 [實例] 功能表中，選取 [_端點_]。 然後從接下來的 [*端點*] 頁面中，選取 [ *+ 建立端點*]。 

在開啟的 [*建立端點*] 頁面上，您可以藉由選取對應的選項按鈕來建立類型為_事件中樞_的端點。 在 [_名稱_] 欄位中輸入您的端點名稱。 然後從個別的下拉式清單中選取您的_訂_用帳戶，以及預先建立的_事件中樞命名空間_、_事件中樞_和_授權規則_。

然後，藉由點擊 [_儲存_] 來建立您的端點。

:::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-hub.png" alt-text="建立事件中樞類型之端點的螢幕擷取畫面。":::

您可以藉由檢查頂端 Azure 入口網站列中的通知圖示，確認已成功建立端點。 

如果端點建立失敗，請觀察錯誤訊息，然後在幾分鐘後再試一次。

現在，事件中樞會在 [_名稱_] 欄位中指定的名稱下，以 Azure 數位 Twins 內的端點形式提供。 您通常會使用該名稱做為**事件路由**的目標，這將[在本文稍後](#event-routes)建立。

### <a name="create-a-service-bus-endpoint"></a>建立服務匯流排端點

**必要條件**： 
* 您將需要_服務匯流排命名空間_和_服務匯流排主題_。 依照服務匯流排[*建立主題和*](../service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md)訂用帳戶] 快速入門中的步驟，建立這兩個內容。 您不需要完成 [[*建立主題的訂閱*](../service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md#create-subscriptions-to-the-topic)] 區段。
* 您將需要_授權規則_。 若要建立此專案，請參閱服務匯流排[*驗證和授權*](../service-bus-messaging/service-bus-authentication-and-authorization.md#shared-access-signature)一文。

在[Azure 入口網站](https://portal.azure.com)中，移至您的 Azure 數位 Twins 實例的詳細資料頁面 (您可以在入口網站的搜尋列) 中輸入其名稱來尋找它。

從 [實例] 功能表中，選取 [_端點_]。 然後從接下來的 [*端點*] 頁面中，選取 [ *+ 建立端點*]。 

在開啟的 [*建立端點*] 頁面上，您可以藉由選取對應的選項按鈕，建立_服務匯流排_類型的端點。 在 [_名稱_] 欄位中輸入您的端點名稱。 然後從個別的下拉式清單中選取您的_訂_用帳戶，以及預先建立的_服務匯流排命名空間_、_服務匯流排主題_和_授權規則_。

然後，藉由點擊 [_儲存_] 來建立您的端點。

:::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-service-bus.png" alt-text="建立服務匯流排類型之端點的螢幕擷取畫面。":::

您可以藉由檢查頂端 Azure 入口網站列中的通知圖示，確認已成功建立端點。 

如果端點建立失敗，請觀察錯誤訊息，然後在幾分鐘後再試一次。

現在，服務匯流排主題會以 [_名稱_] 欄位中指定的名稱，以 Azure 數位 Twins 內的端點形式提供。 您通常會使用該名稱做為**事件路由**的目標，這將[在本文稍後](#event-routes)建立。

## <a name="event-routes"></a>事件路由

若要實際將資料從 Azure 數位 Twins 傳送至端點，您必須定義**事件路由**。 這些路由可讓開發人員連接整個系統和下游服務的事件流程。 如需深入瞭解事件路由，請參閱[*概念：路由 Azure 數位 Twins 事件*](concepts-route-events.md)。

必要條件 **：您**必須先建立端點，如本文稍早所述，才能繼續建立路由。 當您的端點設定完成後，您可以繼續建立事件路由。

>[!NOTE]
>如果您最近已部署端點，請先確認它們已完成部署，**再**嘗試將它們用於新的事件路由。 如果您因為端點尚未就緒而無法設定路由，請等候幾分鐘，然後再試一次。

### <a name="create-an-event-route"></a>建立事件路由 

事件路由定義包含下列元素：
* 您想要使用的路由名稱
* 您想要使用的端點名稱
* 定義哪些事件要傳送至端點的篩選器
    - 若要停用路由而不傳送任何事件，請使用的篩選值為`false`
    - 若要啟用沒有特定篩選的路由，請使用的篩選值為`true`
    - 如需任何其他類型篩選的詳細資料，請參閱下面的[*篩選事件*](#filter-events)一節。

單一路由可允許選取多個通知和事件種類。

若要建立事件路由，請移至[Azure 入口網站](https://portal.azure.com)的 Azure 數位 Twins 實例的詳細資料頁面 (您可以在入口網站的搜尋列) 中輸入實例的名稱，以找到該實例。

從 [實例] 功能表中，選取 [_事件路由_]。 然後從接下來的 [*事件路由*] 頁面，選取 [ *+ 建立事件路由*]。 

在開啟的 [*建立事件路由*] 頁面上，選擇 [最小值]：
* 您在 [_名稱_] 欄位中的路由名稱
* 您想要用來建立路由的_端點_ 

若要啟用路由，您也必須至少**新增的事件路由篩選** `true` 。  (保留的預設值 `false` 將會建立路由，但不會傳送任何事件。 ) 若要這麼做，請切換 [ _Advanced editor_ ] 的參數加以啟用，然後 `true` 在 [*篩選*] 方塊中寫入。

:::image type="content" source="media/how-to-manage-routes-portal/create-event-route-no-filter.png" alt-text="建立實例之事件路由的螢幕擷取畫面。" lightbox="media/how-to-manage-routes-portal/create-event-route-no-filter.png":::

完成時，按 [_儲存_] 按鈕以建立您的事件路由。

### <a name="filter-events"></a>篩選事件

如先前所述，路由具有**篩選**欄位。 如果您路由上的篩選值為 `false` ，則不會將任何事件傳送至您的端點。 

啟用的最小篩選後 `true` ，端點將會收到來自 Azure 數位 Twins 的各種事件：
* 使用 Azure 數位 Twins 服務 API 的[數位 twins](concepts-twins-graph.md)所引發的遙測
* 對應項屬性變更通知，針對 Azure 數位 Twins 實例中的任何對應項的屬性變更引發
* 建立或刪除 twins 或關聯性時引發的生命週期事件
* 新增或刪除 Azure 數位 Twins 實例[中設定的模型時](concepts-models.md)，所引發的模型變更事件

您可以藉由定義更特定的篩選準則來限制所傳送的事件種類。

若要在建立事件路由時新增事件篩選器，請使用 [*建立事件路由*] 頁面的 [_新增事件路由篩選_] 區段。 

您可以從一些基本的一般篩選選項中選取，或使用 [高級篩選] 選項來撰寫您自己的自訂篩選器。

#### <a name="use-the-basic-filters"></a>使用基本篩選

若要使用基本篩選，請展開 [_事件種類_] 選項，然後選取對應至您想要傳送至端點之事件的核取方塊。 

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-basic-1.png" alt-text="使用基本篩選建立事件路由的螢幕擷取畫面。選取事件的核取方塊。":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

這會自動將您所選取篩選準則的文字填入 [篩選] 文字方塊中：

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-basic-2.png" alt-text="使用基本篩選建立事件路由的螢幕擷取畫面。在選取事件之後顯示自動填入的篩選文字。":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

#### <a name="use-the-advanced-filters"></a>使用 advanced 濾波器

或者，您可以使用 [advanced filter] 選項來撰寫您自己的自訂篩選器。

若要使用 [advanced filter] 選項來建立事件路由，請切換 [_高級編輯器_] 的參數加以啟用。 接著，您可以在 [*篩選*] 方塊中撰寫自己的事件篩選器：

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-advanced.png" alt-text="使用 advanced filter 建立事件路由的螢幕擷取畫面。":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

以下是支援的路由篩選。 [*篩選文字架構*] 欄中的詳細資料是可以在 [篩選] 方塊中輸入的文字。

[!INCLUDE [digital-twins-route-filters](../../includes/digital-twins-route-filters.md)]

[!INCLUDE [digital-twins-route-metrics](../../includes/digital-twins-route-metrics.md)]

## <a name="next-steps"></a>後續步驟

閱讀您可以接收的不同事件訊息類型：
* [*如何：解讀事件資料*](how-to-interpret-event-data.md)
