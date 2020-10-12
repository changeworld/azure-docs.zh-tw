---
title: Azure 服務匯流排 - 自動更新傳訊單位
description: 本文會說明如何使用自動更新服務匯流排命名空間的訊息單位。
ms.topic: how-to
ms.date: 09/15/2020
ms.openlocfilehash: 0a72cc991e768a7bed01762d984cc56238ae0ad0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90984748"
---
# <a name="automatically-update-messaging-units-of-an-azure-service-bus-namespace"></a>自動更新 Azure 服務匯流排命名空間的傳訊單位 
自動調整可讓您執行適當數量的資源來處理應用程式的負載。 它可讓您新增資源來處理增加的負載，並可藉由移除閒置資源來節省成本。 請參閱 [Microsoft Azure 中的自動調整總覽](../azure-monitor/platform/autoscale-overview.md) ，以深入瞭解 Azure 監視器的自動調整功能。 

服務匯流排進階傳訊提供 CPU 和記憶體層級的資源隔離，讓每個客戶工作負載能隔離執行。 此資源容器稱為 **訊息單位**。 若要深入瞭解訊息處理單位，請參閱 [服務匯流排 Premium 訊息](service-bus-premium-messaging.md)。 

藉由使用服務匯流排 premium 命名空間的自動調整功能，您可以指定 [訊息單位](service-bus-premium-messaging.md) 的最小和最大數目，以及根據一組規則自動新增或移除訊息單位。 

例如，您可以使用自動調整功能來執行服務匯流排命名空間的下列調整案例。 

- 當命名空間的 CPU 使用量超過75% 時，增加服務匯流排命名空間的訊息單位。 
- 當命名空間的 CPU 使用量低於25% 時，減少服務匯流排命名空間的訊息單位。 
- 在上班時間期間使用更多訊息單位，並在下班時間減少。 

本文說明如何在 Azure 入口網站中自動調整服務匯流排命名空間 (更新 [訊息單位](service-bus-premium-messaging.md)) 。 

> [!IMPORTANT]
> 本文僅適用於 Azure 服務匯流排的 [進階] 層。 

## <a name="autoscale-setting-page"></a>自動調整設定頁面
首先，請遵循下列步驟，以流覽至您的服務匯流排命名空間的 **自動調整設定** 頁面。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 
2. 在搜尋列中，輸入 **服務匯流排**，從下拉式清單中選取 [ **服務匯流排** ]，然後按 **enter**鍵。 
1. 從命名空間清單中選取您的 **premium 命名空間** 。 
1. 切換至 [ **調整** ] 頁面。 

    :::image type="content" source="./media/automate-update-messaging-units/scale-page.png" alt-text="服務匯流排命名空間-調整頁面":::

## <a name="manual-scale"></a>手動調整 
這項設定可讓您為命名空間設定固定的訊息單位數目。 

1. 在 [ **自動調整設定** ] 頁面上，選取 [ **手動調整** ] （如果尚未選取）。 
1. 針對 [ **訊息單位** ] 設定，從下拉式清單中選取訊息單位數目。
1. 選取工具列上的 [ **儲存** ] 以儲存設定。 

    :::image type="content" source="./media/automate-update-messaging-units/manual-scale.png" alt-text="服務匯流排命名空間-調整頁面":::       


## <a name="custom-autoscale---default-condition"></a>自訂自動調整-預設條件
您可以使用條件設定訊息單位的自動調整。 當其他規模調整條件都不相符時，就會執行此規模調整條件。 您可以透過下列其中一種方式來設定預設條件：

- 根據度量 (（例如 CPU 或記憶體使用量）進行調整) 
- 調整為特定數目的訊息單位

您無法針對預設條件，將排程設定為特定天數或日期範圍的自動調整。 當其他具有排程的調整條件都不相符時，就會執行此規模調整條件。 

