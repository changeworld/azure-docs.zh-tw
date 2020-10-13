---
title: 從 Azure 入口網站部署模組-Azure IoT Edge
description: 使用 Azure 入口網站中的 IoT 中樞，將 IoT Edge 模組從 IoT 中樞推送至 IoT Edge 裝置（如部署資訊清單所設定）。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/30/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 67c7c71e1f1f3eb9e76aa4938cb4a0a15ca405c8
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978793"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>從 Azure 入口網站部署 Azure IoT Edge 模組

只要您使用商務邏輯建立 IoT Edge 模組，便需要將這些模組部署到裝置以在邊緣操作。 如果您有多個模組共同運作以收集及處理資料，您可以一次部署所有模組，並宣告這些模組的路由規則。

本文說明 Azure 入口網站如何引導您建立部署資訊清單，並且將部署推送到 IoT Edge 裝置。 如需建立根據其共用標籤以多個裝置為目標的部署的詳細資訊，請參閱 [大規模部署和監視 IoT Edge 模組](how-to-deploy-at-scale.md)。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶中的 [IoT 中樞](../iot-hub/iot-hub-create-through-portal.md)。
* IoT Edge 裝置。

  如果您沒有設定 IoT Edge 裝置，可以在 Azure 虛擬機器中建立一個。 遵循其中一個快速入門文章中的步驟， [建立虛擬 Linux 裝置](quickstart-linux.md) 或 [建立虛擬 Windows 裝置](quickstart.md)。

## <a name="configure-a-deployment-manifest"></a>設定部署資訊清單

部署資訊清單為 JSON 文件，說明應部署的模組、資料如何在模組之間流動，以及想要的模組對應項需要的屬性。 如需部署資訊清單的功能，以及如何建立此類清單的詳細資訊，請參閱[了解如何使用、設定以及重複使用 IoT Edge 模組](module-composition.md) (英文)。

Azure 入口網站有一個精靈，可以引導您建立部署資訊清單，而不用手動建置 JSON 文件。 它有三個步驟：**新增模組**、**指定路由**和**檢閱部署**。

### <a name="select-device-and-add-modules"></a>選取裝置並新增模組

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後瀏覽至 IoT 中樞。
1. 在左窗格中，從功能表中選取 [ **IoT Edge** ]。
1. 按一下裝置清單中目標裝置的識別碼。
1. 在上方列中，選取 [設定模組]。
1. 在頁面的 [ **容器登錄設定** ] 區段中，提供認證以存取任何包含您模組映射的私人容器登錄。
1. 在頁面的 [ **IoT Edge 模組** ] 區段中，選取 [ **新增**]。
1. 從下拉式功能表中查看模組的類型：

   * **IoT Edge 模組** -您提供模組名稱和容器映射 URI。 例如，範例 SimulatedTemperatureSensor 模組的映射 URI 為 `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0` 。 如果模組映射儲存在私人容器登錄中，請在此頁面上新增認證以存取映射。
   * **Marketplace 模組** -主控于 Azure Marketplace 中的模組。 某些 marketplace 模組需要額外的設定，因此請參閱 [Azure Marketplace IoT Edge 模組](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) 清單中的模組詳細資料。
   * **Azure 串流分析模組** -從 Azure 串流分析工作負載產生的模組。

