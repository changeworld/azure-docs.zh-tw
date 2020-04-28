---
title: Azure Functions 部署位置
description: 瞭解如何使用 Azure Functions 建立和使用部署位置
author: craigshoemaker
ms.topic: reference
ms.date: 08/12/2019
ms.author: cshoe
ms.openlocfilehash: 0e8c93ea6d5c2b525ccbea2af900f100afcc3d93
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75769212"
---
# <a name="azure-functions-deployment-slots"></a>Azure Functions 部署位置

Azure Functions 部署位置可讓您的函數應用程式執行稱為「位置」的不同實例。 位置是透過公開可用的端點公開的不同環境。 一個應用程式實例一律會對應至生產位置，而您可以視需要交換指派給某個插槽的實例。 在 App Service 方案下執行的函式應用程式可能會有多個位置，而在取用量方案中，只允許一個位置。

以下會反映函數如何受到交換位置的影響：

- 流量重新導向順暢;因為交換，所以不會捨棄任何要求。
- 如果函式在交換期間執行，則會繼續執行，且後續的觸發程式會路由至已交換的應用程式實例。

> [!NOTE]
> Linux 使用量方案目前無法使用位置。

## <a name="why-use-slots"></a>為何要使用位置？

使用部署位置有一些優點。 下列案例說明位置的常見用法：

- 不同**的環境有不同的用途**：使用不同的位置，可讓您在交換至生產環境或預備位置之前，先區別應用程式實例。
- **預先準備**：部署到位置而不是直接部署到生產環境，可讓應用程式在上線之前準備就緒。 此外，使用位置可減少 HTTP 觸發的工作負載延遲。 實例會在部署之前準備就緒，以減少新部署函數的冷啟動。
- **輕鬆回退**：與生產環境交換之後，具有先前預備應用程式的位置現在會有先前的生產應用程式。 如果交換到生產位置的變更不是您預期的，您可以立即反轉交換，以取得「最後一個已知良好的實例」。

## <a name="swap-operations"></a>交換作業

交換期間，會將一個位置視為來源，另一個是目標。 來源位置具有套用至目標位置之應用程式的實例。 下列步驟可確保目標位置在交換期間不會遇到停機時間：

