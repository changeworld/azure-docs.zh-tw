---
title: 從 Azure 門戶部署模組 - Azure IoT 邊緣
description: 在 Azure 門戶中使用 IoT 中心將 IoT 邊緣模組從 IoT 中心推送到 IoT 邊緣設備，由部署清單配置。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/30/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5c44561895bc1905328ec0eb357bee1c68a8eb55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271443"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>從 Azure 入口網站部署 Azure IoT Edge 模組

只要您使用商務邏輯建立 IoT Edge 模組，便需要將這些模組部署到裝置以在邊緣操作。 如果您有多個模組共同運作以收集及處理資料，您可以一次部署所有模組，並宣告這些模組的路由規則。

本文說明 Azure 入口網站如何引導您建立部署資訊清單，並且將部署推送到 IoT Edge 裝置。 有關基於多個設備的共用標記創建針對多個設備的部署的資訊，請參閱[大規模部署和監視 IoT Edge 模組](how-to-deploy-monitor.md)。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂閱中的[IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。
* 已安裝 IoT Edge 執行階段的 [IoT Edge 裝置](how-to-register-device.md#register-in-the-azure-portal)。

## <a name="configure-a-deployment-manifest"></a>設定部署資訊清單

部署資訊清單為 JSON 文件，說明應部署的模組、資料如何在模組之間流動，以及想要的模組對應項需要的屬性。 如需部署資訊清單的功能，以及如何建立此類清單的詳細資訊，請參閱[了解如何使用、設定以及重複使用 IoT Edge 模組](module-composition.md) (英文)。

Azure 入口網站有一個精靈，可以引導您建立部署資訊清單，而不用手動建置 JSON 文件。 它有三個步驟：**新增模組**、**指定路由**和**檢閱部署**。

### <a name="select-device-and-add-modules"></a>選擇設備和添加模組

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後瀏覽至 IoT 中樞。
1. 在左側窗格中，從功能表中選擇**IoT 邊緣**。
1. 按一下裝置清單中目標裝置的識別碼。
1. 在上方列中，選取 [設定模組]****。
1. 在頁面的 **"容器註冊表設置"** 部分中，提供訪問包含模組映射的任何專用容器註冊表的憑據。
1. 在頁面的**IoT 邊緣模組**部分中，選擇 **"添加**"。
1. 查看下拉式功能表中的模組類型：

   * **IoT 邊緣模組**- 提供模組名稱和容器映射 URI。 例如，示例類比溫度感應器模組的圖像 URI 為`mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0`。 如果模組映射存儲在專用容器註冊表中，則在此頁上添加憑據以訪問映射。
   * **應用商店模組**- 託管在 Azure 應用商店中的模組。 某些應用商店模組需要其他配置，因此請查看 Azure 應用商店[IoT 邊緣模組](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)清單中的模組詳細資訊。
   * **Azure 流分析模組**- 從 Azure 流分析工作負荷生成的模組。

1. 添加模組後，從清單中選擇模組名稱以打開模組設置。 視需要填寫選擇性欄位。 如需容器建立選項、重新啟動原則和所需狀態的詳細資訊，請參閱 [EdgeAgent 所需屬性](module-edgeagent-edgehub.md#edgeagent-desired-properties)。 如需模組對應項的詳細資訊，請參閱[定義或更新所需屬性](module-composition.md#define-or-update-desired-properties)。
1. 如果需要，請重複步驟 5 到 8 以向部署中添加其他模組。
1. 選擇 **"下一步"：要繼續**到路線部分的路由。

### <a name="specify-routes"></a>指定路由

在 [路由]**** 索引標籤上，請定義要用來在模組與 IoT 中樞之間傳遞訊息的方式。 訊息會使用名稱/值組來構成。 預設情況下，路由稱為**路由**，定義為**從 /消息/into\* $upstream**，這意味著任何模組輸出的任何消息都發送到 IoT 中心。  

使用["聲明"路由](module-composition.md#declare-routes)中的資訊添加或更新路由，然後選擇 **"下一步：審閱 + 創建"** 以繼續嚮導的下一步。

### <a name="review-deployment"></a>檢閱部署

檢閱區段會顯示 JSON 部署資訊清單，該清單會根據您在前兩個區段中的選項而建立。 請注意，還有兩個已宣告的模組尚未新增：**$edgeAgent** 和 **$edgeHub**。 這兩個模組組成 [IoT Edge 執行階段](iot-edge-runtime.md)，在每個部署中都是必要的預設值。

查看部署資訊，然後選擇 **"創建**"。

## <a name="view-modules-on-your-device"></a>在裝置上檢視模組

將模組部署到設備後，您可以在 IoT 中心的設備詳細資訊頁中查看所有這些模組。 此頁面會顯示每個已部署模組的名稱，以及像是部署狀態和結束代碼的實用資訊。

## <a name="deploy-modules-from-azure-marketplace"></a>從 Azure Marketplace 部署模組

[Azure 應用商店](https://azuremarketplace.microsoft.com/)是一個線上應用程式和服務市場，您可以在其中流覽各種經過認證和優化以在 Azure 上運行的企業應用程式和解決方案，包括[IoT Edge 模組](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)。

可以從 Azure 應用商店和 IoT 中心部署 IoT 邊緣模組。

### <a name="deploy-from-azure-marketplace"></a>從 Azure Marketplace 進行部署

在市場中，通過"**創建**"或"**立即獲取**"來瞭解 IoT Edge 模組，當您找到所需的模組時，可以通過部署它。 繼續部署嚮導步驟，這些步驟可能因您選擇的 IoT 邊緣模組而異：

1. 選取 [繼續]**** 來確認提供者的使用規定和隱私權原則。 您可能需要首先提供聯繫資訊。
1. 選擇要作為目標裝置連結對象的訂用帳戶和 IoT 中樞。
1. 選擇 [部署至裝置]****。
1. 輸入裝置名稱或選取 [尋找裝置]**** 以瀏覽已向中樞註冊的裝置。
1. 選取 [建立]**** 以繼續執行設定部署資訊清單的標準程序，包括視需要新增其他資訊清單。 會預先定義新模組的詳細資料 (例如影像 URI、建立選項與必要屬性)，而且您可以視需要編輯它們。

驗證模組是否部署在 Azure 門戶中的 IoT 中心中。 選擇您的設備，選擇 **"設置模組"，** 模組應列在**IoT 邊緣模組**部分。

### <a name="deploy-from-azure-iot-hub"></a>從 Azure IoT 中心部署

您可以在 Azure 門戶中的 IoT 中心中快速將 Azure 應用商店中的模組部署到您的設備。

1. 在 Azure 入口網站中，巡覽至您的 IoT 中樞。
1. 在左側窗格中，在 **"自動裝置管理"** 下，選擇**IoT 邊緣**。
1. 選擇要接收部署的 IoT 邊緣設備。
1. 在上方列中，選取 [設定模組]****。
1. 在**IoT 邊緣模組**部分中，按一下"**添加**"並從下拉式功能表中選擇 **"應用商店模組**"。

![在 IoT 中心中添加模組](./media/how-to-deploy-modules-portal/iothub-add-module.png)

從**IoT 邊緣模組應用商店**頁面中選擇模組。 您選擇的模組將自動為訂閱、資源組和設備配置。 然後，它將顯示在 IoT 邊緣模組清單中。 某些模組可能需要其他配置。

> [!TIP]
> Azure IoT 中心中有關 IoT 邊緣模組的資訊有限。 您可以首先瞭解有關 Azure 應用商店中的[IoT 邊緣模組](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)的資訊。

選擇 **"下一步：路由**"，然後按照本文前面文中[指定路由](#specify-routes)和[查看部署](#review-deployment)所述繼續部署。

## <a name="next-steps"></a>後續步驟

了解如何[大規模部署和監視 IoT Edge 模組](how-to-deploy-monitor.md) (英文)
