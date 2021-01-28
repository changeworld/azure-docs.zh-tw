---
title: '使用 Private Link (預覽版啟用私用存取) '
titleSuffix: Azure Digital Twins
description: 瞭解如何使用 Private Link 來啟用 Azure 數位 Twins 解決方案的私用存取
author: baanders
ms.author: baanders
ms.date: 1/25/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: a5328f52975e72298b93107792692b178dac8a97
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98948720"
---
# <a name="enable-private-access-with-private-link-preview"></a>使用 Private Link (預覽版啟用私用存取) 

本文說明 [使用 Azure 數位 Twins 實例的私人端點來啟用 Private Link](concepts-security.md#private-network-access-with-azure-private-link-preview) 的不同方式， (目前為預覽) 。 為您的 Azure 數位 Twins 實例設定私人端點，可讓您保護 Azure 數位 Twins 實例並消除公開風險，同時避免 [ (VNet) 從 Azure 虛擬網路 ](../virtual-network/virtual-networks-overview.md)遭到外泄資料。

以下是本文所涵蓋的步驟： 
1. 開啟 Private Link 並設定 Azure 數位 Twins 實例的私人端點。
1. 停用或啟用公用網路存取旗標，以將 API 存取限制為僅限 Private Link 連接。

## <a name="prerequisites"></a>先決條件

在您可以設定私人端點之前，您需要有 [**Azure 虛擬網路 (VNet)**](../virtual-network/virtual-networks-overview.md) 可部署端點。 如果您還沒有 VNet，可以遵循其中一個 Azure 虛擬網路 [快速入門](../virtual-network/quick-create-portal.md) 來進行設定。

## <a name="add-a-private-endpoint-for-an-azure-digital-twins-instance"></a>新增 Azure 數位 Twins 實例的私人端點 

您可以使用 Azure 數位 Twins 實例的私人端點來開啟 Private Link，作為實例初始設定的一部分，或稍後在已經存在的實例上啟用。 

這兩種建立方法都會提供相同的設定選項和您實例的相同最終結果。 本節說明如何在 [Azure 入口網站](https://portal.azure.com)中進行每一項作業。

>[!TIP]
> 您也可以透過 Private Link 服務（而不是透過 Azure 數位 Twins 實例）設定 Private Link 端點。 這也提供相同的設定選項和相同的最終結果。
>
> 如需設定 Private Link 資源的詳細資訊，請參閱 [Azure 入口網站](../private-link/create-private-endpoint-portal.md)、 [Azure CLI](../private-link/create-private-endpoint-cli.md)、 [ARM](../private-link/create-private-endpoint-template.md)或 [PowerShell](../private-link/create-private-endpoint-powershell.md)的 Private Link 檔。

### <a name="add-a-private-endpoint-during-instance-creation"></a>在實例建立期間新增私人端點

在本節中，您將使用 [Azure 入口網站](https://portal.azure.com) ，在目前正在建立的 Azure 數位 Twins 實例上啟用具有私人端點的 Private Link。 本節著重于建立程式的網路步驟;如需建立新 Azure 數位 Twins 實例的完整逐步解說，請參閱 how [*to：設定實例和驗證*](how-to-set-up-instance-portal.md)。

Private Link 選項位於 [實例設定] 的 [ **網路** ] 索引標籤中。

在此索引標籤中，您可以選取連線 **方法** 的 [**私人端點**] 選項來啟用私人端點。

這會新增稱為 **私人端點** 連線的區段，您可以在其中設定私人端點的詳細資料。 選取 [ **+ 新增** ] 按鈕以繼續。

:::image type="content" source="media/how-to-enable-private-link/create-instance-networking-1.png" alt-text="Azure 入口網站的螢幕擷取畫面，其中顯示 Azure 數位 Twins 的 [建立資源] 對話方塊的 [網路] 索引標籤。索引標籤名稱、連線方法的私用端點選項，以及用來建立新私人端點連線的 [+ 新增] 按鈕周圍都有一個醒目提示。" lightbox="media/how-to-enable-private-link/create-instance-networking-1.png":::

這會開啟一個頁面，以輸入新私人端點的詳細資料。

:::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-full.png" alt-text="顯示 [建立私人端點] 頁面之 Azure 入口網站的螢幕擷取畫面。它包含以下所述的欄位。":::

1. 填寫 **訂** 用帳戶和 **資源群組** 的選取專案。 將 **位置** 設定為與您將使用之 VNet 相同的位置。 選擇端點的 **名稱** ，並為 **目標子資源** 選取 *API*。

1. 接下來，選取您想要用來部署端點的 **虛擬網路** 和 **子網** 。

1. 最後，選取是否要 **與私人 DNS 區域整合**。 您可以使用預設值 [ **是]** 或 []。如需此選項的說明，您可以遵循入口網站中的連結， [深入瞭解私人 DNS 整合](../private-link/private-endpoint-overview.md#dns-configuration)。

填寫設定選項之後，請按 **[確定** ] 完成。

這會讓您回到 Azure 數位 Twins 實例設定的 [ **網路** ] 索引標籤，在此索引標籤中，您的新端點應該會顯示在 [私人端點連線] 下。

:::image type="content" source="media/how-to-enable-private-link/create-instance-networking-2.png" alt-text="Azure 入口網站的螢幕擷取畫面，其中顯示 Azure 數位 Twins 的 [建立資源] 對話方塊的 [網路] 索引標籤。新的私人端點連線有一個醒目提示，而導覽按鈕 (複習 + 建立、上一個、下一個： Advanced) 。" lightbox="media/how-to-enable-private-link/create-instance-networking-2.png":::

然後，您可以使用下方瀏覽按鈕繼續進行其餘的實例設定。

### <a name="add-a-private-endpoint-to-an-existing-instance"></a>將私人端點新增至現有的實例

在本節中，您將使用 [Azure 入口網站](https://portal.azure.com) ，針對已存在的 Azure 數位 Twins 實例啟用具有私人端點的 Private Link。

1. 首先，在瀏覽器中流覽至 [Azure 入口網站](https://portal.azure.com) 。 藉由在入口網站的搜尋列中搜尋其名稱來顯示您的 Azure 數位 Twins 實例。

1. 選取左側功能表中的 [ **網路 (預覽])** 。

1. 切換至 [ **私人端點連接** ] 索引標籤。

1. 選取 [ **+ 私人端點** ] 以開啟 [ **建立私人端點** 設定]。

    :::image type="content" source="media/how-to-enable-private-link/add-endpoint-digital-twins.png" alt-text="Azure 入口網站的螢幕擷取畫面，其中顯示 Azure 數位 Twins 實例的網路 (預覽) 頁面。[私人端點連線] 索引標籤已反白顯示，而且 [+ 私人端點] 按鈕也會反白顯示。" lightbox="media/how-to-enable-private-link/add-endpoint-digital-twins.png":::

1. 在 [ **基本**] 索引標籤中   ，輸入或選取您的專案的 **訂** 用帳戶和 **資源群組**，以及端點的 **名稱** 和 **區域**。 區域必須與您所使用之 VNet 的區域相同。

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-1.png" alt-text="Azure 入口網站的螢幕擷取畫面，其中顯示 [建立私人端點] 對話方塊的第一個 (基本) ] 索引標籤。它包含上面所述的欄位。":::

    當您完成時，請選取 [ **下一步：資源 >** ] 按鈕以移至下一個索引標籤。

1. 在 [ **資源** ] 索引標籤中，輸入或選取這項資訊： 
    * **連接方法**：選取 **[連線到我目錄中的 azure 資源]** ，以搜尋您的 azure 數位 Twins 實例。
    * **訂** 用帳戶：輸入您的訂用帳戶。
    * **資源類型**：選取 [ **DigitalTwins]/[digitalTwinsInstances** ]
    * **資源**：選取 Azure 數位 Twins 實例的名稱。
    * **目標子資源**：選取 **API**。

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-2.png" alt-text="Azure 入口網站的螢幕擷取畫面，顯示 [建立私人端點] 對話方塊的第二個 (資源) ] 索引標籤。它包含上面所述的欄位。":::

    當您完成時，請選取 [ **下一步：設定 >** ] 按鈕以移至下一個索引標籤。    

1.  **在 [設定**] 索引標籤中，輸入或選取這項資訊：
    * **虛擬網路**：選取您的虛擬網路。
    * **子網**：選擇虛擬網路中的子網。
    * **與私人 dns 區域整合**：選取是否要 **與私人 dns 區域整合**。 您可以使用預設值 [ **是]** 或 []。如需此選項的說明，您可以遵循入口網站中的連結， [深入瞭解私人 DNS 整合](../private-link/private-endpoint-overview.md#dns-configuration)。
    如果您選取 **[是]**，您可以保留預設的設定資訊。

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-3.png" alt-text="Azure 入口網站的螢幕擷取畫面，其中顯示 [建立私人端點] 對話方塊的第三個 (設定) ] 索引標籤。它包含上面所述的欄位。":::

    當您完成時，您可以選取 [ **審核 + 建立** ] 按鈕來完成設定。 

1. 在 [ **審核 + 建立** ] 索引標籤中，檢查您的選取專案，然後選取 [ **建立** ] 按鈕。 

當端點完成部署時，它應該會顯示在 Azure 數位 Twins 實例的私人端點連線中。

>[!TIP]
> 您也可以從 Azure 入口網站中的 Private Link 中心查看端點。

## <a name="disable--enable-public-network-access-flags"></a>停用/啟用公用網路存取旗標

您可以設定 Azure 數位 Twins 實例以拒絕所有公用連線，並只允許透過私人端點的連線，以增強網路安全性。 此動作是透過 **公用網路存取旗** 標來完成。 

此原則可讓您限制僅 Private Link 連接的 API 存取。 當公用網路存取旗標設定為 [ *停用*] 時，從公用雲端對 Azure 數位 Twins 實例資料平面的所有 REST API 呼叫都會傳回 `403, Unauthorized` 。 或者，當原則設定為 [ *已停用* ] 並透過私人端點提出要求時，API 呼叫就會成功。

您可以使用 [Azure 入口網站](https://portal.azure.com)、 [Azure CLI](/cli/azure/)或 [ARMClient 命令工具](https://github.com/projectkudu/ARMClient)來更新網路旗標的值。

### <a name="option-1-using-the-azure-portal"></a>選項1：使用 Azure 入口網站

若要在 [Azure 入口網站](https://portal.azure.com)中停用或啟用公用網路存取，請開啟入口網站並流覽至您的 Azure 數位 Twins 實例。

1. 選取左側功能表中的 [ **網路 (預覽])** 。

1. 在 [**公用存取**] 索引標籤中，將 [**允許公用網路存取**] 設定為 [**停用**] 或 [**所有網路**

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-enable-private-link/network-flag-portal.png" alt-text="Azure 入口網站的螢幕擷取畫面，其中顯示 Azure 數位 Twins 實例的網路 (預覽) 頁面。[公用存取] 索引標籤已反白顯示，而選擇是否允許公用網路存取的選項也會反白顯示。" lightbox="media/how-to-enable-private-link/network-flag-portal.png":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

    選取 [儲存]。

### <a name="option-2-using-the-azure-cli"></a>選項2：使用 Azure CLI

使用 Azure CLI，此動作會使用命令完成， `az resource update` 如下所示。 Azure 數位 Twins 實例上的將 publicnetworkaccess 新增屬性可以設定為 `disabled` 或 `enabled` 。

若要停用旗標，請使用下列命令，並以您的實例值取代預留位置。
 
```azurecli
az resource update --ids /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance> --set properties.publicNetworkAccess=disabled  
```

以下是輸出的樣子螢幕擷取畫面。

:::image type="content" source="media/how-to-enable-private-link/network-flag-cli.png" alt-text="執行上述 Azure CLI 命令之終端機視窗的螢幕擷取畫面。輸出會包含實例的詳細資料，包括名為將 publicnetworkaccess 新增的屬性以及已停用的值。" lightbox="media/how-to-enable-private-link/network-flag-cli.png":::

若要啟用旗標，請使用下列類似的命令。
 
```azurecli
az resource update --ids /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance> --set properties.publicNetworkAccess=enabled  
```

### <a name="option-3-usingarmclient"></a>選項3：使用 ARMClient  

使用 [ARMClient 命令工具](https://github.com/projectkudu/ARMClient)，可以使用下列命令來啟用或停用公用網路存取。 

若要停用公用網路存取：
  
```cmd/sh
armclient login 

armclient PATCH /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance>?api-version=2020-12-01 "{ 'properties': { 'publicNetworkAccess': 'disabled' } }"  
```

若要啟用公用網路存取：  
  
```cmd/sh
armclient login 

armclient PATCH /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance>?api-version=2020-12-01 "{ 'properties': { 'publicNetworkAccess': 'enabled' } }"  
``` 

## <a name="next-steps"></a>後續步驟

深入瞭解適用于 Azure 的 Private Link： 
* [*什麼是 Azure Private Link 服務？*](../private-link/private-link-service-overview.md)