1. 套用**設定：** 來自目標位置的設定會套用至來源位置的所有實例。 例如，生產環境設定會套用至暫存實例。 套用的設定包含下列類別：
    - 位置[特定的](#manage-settings)應用程式設定和連接字串（如果適用）
    - [持續部署](../app-service/deploy-continuous-deployment.md)設定（如果已啟用）
    - [App Service 驗證](../app-service/overview-authentication-authorization.md)設定（如果已啟用）

1. **等待重新開機和可用性：** 交換會等候來源位置中的每個實例完成其重新開機，並且可供要求使用。 如果有任何實例無法重新開機，則交換作業會將所有變更還原到來源位置，並停止作業。

1. **更新路由：** 如果來源位置上的所有實例都已成功準備就緒，這兩個插槽會藉由切換路由規則來完成交換。 在此步驟之後，目標位置（例如，生產位置）會有先前在來源插槽中準備就緒的應用程式。

1. **重複操作：** 現在，來源位置已有先前在目標插槽中的預先交換應用程式，請套用所有設定並重新啟動來源位置的實例，以執行相同的操作。

請記住下列幾點：

- 在交換作業的任何時間點，交換應用程式的初始化會在來源位置上進行。 當來源位置正在準備時，目標位置會保持上線，不論交換成功或失敗。

- 若要交換預備位置與生產位置，請確定生產位置*一定*是目標位置。 如此一來，交換作業就不會影響您的生產應用程式。

- 與事件來源和系結相關的設定，必須先設定為[部署位置設定](#manage-settings)，*才能起始交換*。 將它們標示為「固定」，以確保事件和輸出會導向至適當的實例。

## <a name="manage-settings"></a>管理設定

[!INCLUDE [app-service-deployment-slots-settings](../../includes/app-service-deployment-slots-settings.md)]

### <a name="create-a-deployment-setting"></a>建立部署設定

您可以將設定標示為部署設定，使其成為「粘滯」。 粘滯設定不會與應用程式實例交換。

如果您在一個位置建立部署設定，請務必在與交換相關的任何其他位置中，建立具有唯一值的相同設定。 如此一來，當設定的值不會變更時，設定名稱會在插槽之間保持一致。 此名稱一致性可確保您的程式碼不會嘗試存取一個位置中所定義的設定，而不是另一個。

使用下列步驟來建立部署設定：

- 流覽至函數應用程式*中的位置*
- 按一下位置名稱
- 在 *[平臺功能 > 一般設定*] 下**Configuration** ，按一下 [設定]
- 按一下您想要與目前位置保持在一起的設定名稱
- 按一下 [**部署位置設定**] 核取方塊
- 按一下 [檔案] &gt; [新增] &gt; [專案] 
- 設定 blade 消失後，按一下 [**儲存**] 以保留變更

![部署位置設定](./media/functions-deployment-slots/azure-functions-deployment-slots-deployment-setting.png)

## <a name="deployment"></a>部署

當您建立插槽時，位置是空的。 您可以使用任何支援的[部署技術](./functions-deployment-technologies.md)，將您的應用程式部署到某個位置。

## <a name="scaling"></a>調整大小

所有位置都會調整為與生產位置相同的背景工作數目。

- 針對取用方案，位置會隨著函式應用程式的調整而調整。
- 針對 App Service 方案，應用程式會調整為固定的背景工作角色數目。 插槽會在與應用程式方案相同的背景工作數目上執行。

## <a name="add-a-slot"></a>新增位置

您可以透過[CLI](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-create)或入口網站來新增位置。 下列步驟示範如何在入口網站中建立新的位置：

1. 流覽至您的函數應用程式，然後按一下 [*插槽*] 旁的**加號**。

    ![新增 Azure Functions 部署位置](./media/functions-deployment-slots/azure-functions-deployment-slots-add.png)

1. 在文字方塊中輸入名稱，然後按 [**建立**] 按鈕。

    ![名稱 Azure Functions 部署位置](./media/functions-deployment-slots/azure-functions-deployment-slots-add-name.png)

## <a name="swap-slots"></a>交換位置

您可以經由[CLI](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-swap)或透過入口網站交換位置。 下列步驟示範如何在入口網站中交換位置：

1. 流覽至函數應用程式
1. 按一下您要交換的來源位置名稱
1. 從 [*總覽*] 索引標籤中**Swap** ，按一下![[交換] 按鈕交換 Azure Functions 部署位置](./media/functions-deployment-slots/azure-functions-deployment-slots-swap.png)
1. 確認交換的設定值，然後按一下 [**交換** ![交換] Azure Functions 部署位置](./media/functions-deployment-slots/azure-functions-deployment-slots-swap-config.png)

執行交換作業時，作業可能需要一些時間。

## <a name="roll-back-a-swap"></a>復原交換

如果交換導致錯誤，或您只想要「復原」交換，您可以回復為初始狀態。 若要回到預先交換的狀態，請執行另一個交換來反轉交換。

## <a name="remove-a-slot"></a>移除位置

您可以透過[CLI](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-delete)或透過入口網站來移除位置。 下列步驟示範如何在入口網站中移除位置：

1. 流覽至函數應用程式總覽

1. 按一下 [**刪除**] 按鈕

    ![新增 Azure Functions 部署位置](./media/functions-deployment-slots/azure-functions-deployment-slots-delete.png)

## <a name="automate-slot-management"></a>自動插槽管理

使用[Azure CLI](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest)，您可以自動執行下列動作：

- [建立](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-create)
- [delete](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-delete)
- list
- [調換](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-swap)
- [自動交換](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-auto-swap)

## <a name="change-app-service-plan"></a>變更 App Service 計畫

使用在 App Service 計畫下執行的函式應用程式，您可以選擇變更位置的基礎 App Service 計畫。

> [!NOTE]
> 您無法在取用量方案下變更位置的 App Service 方案。

使用下列步驟來變更位置的 App Service 方案：

1. 流覽至位置

1. 在 [*平臺功能*] 底下，按一下 [**所有設定**]

    ![變更 app service 方案](./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-settings.png)

1. 按一下 [ **App Service 方案**]

1. 選取新的 App Service 方案，或建立新的方案

1. 按一下 [檔案] &gt; [新增] &gt; [專案] 

    ![變更 app service 方案](./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-select.png)


## <a name="limitations"></a>限制

Azure Functions 部署位置有下列限制：

- 應用程式可用的插槽數目取決於方案。 耗用量方案只允許一個部署位置。 在 App Service 方案下執行的應用程式可以使用其他位置。
- 交換位置會針對`AzureWebJobsSecretStorageType`應用程式設定等於的應用程式重設金鑰`files`。
- Linux 使用量方案無法使用插槽。

## <a name="support-levels"></a>支援層級

部署位置有兩種支援層級：

- 正式運作 **（GA）**：完全支援並已核准可供生產環境使用。
- **預覽**：尚不支援，但未來預期會到達 GA 狀態。

| OS/主控方案           | 支援層級     |
| ------------------------- | -------------------- |
| Windows 耗用量       | 正式運作 |
| Windows Premium           | 正式運作  |
| Windows 專用         | 正式運作 |
| Linux 使用量         | 不支援          |
| Linux Premium             | 正式運作  |
| Linux 專用           | 正式運作 |

## <a name="next-steps"></a>後續步驟

- [Azure Functions 中的部署技術](./functions-deployment-technologies.md)