### <a name="scale-based-on-a-metric"></a>根據度量進行調整
下列程式說明如何新增條件，以在 cpu 使用量大於75% 時，自動增加訊息單位 (scale out) ，並在 CPU 使用率低於25% 的情況下，將訊息單位縮減 (的) 。 遞增的完成方式是從1到2、2到4，以及4到8。 同樣地，遞減的完成方式是從8到4、4到2，以及2到1。 

1. 在 [**自動調整設定**] 頁面上，針對 [**選擇調整資源的方式**] 選項選取 [**自訂自動調整**] 選項。 
1. 在頁面的 **預設** 區段中，指定預設條件的 **名稱** 。 選取 **鉛筆** 圖示以編輯文字。 
1. 選取 [根據**縮放比例**的**度量調整規模**]。 
1. 選取 [ **+ 新增規則**]。 

    :::image type="content" source="./media/automate-update-messaging-units/default-scale-metric-add-rule-link.png" alt-text="服務匯流排命名空間-調整頁面":::    
1. 在 [ **調整規模規則** ] 頁面上，依照下列步驟執行：
    1. 從 [計量 **名稱** ] 下拉式清單中選取度量。 在此範例中，它是 **CPU**。 
    1. 選取操作員和臨界值。 在此範例中，它們 **大於** 和 **75** （計量 **閾值），以觸發調整動作**。 
    1. 在 [**動作**]**區段中選取**作業。 在此範例中，它會設定為 **增加**。 
    1. 然後，選取 [**新增**]
    
        :::image type="content" source="./media/automate-update-messaging-units/scale-rule-cpu-75.png" alt-text="服務匯流排命名空間-調整頁面":::       

        > [!NOTE]
        > 如果在此範例中，整體 CPU 使用量超過75%，自動調整功能會增加命名空間的訊息單位。 遞增的完成方式是從1到2、2到4，以及4到8。 
1. 再次選取 [ **+ 新增規則** ]，然後在 [ **調整規模規則** ] 頁面上遵循下列步驟：
    1. 從 [計量 **名稱** ] 下拉式清單中選取度量。 在此範例中，它是 **CPU**。 
    1. 選取操作員和臨界值。 在此範例中，它們 **小於** 和 **25** 的計量 **閾值可觸發調整動作**。 
    1. 在 [**動作**]**區段中選取**作業。 在此範例中，它會設定為 **減少**。 
    1. 然後，選取 [**新增**] 

        :::image type="content" source="./media/automate-update-messaging-units/scale-rule-cpu-25.png" alt-text="服務匯流排命名空間-調整頁面":::       

        > [!NOTE]
        > 如果在此範例中，整體 CPU 使用率低於25%，自動調整功能會減少命名空間的訊息單位。 遞減的完成從8到4、4到2，以及2到1。 
1. 設定訊息單位的 **最小值** 和 **最大** 值和 **預設** 數目。

    :::image type="content" source="./media/automate-update-messaging-units/default-scale-metric-based.png" alt-text="服務匯流排命名空間-調整頁面":::
1. 選取工具列上的 [ **儲存** ]，以儲存自動調整設定。 
        
### <a name="scale-to-specific-number-of-messaging-units"></a>調整為特定數目的訊息單位
遵循下列步驟來設定規則，以將命名空間調整為使用特定的訊息單位數目。 同樣地，當其他規模調整條件都不相符時，就會套用預設條件。 

1. 在 [**自動調整設定**] 頁面上，針對 [**選擇調整資源的方式**] 選項選取 [**自訂自動調整**] 選項。 
1. 在頁面的 **預設** 區段中，指定預設條件的 **名稱** 。 
1. 針對**調整模式**選取 [**調整為特定的訊息單位**]。 
1. 針對 [ **訊息單位**]，選取預設的訊息單位數目。 

    :::image type="content" source="./media/automate-update-messaging-units/default-scale-messaging-units.png" alt-text="服務匯流排命名空間-調整頁面":::       

