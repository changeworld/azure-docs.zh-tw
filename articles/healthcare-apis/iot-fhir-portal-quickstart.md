---
title: 快速入門：使用 Azure 入口網站部署 IoT 連接器 (預覽)
description: 在此快速入門中，您將了解如何使用 Azure 入口網站部署、設定和使用 Azure API for FHIR 的 IoT 連接器功能。
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: quickstart
ms.date: 05/11/2020
ms.author: punagpal
ms.openlocfilehash: bc85765666ba3baeae7ec795118f615f6b4b4368
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87100832"
---
# <a name="quickstart-deploy-iot-connector-preview-using-azure-portal"></a>快速入門：使用 Azure 入口網站部署 IoT 連接器 (預覽)

IoT 連接器是 Azure API for FHIR 的一個選用功能，可讓您內嵌來自醫學物聯網 (IoMT) 裝置的資料。 此外，在預覽階段，IoT 連接器功能可供免費使用。 在本快速入門中，您將了解如何：
- 使用 Azure 入口網站部署和設定 IoT 連接器
- 使用模擬裝置將資料傳送至 IoT 連接器
- 在 Azure API for FHIR 上檢視 IoT 連接器所建立的資源

## <a name="prerequisites"></a>必要條件

- 有效的 Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure API for FHIR 資源 - [使用 Azure 入口網站部署 Azure API for FHIR](fhir-paas-portal-quickstart.md)

## <a name="go-to-azure-api-for-fhir-resource"></a>移至 Azure API for FHIR 資源

