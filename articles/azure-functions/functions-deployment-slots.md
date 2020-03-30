---
title: Azure 函數部署槽
description: 瞭解如何使用 Azure 函數創建和使用部署槽
author: craigshoemaker
ms.topic: reference
ms.date: 08/12/2019
ms.author: cshoe
ms.openlocfilehash: 0e8c93ea6d5c2b525ccbea2af900f100afcc3d93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75769212"
---
# <a name="azure-functions-deployment-slots"></a>Azure 函數部署槽

Azure 函數部署槽允許函數應用運行稱為"插槽"的不同實例。 插槽是通過公開可用的終結點公開的不同環境。 一個應用實例始終映射到生產槽，您可以按需交換分配給槽的實例。 在應用服務方案下運行的函數應用可能有多個插槽，而在"消費計畫"下只允許一個插槽。

下面反映了交換插槽對函數的影響：

- 流量重定向是無縫的;不會因為交換而刪除任何請求。
- 如果函數在交換期間運行，則執行將繼續，後續觸發器將路由到交換的應用實例。

> [!NOTE]
> 插槽當前不適用於 Linux 消費計畫。

## <a name="why-use-slots"></a>為什麼要使用插槽？

使用部署槽有很多優點。 以下方案描述了插槽的常見用途：

- **不同的環境用於不同目的**：使用不同的插槽，您可以在交換到生產槽或暫存槽之前區分應用實例。
- **預熱**：部署到插槽而不是直接部署到生產允許應用程式在上線前預熱。 此外，使用插槽可減少 HTTP 觸發工作負載的延遲。 實例在部署前預熱，從而減少新部署功能的冷啟動。
- **簡單的回退**：在與生產交換後，具有以前暫存應用的插槽現在具有以前的生產應用。 如果交換到生產槽中的更改未如預期的那樣，您可以立即反轉交換，以找回"最後已知良好的實例"。

## <a name="swap-operations"></a>交換操作

在交換期間，一個插槽被視為源，另一個插槽被視為目標。 源槽具有應用於目標槽的應用程式實例。 以下步驟可確保目標插槽在交換期間不會遇到停機：