1. 新增模組之後，請從清單中選取模組名稱以開啟模組設定。 視需要填寫選擇性欄位。 如需容器建立選項、重新啟動原則和所需狀態的詳細資訊，請參閱 [EdgeAgent 所需屬性](module-edgeagent-edgehub.md#edgeagent-desired-properties)。 如需模組對應項的詳細資訊，請參閱[定義或更新所需屬性](module-composition.md#define-or-update-desired-properties)。
1. 如有需要，請重複步驟5到8，將其他模組新增至您的部署。
1. 選取 **[下一步：路由]** 以繼續前往 [路由] 區段。

### <a name="specify-routes"></a>指定路由

在 [路由] 索引標籤上，請定義要用來在模組與 IoT 中樞之間傳遞訊息的方式。 訊息會使用名稱/值組來構成。 根據預設，路由會呼叫 **route** 並定義為 **從/messages/ \* 到 $upstream**，這表示任何模組輸出的任何訊息都會傳送至您的 IoT 中樞。  

使用「宣告 [路由](module-composition.md#declare-routes)」中的資訊來新增或更新路由，然後選取 **[下一步：檢查 + 建立** ] 以繼續進行嚮導的下一個步驟。

### <a name="review-deployment"></a>檢閱部署

檢閱區段會顯示 JSON 部署資訊清單，該清單會根據您在前兩個區段中的選項而建立。 請注意，還有兩個已宣告的模組尚未新增：**$edgeAgent** 和 **$edgeHub**。 這兩個模組組成 [IoT Edge 執行階段](iot-edge-runtime.md)，在每個部署中都是必要的預設值。

檢查您的部署資訊，然後選取 [ **建立**]。

## <a name="view-modules-on-your-device"></a>在裝置上檢視模組

將模組部署至裝置之後，您可以在 IoT 中樞的 [裝置詳細資料] 頁面中加以查看。 此頁面會顯示每個已部署模組的名稱，以及像是部署狀態和結束代碼的實用資訊。

## <a name="deploy-modules-from-azure-marketplace"></a>從 Azure Marketplace 部署模組

[Azure Marketplace](https://azuremarketplace.microsoft.com/) 是線上應用程式和服務的 Marketplace，您可以在其中流覽經過認證及優化可在 Azure 上執行的各種企業應用程式和解決方案，包括 [IoT Edge 模組](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)。

您可以從 Azure Marketplace 和 IoT 中樞部署 IoT Edge 模組。

### <a name="deploy-from-azure-marketplace"></a>從 Azure Marketplace 進行部署

詳閱 marketplace 中的 IoT Edge 模組，當您找到想要的模組時，請選取 [ **建立** ] 或 [ **立即取得**] 來部署。 繼續進行部署嚮導步驟，這可能會因您所選取的 IoT Edge 模組而有所不同：

1. 選取 [繼續]**** 來確認提供者的使用規定和隱私權原則。 您可能必須先提供連絡人資訊。
1. 選擇要作為目標裝置連結對象的訂用帳戶和 IoT 中樞。
1. 選擇 [部署至裝置]****。
1. 輸入裝置名稱或選取 [尋找裝置]**** 以瀏覽已向中樞註冊的裝置。
1. 選取 [建立]**** 以繼續執行設定部署資訊清單的標準程序，包括視需要新增其他資訊清單。 會預先定義新模組的詳細資料 (例如影像 URI、建立選項與必要屬性)，而且您可以視需要編輯它們。

確認模組已部署在您的 IoT 中樞的 Azure 入口網站中。 選取您的裝置，選取 [ **設定模組** ]，模組應該會列在 [ **IoT Edge 模組** ] 區段中。

### <a name="deploy-from-azure-iot-hub"></a>從 Azure IoT 中樞部署

您可以從 Azure Marketplace 快速地將模組部署到您的 IoT 中樞內的裝置 Azure 入口網站。

1. 在 Azure 入口網站中，巡覽至您的 IoT 中樞。
1. 在左窗格的 [ **自動裝置管理**] 下，選取 [ **IoT Edge**]。
1. 選取要接收部署的 IoT Edge 裝置。
1. 在上方列中，選取 [設定模組]。
1. 在 [ **IoT Edge 模組** ] 區段中，按一下 [ **新增**]，然後從下拉式功能表中選取 [ **Marketplace 模組** ]。

![在 IoT 中樞新增模組](./media/how-to-deploy-modules-portal/iothub-add-module.png)

從 **IoT Edge 模組 Marketplace** ] 頁面中選擇模組。 您選取的模組會自動針對您的訂用帳戶、資源群組和裝置進行設定。 然後，它會出現在您的 IoT Edge 模組清單中。 某些模組可能需要額外的設定。

> [!TIP]
> 從 Azure IoT 中樞 IoT Edge 模組的相關資訊會受到限制。 您可以先深入瞭解 Azure Marketplace 中的 [IoT Edge 模組](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) 。

選取 **[下一步]：** 依照此文章稍早 [指定路由](#specify-routes) 和 [審核部署](#review-deployment) 的說明，使用部署的路由和繼續。

## <a name="next-steps"></a>後續步驟

了解如何[大規模部署和監視 IoT Edge 模組](how-to-deploy-at-scale.md) (英文)
