---
title: 包含檔案
description: 包含檔案
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 06/30/2020
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 76c8eb7acf20d8cf68d3573defd947efbc6c3c43
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "85801678"
---
Azure IoT Edge 的主要功能之一，是能夠從雲端將程式碼部署到您的 IoT Edge 裝置。 *IoT Edge 模組*是實作為容器的可執行檔套件。 在本節中，您會直接透過 Azure IoT 中樞，從 [Azure Marketplace 的 IoT Edge 模組區段](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)部署預先建置的模組。

您在本節中部署的模組會模擬感應器，並傳送產生的資料。 當您開始使用 IoT Edge 時，此模組會是很有用的一組程式碼，因為您可以使用模擬的資料進行開發和測試。 如果您想要清楚檢視此模組的運作情形，您可以檢視[模擬溫度感應器的原始程式碼](https://github.com/Azure/iotedge/blob/027a509549a248647ed41ca7fe1dc508771c8123/edge-modules/SimulatedTemperatureSensor/src/Program.cs)。

若要從 Azure Marketplace 部署您的第一個模組，請使用下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後瀏覽至 IoT 中樞。

1. 從左窗格上的功能表，在 [自動裝置管理] 下方選取 [IoT Edge]。

1. 從裝置清單中按一下目標裝置的裝置識別碼。

1. 在上方列中，選取 [設定模組]。

   ![從裝置詳細資料頁面選取 [設定模組]](./media/iot-edge-deploy-module/select-set-modules.png)

1. 在頁面的 [IoT Edge 模組] 區段中，按一下 [新增]，然後從下拉式功能表中選取 [Marketplace 模組]。

   ![新增 Marketplace 模組](./media/iot-edge-deploy-module/add-marketplace-module.png)

1. 在 [IoT Edge 模組 Marketplace]中，搜尋「模擬溫度感應器」，然後選取該模組。

1. 請注意，SimulatedTemperatureSensor 模組會新增至 [IoT Edge 模組] 區段中，具有想要的**執行中**狀態。

   完成時，選取 [下一步:**路由]** ，繼續進行精靈的下一個步驟。

   ![一旦列出溫度感應器模組之後，移至下一個步驟](./media/iot-edge-deploy-module/view-temperature-sensor-next-routes.png)

1. 在精靈的 [路由] 索引標籤上，您可以定義要用來在模組與 IoT 中樞之間傳遞訊息的方式。 路由會使用名稱/值組構成。 您應該會在此頁面上看到兩個路由。 預設路由稱為 **route**，會將所有訊息傳送至 IoT 中樞 (稱為 `$upstream`)。 第二個路由稱為 **SimulatedTemperatureSensorToIoTHub**，會在您從 Marketplace 新增模組時自動建立。 此路由會將所有訊息明確地從模擬的溫度模組傳送到 IoT 中樞。 因為在此情況下預設路由是多餘的，所以您可以刪除預設路由。

   完成時，選取 [下一步:**檢閱 + 建立]** ，繼續進行精靈的下一個步驟。

   ![刪除預設路由，然後移至下一個步驟](./media/iot-edge-deploy-module/delete-route-next-review-create.png)

1. 在精靈的 [檢閱 + 建立] 索引標籤上，您可以預覽 JSON 檔案；此檔案定義了所有部署至 IoT Edge 裝置的模組。 請注意，**SimulatedTemperatureSensor** 模組也會包含在其中，此外還有兩個執行階段模組，**edgeAgent** 和 **edgeHub**。 完成檢閱後，請選取 [建立]。

   當您將新的部署提交至 IoT Edge 裝置時，並沒有任何項目會推送至您的裝置。 此時，裝置會定期查詢 IoT 中樞以取得新的指示。 裝置在發現更新的部署資訊清單時，即會使用新部署的相關資訊從雲端提取模組映像，然後開始在本機執行模組。 此流程可能需要幾分鐘的時間。

1. 在您建立模組部署詳細資料之後，精靈會讓您回到裝置詳細資料頁面。 在裝置詳細資料頁面中，檢視 [模組] 索引標籤中的部署狀態。該處應會列出三個模組：$edgeAgent、$edgeHub 和 SimulatedTemperatureSensor。 如果有一或多個模組已依照部署中的指定列出，但裝置尚未加以報告，表示您的 IoT Edge 裝置仍在啟動這些模組。 請稍待片刻，然後選取頁面頂端的 [重新整理]。

   ![在已部署的模組清單中檢視 SimulatedTemperatureSensor](./media/iot-edge-deploy-module/view-deployed-modules.png)