開啟 [Azure 入口網站](https://portal.azure.com)，並移至您想要為其建立 IoT 連接器功能的 **Azure API for FHIR** 資源。

[![Azure API for FHIR 資源](media/quickstart-iot-fhir-portal/portal-azure-api-fhir.jpg)](media/quickstart-iot-fhir-portal/portal-azure-api-fhir.jpg#lightbox)

在左側導覽功能表上，按一下 [增益集] 區段底下的 [IoT 連接器 (預覽)]，以開啟 [IoT 連接器] 頁面。

[![IoT 連接器功能](media/quickstart-iot-fhir-portal/portal-iot-connectors.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connectors.jpg#lightbox)

## <a name="create-new-iot-connector-preview"></a>建立新的 IoT 連接器 (預覽)

按一下 [新增] 按鈕，以開啟 [建立 IoT 連接器] 頁面。

[![新增 IoT 連接器](media/quickstart-iot-fhir-portal/portal-iot-connectors-add.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connectors-add.jpg#lightbox)

輸入新 IoT 連接器的設定。 按一下 [建立] 按鈕，並等待 IoT 連接器部署。

> [!NOTE]
> 必須選取 [建立] 作為此安裝的 [解析類型] 下拉式清單的值。 

[![建立 IoT 連接器](media/quickstart-iot-fhir-portal/portal-iot-connector-create.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-create.jpg#lightbox)

|設定|值|說明 |
|---|---|---|
|連接器名稱|唯一的名稱|輸入用來識別 IoT 連接器的名稱。 此名稱在 Azure API for FHIR 資源中應該是唯一的。 名稱只能包含小寫字母、數字及連字號 (-) 字元。 其開頭和結尾必須是字母或數字，而且長度必須介於 3-24 個字元之間。|
|解析類型|查閱或建立|如果您有頻外程序可以在 Azure API for FHIR 中建立[裝置](https://www.hl7.org/fhir/device.html) \(英文\) 和[患者](https://www.hl7.org/fhir/patient.html) \(英文\) FHIR 資源，請選取 [查閱]。 IoT 連接器將會在建立[觀察](https://www.hl7.org/fhir/observation.html) \(英文\) FHIR 資源以代表裝置資料時，使用這些資源的參考。 當您希望 IoT 連接器使用裝置資料中出現的個別識別碼值，在您的 Azure API for FHIR 中建立準系統裝置和患者資源時，請選取 [建立]。|

安裝完成之後，新建立的 IoT 連接器將會顯示在 [IoT 連接器] 頁面上。

[![IoT 連接器已建立](media/quickstart-iot-fhir-portal/portal-iot-connector-created.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-created.jpg#lightbox)

## <a name="configure-iot-connector-preview"></a>設定 IoT 連接器 (預覽)

IoT 連接器需要兩個對應範本，才能將裝置訊息轉換成 FHIR 型觀察資源：**裝置對應**和 **FHIR 對應**。 在上傳這些對應之前，您的 IoT 連接器無法完全正常運作。

[![IoT 連接器遺漏的對應](media/quickstart-iot-fhir-portal/portal-iot-connector-missing-mappings.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-missing-mappings.jpg#lightbox)

若要上傳對應範本，請按一下新部署的 IoT 連接器，以移至 [IoT 連接器] 頁面。

[![IoT 連接器按一下](media/quickstart-iot-fhir-portal/portal-iot-connector-click.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click.jpg#lightbox)

#### <a name="device-mapping"></a>裝置對應

裝置對應範本會將裝置資料轉換成標準化結構描述。 在 [IoT 連接器] 頁面上，按一下 [設定裝置對應] 按鈕以移至 [裝置對應] 頁面。 

[![IoT 連接器按一下設定裝置對應](media/quickstart-iot-fhir-portal/portal-iot-connector-click-device-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click-device-mapping.jpg#lightbox)

在 [裝置對應] 頁面上，將下列指令碼新增至 JSON 編輯器，然後按一下 [儲存]。

```json
{
  "templateType": "CollectionContent",
  "template": [
    {
      "templateType": "IotJsonPathContent",
      "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@Body.HeartRate)]",
        "patientIdExpression": "$.SystemProperties.iothub-connection-device-id",
        "values": [
          {
            "required": "true",
            "valueExpression": "$.Body.HeartRate",
            "valueName": "hr"
          }
        ]
      }
    }
  ]
}
```

[![IoT 連接器裝置對應](media/quickstart-iot-fhir-portal/portal-iot-device-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-device-mapping.jpg#lightbox)


#### <a name="fhir-mapping"></a>FHIR 對應

FHIR 對應範本會將標準化的訊息轉換成 FHIR 型觀察資源。 在 [IoT 連接器] 頁面上，按一下 [設定 FHIR 對應] 按鈕以移至 [FHIR 對應] 頁面。  

[![IoT 連接器按一下設定 FHIR 對應](media/quickstart-iot-fhir-portal/portal-iot-connector-click-fhir-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click-fhir-mapping.jpg#lightbox)

在 [FHIR 對應] 頁面上，將下列指令碼新增至 JSON 編輯器，然後按一下 [儲存]。

```json
{
  "templateType": "CollectionFhir",
  "template": [
    {
      "templateType": "CodeValueFhir",
      "template": {
        "codes": [
          {
            "code": "8867-4",
            "system": "http://loinc.org",
            "display": "Heart rate"
          }
        ],
        "periodInterval": 0,
        "typeName": "heartrate",
        "value": {
          "unit": "count/min",
          "valueName": "hr",
          "valueType": "Quantity"
        }
      }
    }
  ]
}
```

[![IoT 連接器 FHIR 對應](media/quickstart-iot-fhir-portal/portal-iot-fhir-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-fhir-mapping.jpg#lightbox)

## <a name="generate-a-connection-string"></a>產生連接字串

IoMT 裝置需要一個連接字串，才能將訊息連線並傳送至 IoT 連接器。 在新部署的 IoT 連接器的 [IoT 連接器] 頁面上，選取 [管理用戶端連線] 按鈕。 

[![IoT 連接器按一下管理用戶端連線](media/quickstart-iot-fhir-portal/portal-iot-connector-click-client-connections.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click-client-connections.jpg#lightbox)

在 [連線] 頁面上，按一下 [新增] 按鈕，以建立新連線。 

[![IoT 連接器連線](media/quickstart-iot-fhir-portal/portal-iot-connections.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connections.jpg#lightbox)

在重疊視窗上為此連線提供一個易記名稱，然後選取 [建立] 按鈕。

[![IoT 連接器新連線](media/quickstart-iot-fhir-portal/portal-iot-new-connection.jpg)](media/quickstart-iot-fhir-portal/portal-iot-new-connection.jpg#lightbox)

從 [連線] 頁面中選取新建立的連線，然後從右側的重疊視窗複製 [主要連接字串] 欄位的值。

[![IoT 連接器連接字串](media/quickstart-iot-fhir-portal/portal-iot-connection-string.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connection-string.jpg#lightbox)

保留此連接字串以便在稍後的步驟中使用。 

## <a name="connect-your-devices-to-iot"></a>將裝置連線至 IoT

Azure 提供一套廣泛的 IoT 產品，可連線並管理您的 IoT 裝置。 您可以根據 PaaS，使用 Azure IoT 中樞建置自己的解決方案，或從使用 Azure IoT Central 管理 IoT 應用程式平台開始。 在此教學課程中，我們將利用 Azure IoT Central 中，專門針對產業提供的解決方案範本，協助您開始使用。

部署[持續性患者監視應用程式範本](https://docs.microsoft.com/azure/iot-central/healthcare/tutorial-continuous-patient-monitoring#create-an-application-template)。 此範本包含兩個可產生即時資料的模擬裝置，可協助您開始使用：**Smart Vitals Patch** 和 **Smart Knee Brace**。

> [!NOTE]
> 當您的實際裝置準備就緒時，您可以使用相同的 IoT Central 應用程式[將裝置上線](https://docs.microsoft.com/azure/iot-central/core/howto-set-up-template) \(部分機器翻譯\)，並取代裝置模擬器。 您的裝置資料也會自動開始流向 FHIR。 

## <a name="connect-your-iot-data-with-the-iot-connector-preview"></a>使用 IoT 連接器連線 IoT 資料 (預覽)
部署 IoT Central 應用程式之後，您的兩個現成的模擬裝置將會開始產生遙測資料。 在此教學課程中，我們會透過 IoT 連接器，將 *Smart Vitals Patch* 模擬器的遙測資料內嵌到 FHIR 中。 若要將您的 IoT 資料匯出至 IoT 連接器，我們會想要[在 IoT Central 中設定連續資料匯出](https://docs.microsoft.com/azure/iot-central/core/howto-export-data#set-up-data-export)。 在連續資料匯出頁面上：
- 挑選 [Azure 事件中樞] 作為匯出目的地。
- 針對 [事件中樞命名空間] 欄位，選取 [使用連接字串] 值。
- 針對 [連接字串] 欄位，提供上一個步驟中取得的 IoT 連接器的連接字串。
- 在 [要匯出的資料] 欄位上，將 [遙測] 選項維持 [開啟] 狀態。

## <a name="view-device-data-in-azure-api-for-fhir"></a>在 Azure API for FHIR 中檢視裝置資料

您可以使用 Postman，在 Azure API for FHIR 上檢視 IoT 連接器所建立的 FHIR 型觀察資源。 設定您的 [Postman 以存取 Azure API for FHIR](access-fhir-postman-tutorial.md)，並向 `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` 提出 `GET` 要求，以檢視含有心率值的觀察 FHIR 資源。 

> [!TIP]
> 請確定您的使用者擁有適當的 Azure API for FHIR 資料平面存取權。 使用 [Azure 角色型存取控制](configure-azure-rbac.md)，指派必要的資料平面角色。

## <a name="clean-up-resources"></a>清除資源

若不再需要，您可以移除相關聯的資源群組或相關聯的 Azure API for FHIR 服務，或 IoT 連接器執行個體本身，以刪除 IoT 連接器的執行個體。 

若要直接移除 IoT 連接器執行個體，請從 [IoT 連接器] 頁面選取執行個體，以移至 [IoT 連接器] 頁面，然後按一下 [刪除] 按鈕。 當系統要求您確認時，請選取 [是]。 

[![刪除 IoT 連接器執行個體](media/quickstart-iot-fhir-portal/portal-iot-connector-delete.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-delete.jpg#lightbox)

## <a name="next-steps"></a>後續步驟

在此快速入門指南中，您已經在 Azure API for FHIR 資源中部署 IoT 連接器功能。 從下面的後續步驟中選取各項主題，以深入了解 IoT 連接器：

了解 IoT 連接器內不同的資料流程階段。

>[!div class="nextstepaction"]
>[IoT 連接器資料流程](iot-data-flow.md)

了解如何使用裝置和 FHIR 對應範本來設定 IoT 連接器。

>[!div class="nextstepaction"]
>[IoT 連接器對應範本](iot-mapping-templates.md)

FHIR 是 HL7 的註冊商標，必須搭配 HL7 權限方可使用。
