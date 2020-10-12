---
title: Azure Functions 部署位置
description: 瞭解如何建立及使用 Azure Functions 的部署位置
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: cshoe
ms.openlocfilehash: 0361ba7bc67948c25b842a3fb7406d2999fdd725
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91530607"
---
# <a name="azure-functions-deployment-slots"></a>Azure Functions 部署位置

Azure Functions 部署位置可讓您的函數應用程式執行不同的實例，稱為「位置」。 插槽是透過公開可用的端點公開的不同環境。 其中一個應用程式實例一律對應至生產位置，而且您可以視需要交換指派給位置的實例。 在 App Service 方案下執行的函式應用程式可能有多個位置，而在取用方案下只允許一個位置。

下列內容反映函式如何受到交換位置的影響：

- 流量重新導向很順暢;因為交換，所以不會捨棄任何要求。
- 如果函式在交換期間正在執行，則會繼續執行，並將下一個觸發程式路由至已交換的應用程式實例。

> [!NOTE]
> Linux 使用量方案目前無法使用插槽。

## <a name="why-use-slots"></a>為何要使用插槽？

使用部署位置有一些優點。 下列案例說明位置的常見用法：

- 不同**的環境有不同的用途**：使用不同的位置，讓您有機會在交換至生產環境或預備位置之前，先區分應用程式實例。
- **預先準備**：部署至位置而非直接進入生產環境，可讓應用程式在上線之前先準備就緒。 此外，使用位置可減少 HTTP 觸發之工作負載的延遲。 實例會在部署之前準備就緒，以減少新部署函數的冷啟動。
- **輕鬆的回退**：在與生產環境交換之後，具有先前預備應用程式的位置現在會有先前的生產應用程式。 如果交換到生產位置的變更不是您預期的，您可以立即反轉交換以取得「最後一個已知的良好實例」。

## <a name="swap-operations"></a>交換作業

交換期間會將一個位置視為來源，另一個則視為目標。 來源位置具有應用程式的實例，此應用程式會套用至目標位置。 下列步驟可確保在交換期間，目標位置不會遭遇停機時間：

