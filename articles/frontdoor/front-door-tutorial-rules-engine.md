---
title: 教學課程：設定規則引擎 - Azure Front Door
description: 本文提供如何在 Azure 入口網站和 CLI 中設定規則引擎的教學課程。
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/09/2020
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3366f1a16777ecf46951296e4a1c2c28aed75feb
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2020
ms.locfileid: "90031920"
---
# <a name="tutorial-configure-your-rules-engine"></a>教學課程：設定規則引擎

本教學課程說明如何在 Azure 入口網站和 CLI 中建立規則引擎組態和第一個規則的步驟。 

在本教學課程中，您會了解如何：
> [!div class="checklist"]
> - 使用入口網站設定規則引擎。
> - 使用 Azure CLI 設定規則引擎

## <a name="prerequisites"></a>Prerequisites

* 您必須先建立 Front Door，才能完成本教學課程中的步驟。 如需詳細資訊，請參閱[快速入門：建立前端](quickstart-create-front-door.md)。

## <a name="configure-rules-engine-in-azure-portal"></a>在 Azure 入口網站中設定規則引擎
1. 在您的 Front Door 資源內，移至 [設定]，然後選取 [規則引擎組態]。 按一下 [新增]，為您的組態指定名稱，然後開始建立您的第一個規則引擎組態。

    ![Front Door 設定功能表](./media/front-door-rules-engine/rules-engine-tutorial-1.png)

1. 按一下 [新增規則]，以建立您的第一個規則。 然後，按一下 [新增條件] 或 [新增動作]，即可定義您的規則。
    
    > [!NOTE]
    >- 若要從規則中刪除條件或動作，請使用特定條件或動作右手邊的垃圾桶。
    > - 若要建立適用於所有連入流量的規則，請勿指定任何條件。
    > - 若要在符合第一個比對條件時停止評估規則，請勾選 [停止評估剩餘規則]。 若已選取，且滿足特定規則的所有比對條件，則不會執行組態中的剩餘規則。  

    ![規則引擎組態](./media/front-door-rules-engine/rules-engine-tutorial-4.png) 

1. 使用 [上移]、[下移] 和 [移至頂端] 按鈕，判斷您組態內的規則優先順序。 優先順序採用遞增順序，表示第一個列出的規則是最重要的規則。

1. 建立一或多個規則之後，請按 [儲存]。 此動作會建立規則引擎組態。

1. 建立一或多個組態之後，請將規則引擎組態與路由規則建立關聯。 雖然單一組態可以套用至許多路由規則，但一個路由規則只能包含一個規則引擎組態。 若要建立關聯，請移至您的 [Front Door 設計工具] > [路由規則]。 選取您想要新增規則引擎組態的路由規則，移至 [路由詳細資料] > [規則引擎組態]，然後選取您要建立關聯的組態。

    ![設定路由規則](./media/front-door-rules-engine/rules-engine-tutorial-5.png)


## <a name="configure-rules-engine-in-azure-cli"></a>在 Azure CLI 中設定規則引擎

1. 如果尚未安裝 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)，請先安裝。 新增 “front-door” extension:- az extension add --name front-door。 然後，登入並切換至您的訂用帳戶 az account set --subscription <name_or_Id>。

1. 從建立規則引擎著手 - 此範例會顯示一個規則，其中包含一個以標頭為基礎的動作和一個比對條件。 

    ```azurecli-interactive
    az network front-door rules-engine rule create -f {front_door} -g {resource_group} --rules-engine-name {rules_engine} --name {rule1} --priority 1 --action-type RequestHeader --header-action Overwrite --header-name Rewrite --header-value True --match-variable RequestFilenameExtension --operator Contains --match-values jpg png --transforms Lowercase
    ```

1. 列出所有規則。 

    ```azurecli-interactive
    az network front-door rules-engine rule list -f {front_door} -g {rg} --name {rules_engine}
    ```

1. 新增轉送路由覆寫動作。 

    ```azurecli-interactive
    az network front-door rules-engine rule action add -f {front_door} -g {rg} --rules-engine-name {rules_engine} --name {rule1} --action-type ForwardRouteOverride --backend-pool {backend_pool_name} --caching Disabled
    ```

1. 列出規則中的所有動作。 

    ```azurecli-interactive
    az network front-door rules-engine rule action list -f {front_door} -g {rg} -r {rules_engine} --name {rule1}
    ```

1. 將規則引擎組態連結至路由規則。  

    ```azurecli-interactive
    az network front-door routing-rule update -g {rg} -f {front_door} -n {routing_rule_name} --rules-engine {rules_engine}
    ```

1. 取消連結規則引擎。 

    ```azurecli-interactive
    az network front-door routing-rule update -g {rg} -f {front_door} -n {routing_rule_name} --remove rulesEngine # case sensitive word ‘rulesEngine’
    ```

如需詳細資訊，在[這裡](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/rules-engine?view=azure-cli-latest&preserve-view=true)可找到 AFD 規則引擎命令的完整清單。   

## <a name="clean-up-resources"></a>清除資源

在上述步驟中，您已設定規則引擎，並建立其與路由規則的關聯。 如果您不想再讓規則引擎設定與您的 Front Door 建立關聯，可以執行下列步驟來移除設定：

1. 按一下規則引擎名稱旁的三個點，將任何路由規則從規則引擎組態取消關聯。

    :::image type="content" source="./media/front-door-rules-engine/front-door-rule-engine-routing-association.png" alt-text="建立路由規則的關聯":::

1. 取消選取與此規則引擎組態相關聯的所有路由規則，然後按一下 [儲存]。

    :::image type="content" source="./media/front-door-rules-engine/front-door-routing-rule-association.png" alt-text="建立路由規則的關聯":::

1. 現在您可以從 Front Door 刪除規則引擎組態。

    :::image type="content" source="./media/front-door-rules-engine/front-door-delete-rule-engine-configuration.png" alt-text="建立路由規則的關聯":::

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

* 建立規則引擎組態
* 將組態與您的 Front Door 路由規則產生關聯。

若要了解如何使用規則引擎新增安全性標頭，請繼續進行下一個教學課程。

> [!div class="nextstepaction"]
> [使用規則引擎的安全性標頭](front-door-security-headers.md)