## <a name="custom-autoscale---additional-conditions"></a>自訂自動調整-其他條件
上一節說明如何新增自動調整設定的預設條件。 本節說明如何將更多條件新增至自動調整設定。 針對這些額外的非預設條件，您可以根據一周的特定天數或日期範圍來設定排程。 

### <a name="scale-based-on-a-metric"></a>根據度量進行調整
1. 在 [**自動調整設定**] 頁面上，針對 [**選擇調整資源的方式**] 選項選取 [**自訂自動調整**] 選項。 
1. 選取 [在**預設**區塊下**新增調整規模條件**]。 

    :::image type="content" source="./media/automate-update-messaging-units/add-scale-condition-link.png" alt-text="服務匯流排命名空間-調整頁面":::    
1. 指定條件的 **名稱** 。 
1. 確認已選取 [ **根據度量來調整規模** ] 選項。 
1. 選取 [ **+ 新增規則** ] 以新增規則，以在整體 CPU 使用量超過75% 時增加訊息單位。 遵循 [ [預設條件](#custom-autoscale---default-condition) ] 區段中的步驟。 
5. 設定訊息單位的 **最小值** 和 **最大** 值和 **預設** 數目。
6. 您也可以針對自訂條件設定 **排程** ， (但不是預設條件) 。 您可以指定條件的開始和結束日期 (或) 選取特定的天數 (星期一、星期二等等。 ) 周。 
    1. 如果您選取 [ **指定開始/結束日期**]，請選取 [ **時區**]、[ **開始日期** ] 和 [時間] 和 [ **結束日期和時間** ] (，如下圖所示) 讓條件生效。 

       :::image type="content" source="./media/automate-update-messaging-units/custom-min-max-default.png" alt-text="服務匯流排命名空間-調整頁面":::
    1. 如果您選取 [ **重複特定天數**]，請選取一周中的哪幾天、[時區]、[開始時間]，以及應套用條件的結束時間。 

        :::image type="content" source="./media/automate-update-messaging-units/repeat-specific-days.png" alt-text="服務匯流排命名空間-調整頁面":::
  
### <a name="scale-to-specific-number-of-messaging-units"></a>調整為特定數目的訊息單位
1. 在 [**自動調整設定**] 頁面上，針對 [**選擇調整資源的方式**] 選項選取 [**自訂自動調整**] 選項。 
1. 選取 [在**預設**區塊下**新增調整規模條件**]。 

    :::image type="content" source="./media/automate-update-messaging-units/add-scale-condition-link.png" alt-text="服務匯流排命名空間-調整頁面":::    
1. 指定條件的 **名稱** 。 
2. 針對**調整模式**選取 [**調整為特定的訊息單位**] 選項。 
1. 從下拉式清單中選取 **訊息單位** 數目。 
6. 針對 [ **排程**]，指定條件的 [開始] 和 [結束日期] (或) 選取 [特定天數] ([星期一]、[星期二] 等等。 ) 周和時間。 
    1. 如果您選取 [ **指定開始/結束日期**]，請選取 [ **時區**]、[ **開始日期** ] 和 [時間] 和 [ **結束日期** ] 和 [時間]，條件才會生效。 
    
    :::image type="content" source="./media/automate-update-messaging-units/scale-specific-messaging-units-start-end-dates.png" alt-text="服務匯流排命名空間-調整頁面":::        
    1. 如果您選取 [ **重複特定天數**]，請選取一周中的哪幾天、[時區]、[開始時間]，以及應套用條件的結束時間。
    
    :::image type="content" source="./media/automate-update-messaging-units/repeat-specific-days-2.png" alt-text="服務匯流排命名空間-調整頁面":::

> [!IMPORTANT]
> 若要深入瞭解自動調整設定的運作方式，尤其是它如何挑選設定檔或條件並評估多項規則，請參閱 [瞭解自動調整設定](../azure-monitor/platform/autoscale-understanding-settings.md)。          

## <a name="next-steps"></a>後續步驟
若要深入了解傳訊單位，請參閱[進階傳訊](service-bus-premium-messaging.md)