1. 套用**設定：** 來自目標位置的設定會套用至來源位置的所有實例。 例如，生產環境設定會套用至預備實例。 套用的設定包括下列類別：
    - [特定位置的](#manage-settings) 應用程式設定和連接字串（若適用） (（如果適用）) 
    - [持續部署](../app-service/deploy-continuous-deployment.md) 設定 (啟用時) 
    - [App Service 驗證](../app-service/overview-authentication-authorization.md) 設定 (啟用) 

1. **等候重新開機和可用性：** 交換會等候來源位置中的每個實例完成其重新開機，並且可供要求使用。 如果任何實例無法重新開機，交換作業就會將所有變更還原至來源位置，並停止操作。

1. **更新路由：** 如果來源位置上的所有實例都成功準備就緒，則兩個位置會藉由切換路由規則來完成交換。 在這個步驟之後，目標位置 (例如，生產位置) 具有先前在來源位置中準備就緒的應用程式。

1. **重複操作：** 現在來源位置具有之前在目標位置的預先交換應用程式，請套用所有設定並重新啟動來源位置的實例，以完成相同的作業。

請記住下列幾點：

- 在交換作業的任何時間點，會在來源位置上初始化已交換的應用程式。 當來源位置備妥時，目標位置會保持連線，不論交換成功或失敗。

- 若要交換預備位置與生產位置，請確定生產位置 *一律* 為目標位置。 如此一來，交換作業就不會影響您的生產應用程式。

- 與事件來源和系結相關的設定必須先設定為 [部署位置設定](#manage-settings)， *才能開始交換*。 事先將它們標示為「粘滯」，可確保事件和輸出會導向至適當的實例。

## <a name="manage-settings"></a>管理設定

[!INCLUDE [app-service-deployment-slots-settings](../../includes/app-service-deployment-slots-settings.md)]

### <a name="create-a-deployment-setting"></a>建立部署設定

您可以將設定標示為部署設定，讓它成為「粘滯」。 [粘滯] 設定不會與應用程式實例交換。

如果您在一個位置建立部署設定，請務必在與交換相關的任何其他位置中，建立具有唯一值的相同設定。 如此一來，當設定的值不會變更時，設定名稱在插槽之間保持一致。 此名稱一致性可確保您的程式碼不會嘗試存取定義在某個位置的設定，而不會嘗試存取另一個位置中定義的設定

使用下列步驟建立部署設定：

1. 流覽至函數應用程式中的 **部署** 位置，然後選取位置名稱。

    :::image type="content" source="./media/functions-deployment-slots/functions-navigate-slots.png" alt-text="尋找 Azure 入口網站中的位置。" border="true":::

1. 選取 [設定 **]，然後選取要**與目前位置保持在一起的設定名稱。

    :::image type="content" source="./media/functions-deployment-slots/functions-configure-deployment-slot.png" alt-text="尋找 Azure 入口網站中的位置。" border="true":::

1. 選取 [部署位置] **設定**，然後選取 **[確定]**。

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slot-setting.png" alt-text="尋找 Azure 入口網站中的位置。" border="true":::

1. 設定區段消失之後，請選取 [ **儲存** ] 以保留變更

    :::image type="content" source="./media/functions-deployment-slots/functions-save-deployment-slot-setting.png" alt-text="尋找 Azure 入口網站中的位置。" border="true":::

## <a name="deployment"></a>部署

當您建立插槽時，位置是空的。 您可以使用任何支援的 [部署技術](./functions-deployment-technologies.md) ，將您的應用程式部署至某個位置。

## <a name="scaling"></a>調整大小

所有位置會調整為與生產位置相同的背景工作數目。

- 針對取用方案，位置會隨著函式應用程式調整而調整。
- 針對 App Service 方案，應用程式會調整為固定數目的背景工作角色。 位置會與應用程式方案在相同數量的背景工作角色上執行。

## <a name="add-a-slot"></a>新增位置

您可以透過 [CLI](/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-create) 或透過入口網站新增位置。 下列步驟示範如何在入口網站中建立新的位置：

1. 流覽至您的函數應用程式。

1. 選取 [ **部署**位置]，然後選取 [ **+ 新增**位置]。

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slots-add.png" alt-text="尋找 Azure 入口網站中的位置。" border="true":::

1. 輸入位置的名稱，然後選取 [ **新增**]。

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slots-add-name.png" alt-text="尋找 Azure 入口網站中的位置。" border="true":::

## <a name="swap-slots"></a>交換位置

您可以透過 [CLI](/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-swap) 或透過入口網站交換插槽。 下列步驟示範如何在入口網站中交換位置：

1. 巡覽至函式應用程式。
1. 選取 [ **部署**位置]，然後選取 [ **交換**]。

    :::image type="content" source="./media/functions-deployment-slots/functions-swap-deployment-slot.png" alt-text="尋找 Azure 入口網站中的位置。" border="true":::

1. 確認交換的設定，然後選取 [**交換**]
    
    :::image type="content" source="./media/functions-deployment-slots/azure-functions-deployment-slots-swap-config.png" alt-text="尋找 Azure 入口網站中的位置。" border="true":::

執行交換作業時，作業可能需要一些時間。

## <a name="roll-back-a-swap"></a>復原交換

如果交換導致錯誤，或您只想要「復原」交換，您可以回復為初始狀態。 若要返回預先交換的狀態，請進行其他交換以反轉交換。

## <a name="remove-a-slot"></a>移除插槽

您可以透過 [CLI](/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-delete) 或透過入口網站移除插槽。 下列步驟示範如何在入口網站中移除位置：

1. 流覽至函數應用程式中的 **部署** 位置，然後選取位置名稱。

    :::image type="content" source="./media/functions-deployment-slots/functions-navigate-slots.png" alt-text="尋找 Azure 入口網站中的位置。" border="true":::

1. 選取 [刪除]  。

    :::image type="content" source="./media/functions-deployment-slots/functions-delete-deployment-slot.png" alt-text="尋找 Azure 入口網站中的位置。" border="true":::

1. 輸入您想要刪除的部署位置名稱，然後選取 [ **刪除**]。

    :::image type="content" source="./media/functions-deployment-slots/functions-delete-deployment-slot-details.png" alt-text="尋找 Azure 入口網站中的位置。" border="true":::

1. 關閉 [刪除確認] 窗格。

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slot-deleted.png" alt-text="尋找 Azure 入口網站中的位置。" border="true":::

## <a name="automate-slot-management"></a>自動插槽管理

您可以使用 [Azure CLI](/cli/azure/functionapp/deployment/slot?view=azure-cli-latest)，將下列位置的動作自動化：

- [create](/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-create)
- [delete](/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-delete)
- [list](/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-list)
- [交換](/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-swap)
- [自動交換](/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-auto-swap)

## <a name="change-app-service-plan"></a>變更 App Service 方案

使用在 App Service 計畫下執行的函數應用程式，您可以變更位置的基礎 App Service 計畫。

> [!NOTE]
> 您無法在取用方案下變更位置的 App Service 方案。

使用下列步驟來變更位置的 App Service 方案：

1. 流覽至函數應用程式中的 **部署** 位置，然後選取位置名稱。

    :::image type="content" source="./media/functions-deployment-slots/functions-navigate-slots.png" alt-text="尋找 Azure 入口網站中的位置。" border="true":::

1. 在 [ **App Service 方案**] 下，選取 [ **變更 App Service 方案**]。

1. 選取您要升級的方案，或建立新的方案。

    :::image type="content" source="./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-apply.png" alt-text="尋找 Azure 入口網站中的位置。" border="true":::

1. 選取 [確定]  。

## <a name="limitations"></a>限制

Azure Functions 部署位置有下列限制：

- 應用程式可用的插槽數目取決於方案。 使用量方案只允許一個部署位置。 App Service 方案下執行的應用程式可使用其他位置。
- 交換位置會重設應用程式設定等於的應用程式金鑰 `AzureWebJobsSecretStorageType` `files` 。
- Linux 使用量方案無法使用插槽。

## <a name="support-levels"></a>支援層級

部署位置有兩個支援層級：

- **正式運作 (GA) **：完全支援並核准供生產環境使用。
- **預覽**：尚未支援，但預期會在未來達到 GA 狀態。

| 作業系統/主控方案           | 支援層級     |
| ------------------------- | -------------------- |
| Windows 耗用量       | 正式運作 |
| Windows Premium           | 正式運作  |
| Windows 專用         | 正式運作 |
| Linux 使用量         | 不支援          |
| Linux Premium             | 正式運作  |
| Linux 專用           | 正式運作 |

## <a name="next-steps"></a>後續步驟

- [Azure Functions 中的部署技術](./functions-deployment-technologies.md)