1. **應用設置：** 目標槽中的設置將應用於源槽的所有實例。 例如，生產設置應用於暫存實例。 應用的設置包括以下類別：
    - [特定于插槽](#manage-settings)的應用設置和連接字串（如果適用）
    - [連續部署](../app-service/deploy-continuous-deployment.md)設置（如果啟用）
    - [應用服務身份驗證](../app-service/overview-authentication-authorization.md)設置（如果啟用）

1. **等待重新開機和可用性：** 交換將等待源槽中的每個實例完成其重新開機並可用於請求。 如果任何實例無法重新開機，交換操作將恢復所有更改到源槽並停止該操作。

1. **更新路由：** 如果源插槽上的所有實例都成功預熱，則兩個插槽通過切換路由規則完成交換。 在此步驟之後，目標槽（例如，生產槽）具有以前在源槽中預熱的應用。

1. **重複操作：** 現在，源槽以前在目標槽中具有預交換應用，則通過應用所有設置並重新啟動源槽的實例來執行相同的操作。

請記住下列幾點：

- 在交換操作的任何點，交換應用的初始化都發生在源插槽上。 在準備源槽時，無論交換成功還是失敗，目標槽將保持線上狀態。

- 要將暫存槽與生產槽交換，請確保生產槽*始終*為目標槽。 這樣，交換操作不會影響您的生產應用。

- 在*啟動交換 之前*，需要將與事件源和綁定相關的[設置配置為部署槽設置](#manage-settings)。 提前將它們標記為"粘性"可確保事件和輸出被定向到適當的實例。

## <a name="manage-settings"></a>管理設定

[!INCLUDE [app-service-deployment-slots-settings](../../includes/app-service-deployment-slots-settings.md)]

### <a name="create-a-deployment-setting"></a>創建部署設置

您可以將設置標記為"粘性"的部署設置。 粘滯設置不會與應用實例交換。

如果在一個插槽中創建部署設置，請確保在交換中涉及的任何其他槽中創建具有相同值的相同設置。 這樣，雖然設置的值不會更改，但設置名稱在插槽之間保持一致。 此名稱一致性可確保代碼不會嘗試訪問在一個插槽中定義的設置，而不是另一個插槽中定義的設置。

使用以下步驟創建部署設置：

- 導航到函數應用中的*插槽*
- 按一下插槽名稱
- 在 *"平臺功能>常規設置*下，按一下 **"配置**"
- 按一下要與當前插槽一起粘住的設置名稱
- 按一下 **"部署"插槽設置**核取方塊
- 按一下 [確定]****。
- 設置邊欄選項卡消失後，按一下 **"保存"** 以保留更改

![部署插槽設置](./media/functions-deployment-slots/azure-functions-deployment-slots-deployment-setting.png)

## <a name="deployment"></a>部署

創建插槽時，插槽為空。 您可以使用任何[受支援的部署技術](./functions-deployment-technologies.md)將應用程式部署到插槽。

## <a name="scaling"></a>調整大小

所有插槽都縮放為與生產槽相同的工時數。

- 對於消耗計畫，槽會隨著函數應用的縮放而縮放。
- 對於應用服務方案，應用將縮放為固定數量的工作人員。 插槽的運行工作與應用計畫相同的工作名數。

## <a name="add-a-slot"></a>新增位置

您可以通過[CLI](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-create)或通過門戶添加插槽。 以下步驟演示如何在門戶中創建新槽：

1. 導航到您的功能應用程式，然後按一下*插槽*旁邊的**加號**。

    ![添加 Azure 函數部署槽](./media/functions-deployment-slots/azure-functions-deployment-slots-add.png)

1. 在文字方塊中輸入名稱，然後按 **"創建**"按鈕。

    ![命名 Azure 函數部署槽](./media/functions-deployment-slots/azure-functions-deployment-slots-add-name.png)

## <a name="swap-slots"></a>交換插槽

您可以通過[CLI](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-swap)或閘戶交換插槽。 以下步驟演示如何交換門戶中的插槽：

1. 導航到函數應用
1. 按一下要交換的源槽名稱
1. 在 *"概述"* 選項卡中，按一下 **"交換**"按鈕!["交換 Azure 函數部署槽](./media/functions-deployment-slots/azure-functions-deployment-slots-swap.png)
1. 驗證交換的配置設置，然後按一下 **"交換**![Azure 函數"部署槽](./media/functions-deployment-slots/azure-functions-deployment-slots-swap-config.png)

在執行交換操作時，操作可能需要一段時間。

## <a name="roll-back-a-swap"></a>回滾交換

如果交換導致錯誤，或者您只想"撤銷"交換，則可以回滾到初始狀態。 要返回到預交換狀態，執行另一個交換以沖銷掉期。

## <a name="remove-a-slot"></a>刪除插槽

您可以通過[CLI](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-delete)或通過門戶刪除插槽。 以下步驟演示如何刪除門戶中的插槽：

1. 導航到功能應用概述

1. 按一下"**刪除**"按鈕

    ![添加 Azure 函數部署槽](./media/functions-deployment-slots/azure-functions-deployment-slots-delete.png)

## <a name="automate-slot-management"></a>自動老虎機管理

使用[Azure CLI](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest)，可以自動執行槽的以下操作：

- [創建](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-create)
- [刪除](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-delete)
- [清單](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-list)
- [交換](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-swap)
- [自動交換](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-auto-swap)

## <a name="change-app-service-plan"></a>更改應用服務方案

使用在應用服務方案下運行的函數應用，您可以選擇更改槽的基礎應用服務方案。

> [!NOTE]
> 在"消耗計畫"下，無法更改插槽的應用服務方案。

使用以下步驟更改插槽的應用服務方案：

1. 導航到插槽

1. 在*平臺功能*下 ，按一下 **"所有設置"**

    ![更改應用服務方案](./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-settings.png)

1. 點擊**應用服務方案**

1. 選擇新的應用服務方案，或創建新計畫

1. 按一下 [確定]****。

    ![更改應用服務方案](./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-select.png)


## <a name="limitations"></a>限制

Azure 函數部署槽具有以下限制：

- 可用於應用的插槽數取決於計畫。 消耗計畫只允許一個部署槽。 其他插槽可用於在應用服務方案下運行的應用。
- 交換插槽會重置`AzureWebJobsSecretStorageType`應用設置等於`files`的應用的鍵。
- 插槽不適用於 Linux 消費計畫。

## <a name="support-levels"></a>支援級別

部署槽有兩個級別的支援：

- **通用 （GA）**：完全支援並批准用於生產。
- **預覽**：尚未支援，但預計將來將達到 GA 狀態。

| 作業系統/託管計畫           | 支援級別     |
| ------------------------- | -------------------- |
| 視窗消耗       | 正式運作 |
| 視窗高級版           | 正式運作  |
| 專用視窗         | 正式運作 |
| Linux 消費         | 不支援          |
| Linux 高級版             | 正式運作  |
| Linux 專用           | 正式運作 |

## <a name="next-steps"></a>後續步驟

- [Azure 函數中的部署技術](./functions-deployment-technologies.md)
