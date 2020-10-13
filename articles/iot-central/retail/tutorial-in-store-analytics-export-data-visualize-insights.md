---
title: 教學課程 - 在 Azure IoT Central 中匯出資料並以視覺化方式呈現見解
description: 在本教學課程中，了解如何從 IoT Central 匯出資料，並在 Power BI 儀表板中以視覺化方式呈現見解。
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: dobett
author: dominicbetts
ms.date: 11/12/2019
ms.openlocfilehash: f00448f19cc0a2118477a9527005548fea25537e
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2020
ms.locfileid: "91651440"
---
# <a name="tutorial-export-data-from-azure-iot-central-and-visualize-insights-in-power-bi"></a>教學課程：從 Azure IoT Central 匯出資料並以視覺化方式呈現 Power BI 中的見解



在先前的兩個教學課程中，您使用**店內分析 - 結帳**應用程式範本建立和自訂 IoT Central 應用程式。 在本教學課程中，您將設定 IoT Central 應用程式以匯出從裝置收集的遙測資料。 您接著將使用 Power BI 為商店經理建立自訂儀表板，以視覺化方式呈現從遙測得到的見解。

在本教學課程中，您將學會如何：
> [!div class="checklist"]
> * 設定 IoT Central 應用程式，以便將遙測資料匯出至事件中樞。
> * 使用 Logic Apps 從事件中樞將資料傳送到 Power BI 串流資料集。
> * 建立 Power BI 儀表板，以視覺化方式呈現串流資料集中的資料。

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程，您需要：

* 若要完成前兩個教學課程，請[在 Azure IoT Central 中建立店內分析應用程式](./tutorial-in-store-analytics-create-app.md)，而且[自訂操作員儀表板並在 Azure IoT Central 中管理裝置](./tutorial-in-store-analytics-customize-dashboard.md)。
* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* Power BI 帳戶。 如果您沒有 Power BI 帳戶，請在開始前先註冊[免費的 Power BI Pro 試用版](https://app.powerbi.com/signupredirect?pbi_source=web)。

## <a name="create-a-resource-group"></a>建立資源群組

建立事件中樞和邏輯應用程式之前，需要先建立一個管理用的資源群組。 資源群組應與您的**店內分析 - 結帳** IoT Central 應用程式位於同一個位置。 若要建立資源群組︰

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左側瀏覽中，選取 [資源群組]  。 然後選取 [新增]  。
1. 對於**訂用帳戶**，選取用於建立 IoT Central 應用程式的 Azure 訂用帳戶的名稱。
1. 對於**資源群組**名稱，輸入 _retail-store-analysis_*。
1. 對於**區域**，選取您為 IoT Central 應用程式選擇的同一個區域。
1. 選取 [檢閱 + 建立]  。
1. 在 [檢閱 + 建立]  頁面上，選取 [建立]  。

您的訂用帳戶中現在有一個名為 **retail-store-analysis** 的資源群組。

## <a name="create-an-event-hub"></a>建立事件中樞

在設定零售監視應用程式匯出遙測資料之前，您需要先建立事件中樞來接收匯出的資料。 下列步驟顯示如何建立事件中樞：

1. 在 Azure 入口網站中，選取畫面左上方的 [建立資源]  。
1. 在 [搜尋 Marketplace]  中，輸入_事件中樞_，然後按 **Enter**。
1. 在 [事件中樞]  頁面上，選取 [建立]  。
1. 在 [建立命名空間]  頁面上，採取下列步驟：
    * 輸入命名空間的唯一名稱，例如_您的名稱-retail-store-analysis_。 系統會檢查此名稱是否可用。
    * 選擇 [基本]  定價層。
    * 選取建立 IoT Central 應用程式所用的同一個**訂用帳戶**。
    * 選取 **retail-store-analysis**資源群組。
    * 選取您用於 IoT Central 應用程式的位置。
    * 選取 [建立]  。 您可能必須等待幾分鐘，系統才會佈建資源。
1. 在入口網站中，瀏覽至 **retail-store-analysis** 資源群組。 等待部署完成。 您可能需要選取 [重新整理]  才能更新部署狀態。 您也可以在 [通知]  中檢查事件中樞命名空間建立的狀態。
1. 在 **retail-store-analysis** 資源群組中，選取 [事件中樞命名空間]  。 您會在入口網站中看到**事件中樞命名空間**的首頁。

現在，您有了**事件中樞命名空間**，可以建立**事件中樞**與 IoT Central 應用程式搭配使用：

1. 在入口網站的**事件中樞命名空間**首頁上，選取 [+ 事件中樞]  。
1. 在 [建立事件中樞]  頁面上，輸入 _store-telemetry_ 作為名稱，然後選取 [建立]  。

現在，您有一個事件中樞，可以在從 IoT Central 應用程式設定資料匯出時使用：

![事件中樞](./media/tutorial-in-store-analytics-visualize-insights/event-hub.png)

## <a name="configure-data-export"></a>設定資料匯出

現在，您有了事件中樞，可以設定**店內分析 - 結帳**應用程式，進而從連線的裝置匯出遙測資料。 下列步驟顯示如何設定匯出：

1. 登入您的**店內分析 - 結帳** IoT Central 應用程式。
1. 在左側窗格中選取 [資料匯出]  。
1. 選取 [新增] > [Azure 事件中樞]  。
1. 輸入_遙測匯出_作為**顯示名稱**。
1. 選取您的**事件中樞命名空間**。
1. 選取**商店遙測**事件中樞。
1. 關閉 [將匯出的資料]  區段中的**裝置**和**裝置範本**。
1. 選取 [儲存]  。

資料匯出可能需要幾分鐘才能開始將遙測資料傳送到事件中樞。 您可以在 [資料匯出]  頁面上查看匯出的狀態：

![連續資料匯出設定](./media/tutorial-in-store-analytics-visualize-insights/export-configuration.png)

## <a name="create-the-power-bi-datasets"></a>建立 Power BI 資料集

您的 Power BI 儀表板將顯示來自零售監視應用程式的資料。 在此解決方案中，您將使用 Power BI 串流資料集作為 Power BI 儀表板的資料來源。 在此區段中，您將定義串流資料集的結構描述，以便邏輯應用程式可以轉送來自事件中樞的資料。 下列步驟顯示如何為環境感應器建立兩個串流資料集，並為佔用量感應器建立一個串流資料集：

1. 登入您的 **Power BI** 帳戶。
1. 選取 [工作區]  ，然後選取 [建立工作區]  。
1. 在 [建立工作空間]  頁面上，輸入_店內分析 - 結帳_為**工作空間名稱**。
1. 捲動到 [歡迎使用店內分析 - 結帳工作區]  頁面的底部，然後選取 [跳過]  。
1. 在工作區頁面上，選取 [建立] > [串流資料集]  。
1. 在 [新的串流資料集]  頁面上，選擇 [API]  ，然後選取 [下一步]  。
1. 輸入_區域 1 感應器_作為**資料集名稱**。
1. 在下表中輸入三個**來自串流的值**：

    | 值名稱  | 值類型 |
    | ----------- | ---------- |
    | 時間戳記   | Datetime   |
    | 溼度    | Number     |
    | 溫度 | Number     |

1. 開啟**歷程資料分析**。
1. 選取 [建立]  ，然後選取 [完成]  。
1. 使用與**區域 1 感應器**串流資料集相同的結構描述和設定，建立另一個名為**區域 2 感應器**的串流資料集。

現在，您有兩個串流資料集。 邏輯應用程式會從與您的**店內分析 - 結帳**應用程式連線的兩個環境感應器，將遙測資料傳送到這兩個資料集：

![區域資料集](./media/tutorial-in-store-analytics-visualize-insights/dataset-1.png)

此解決方案針對每個感應器使用一個串流資料集，因為無法將篩選條件套用於 Power BI 中的串流資料。

您也需要佔用量遙測資料的串流資料集：

1. 在工作區頁面上，選取 [建立] > [串流資料集]  。
1. 在 [新的串流資料集]  頁面上，選擇 [API]  ，然後選取 [下一步]  。
1. 輸入_佔用量感應器_作為**資料集名稱**。
1. 在下表中輸入五個**來自串流的值**：

    | 值名稱     | 值類型 |
    | -------------- | ---------- |
    | 時間戳記      | Datetime   |
    | 佇列長度 1 | Number     |
    | 佇列長度 2 | Number     |
    | 停留時間 1   | Number     |
    | 停留時間 2   | Number     |

1. 開啟**歷程資料分析**。
1. 選取 [建立]  ，然後選取 [完成]  。

現在，您有第三個串流資料集，這會儲存模擬佔用量感應器的值。 此感應器會報告在商店的兩個結帳處排隊的佇列長度，以及客戶在這些排隊的佇列中時等待的時間：

![佔用量資料集](./media/tutorial-in-store-analytics-visualize-insights/dataset-2.png)

## <a name="create-a-logic-app"></a>建立邏輯應用程式

在此解決方案中，邏輯應用程式會從事件中樞讀取遙測資料、剖析資料，然後傳送到您建立的 Power BI 串流資料集。

您先前曾於[在 Azure IoT Central 中建立店內分析應用程式](./tutorial-in-store-analytics-create-app.md)教學課程中透過兩個 RuuviTag 感應器連線至 IoT Central 應用程式，建立邏輯應用程式之前，您需要這兩個 RuuviTag 感應器所用的裝置識別碼：

1. 登入您的**店內分析 - 結帳** IoT Central 應用程式。
1. 在左側窗格中，選取 [裝置]  。 然後選取 [RuuviTag]  。
1. 記下**裝置識別碼**。 在下列螢幕擷取畫面中，識別碼為 **f5dcf4ac32e8** 和 **e29ffc8d5326**：

    ![裝置識別碼](./media/tutorial-in-store-analytics-visualize-insights/device-ids.png)

下列步驟顯示如何在 Azure 入口網站建立邏輯應用程式：

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後選取畫面左上方的 [建立資源]  。
1. 在 [搜尋 Marketplace]  中，輸入_邏輯應用程式_，然後按 **Enter**。
1. 在 [邏輯應用程式]  頁面上，選取 [建立]  。
1. 在 [**邏輯應用程式**建立] 頁面上：
    * 輸入邏輯應用程式的唯一名稱，例如_您的名稱-retail-store-analysis_。
    * 選取建立 IoT Central 應用程式所用的同一個**訂用帳戶**。
    * 選取 **retail-store-analysis**資源群組。
    * 選取您用於 IoT Central 應用程式的位置。
    * 選取 [建立]  。 您可能必須等待幾分鐘，系統才會佈建資源。
1. 在 Azure 入口網站中，瀏覽至新的邏輯應用程式。
1. 在 [Logic Apps 設計工具]  頁面上，向下捲動並選取 [空白邏輯應用程式]  。
1. 在 [搜尋連接器和觸發程序]  中，輸入_事件中樞_。
1. 在 [觸發程序]  中，選取 [當事件可用於事件中樞時]  。
1. 輸入_商店遙測資料_作為**連線名稱**，然後選取 [事件中樞命名空間]  。
1. 選取 **RootManageSharedAccess** 原則，然後選取 [建立]  。
1. 在**當事件可用於事件中樞時**動作中：
    * 在 [事件中樞名稱]  中，選取 [store-telemetry]  。
    * 在 [內容類型]  中，選取 [application/json]  。
    * 將 [間隔]  設定為 3，將 [頻率]  設定為秒
1. 選取 [儲存]  儲存您的邏輯應用程式。

若要將邏輯新增至邏輯應用程式設計中，請選取 [程式碼檢視]  ：

1. 將 `"actions": {},` 取代為下列 JSON。 將兩個預留位置 `[YOUR RUUVITAG DEVICE ID 1]` 和 `[YOUR RUUVITAG DEVICE ID 2]` 取代為您為兩個 RuuviTag 裝置記下的識別碼：

    ```json
    "actions": {
        "Initialize_Device_ID_variable": {
            "inputs": {
                "variables": [
                    {
                        "name": "DeviceID",
                        "type": "String"
                    }
                ]
            },
            "runAfter": {},
            "type": "InitializeVariable"
        },
        "Initialize_Interface_ID_variable": {
            "inputs": {
                "variables": [
                    {
                        "name": "InterfaceID",
                        "type": "String",
                        "value": "Other"
                    }
                ]
            },
            "runAfter": {
                "Initialize_Device_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "InitializeVariable"
        },
        "Parse_Properties": {
            "inputs": {
                "content": "@triggerBody()?['Properties']",
                "schema": {
                    "properties": {
                        "iothub-connection-auth-generation-id": {
                            "type": "string"
                        },
                        "iothub-connection-auth-method": {
                            "type": "string"
                        },
                        "iothub-connection-device-id": {
                            "type": "string"
                        },
                        "iothub-enqueuedtime": {
                            "type": "string"
                        },
                        "iothub-interface-name": {
                            "type": "string"
                        },
                        "iothub-message-source": {
                            "type": "string"
                        },
                        "x-opt-enqueued-time": {
                            "type": "string"
                        },
                        "x-opt-offset": {
                            "type": "string"
                        },
                        "x-opt-sequence-number": {
                            "type": "integer"
                        }
                    },
                    "type": "object"
                }
            },
            "runAfter": {
                "Initialize_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "ParseJson"
        },
        "Parse_Telemetry": {
            "inputs": {
                "content": "@triggerBody()?['ContentData']",
                "schema": {
                    "properties": {
                        "DwellTime1": {
                            "type": "number"
                        },
                        "DwellTime2": {
                            "type": "number"
                        },
                        "count1": {
                            "type": "number"
                        },
                        "count2": {
                            "type": "number"
                        },
                        "humidity": {
                            "type": "number"
                        },
                        "temperature": {
                            "type": "number"
                        }
                    },
                    "type": "object"
                }
            },
            "runAfter": {
                "Initialize_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "ParseJson"
        },
        "Set_Device_ID_variable": {
            "inputs": {
                "name": "DeviceID",
                "value": "@body('Parse_Properties')?['iothub-connection-device-id']"
            },
            "runAfter": {
                "Parse_Properties": [
                    "Succeeded"
                ]
            },
            "type": "SetVariable"
        },
        "Set_Interface_ID_variable": {
            "inputs": {
                "name": "InterfaceID",
                "value": "@body('Parse_Properties')?['iothub-interface-name']"
            },
            "runAfter": {
                "Set_Device_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "SetVariable"
        },
        "Switch_by_DeviceID": {
            "cases": {
                "Occupancy": {
                    "actions": {
                        "Switch_by_InterfaceID": {
                            "cases": {
                                "Dwell_Time_interface": {
                                    "actions": {},
                                    "case": "RS40_Occupancy_Sensor_v2_1l0"
                                },
                                "People_Count_interface": {
                                    "actions": {},
                                    "case": "RS40_Occupancy_Sensor_iv"
                                }
                            },
                            "default": {
                                "actions": {}
                            },
                            "expression": "@variables('InterfaceID')",
                            "runAfter": {},
                            "type": "Switch"
                        }
                    },
                    "case": "Occupancy"
                },
                "Zone 2 environment": {
                    "actions": {},
                    "case": "[YOUR RUUVITAG DEVICE ID 2]"
                },
                "Zone_1_environment": {
                    "actions": {},
                    "case": "[YOUR RUUVITAG DEVICE ID 1]"
                }
            },
            "default": {
                "actions": {}
            },
            "expression": "@variables('DeviceID')",
            "runAfter": {
                "Parse_Telemetry": [
                    "Succeeded"
                ],
                "Set_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "Switch"
        }
    },
    ```

1. 選取 [儲存]  ，然後選取 [設計工具]  ，查看您新增的邏輯視覺效果版本：

    ![邏輯應用程式設計](./media/tutorial-in-store-analytics-visualize-insights/logic-app.png)

1. 選取 [依 DeviceID 切換]  展開動作。 然後選取 [區域 1 環境]  ，然後選取 [新增動作]  。
1. 在 [搜尋連接器和動作]  中，輸入 **Power BI**，然後按 **Enter**。
1. 選取 [將資料列新增至資料集 (預覽)]  動作。
1. 選取 [登入]  ，然後按照提示登入 Power BI 帳戶。
1. 登入流程完成後，在 [將資料列新增至資料集]  動作中：
    * 選取 [店內分析 - 結帳]  作為工作區。
    * 選取 [區域 1 感應器]  作為資料集。
    * 選取 [RealTimeData]  作為資料表。
    * 選取 [新增參數]  ，然後選取 [時間戳記]  、[濕度]  和 [溫度]  欄位。
    * 選取 [時間戳記]  欄位，然後從 [動態內容]  清單中選取 [x-opt-enqueuedtime]  。
    * 選取 [濕度]  欄位，然後選取 [剖析遙測]  旁的 [查看更多]  。 然後選取 [濕度]  。
    * 選取 [溫度]  欄位，然後選取 [剖析遙測]  旁的 [查看更多]  。 然後選取 [溫度]  。
    * 選取 [儲存]  來儲存變更。 **區域 1 環境**動作如下列螢幕擷取畫面所示：![區域 1 環境](./media/tutorial-in-store-analytics-visualize-insights/zone-1-action.png)
1. 選取 [區域 2 環境]  動作，然後選取 [新增動作]  。
1. 在 [搜尋連接器和動作]  中，輸入 **Power BI**，然後按 **Enter**。
1. 選取 [將資料列新增至資料集 (預覽)]  動作。
1. 在 [將資料列新增至資料集 2]  動作中：
    * 選取 [店內分析 - 結帳]  作為工作區。
    * 選取 [區域 2 感應器]  作為資料集。
    * 選取 [RealTimeData]  作為資料表。
    * 選取 [新增參數]  ，然後選取 [時間戳記]  、[濕度]  和 [溫度]  欄位。
    * 選取 [時間戳記]  欄位，然後從 [動態內容]  清單中選取 [x-opt-enqueuedtime]  。
    * 選取 [濕度]  欄位，然後選取 [剖析遙測]  旁的 [查看更多]  。 然後選取 [濕度]  。
    * 選取 [溫度]  欄位，然後選取 [剖析遙測]  旁的 [查看更多]  。 然後選取 [溫度]  。
    選取 [儲存]  來儲存變更。  **區域 2 環境**動作如下列螢幕擷取畫面所示：![區域 2 環境](./media/tutorial-in-store-analytics-visualize-insights/zone-2-action.png)
1. 選取 [佔用量]  動作，然後選取 [依介面識別碼切換]  動作。
1. 選取 [停留時間介面]  動作，然後選取 [新增動作]  。
1. 在 [搜尋連接器和動作]  中，輸入 **Power BI**，然後按 **Enter**。
1. 選取 [將資料列新增至資料集 (預覽)]  動作。
1. 在 [將資料列新增至資料集]  動作中：
    * 選取 [店內分析 - 結帳]  作為工作區。
    * 選取 [佔用量感應器]  作為資料集。
    * 選取 [RealTimeData]  作為資料表。
    * 選取 [新增參數]  ，然後選取 [時間戳記]  、[停留時間 1]  和 [停留時間 2]  欄位。
    * 選取 [時間戳記]  欄位，然後從 [動態內容]  清單中選取 [x-opt-enqueuedtime]  。
    * 選取 [停留時間 1]  欄位，然後選取 [剖析遙測]  旁的 [查看更多]  。 然後選取 [DwellTime1]  。
    * 選取 [停留時間 2]  欄位，然後選取 [剖析遙測]  旁的 [查看更多]  。 然後選取 [DwellTime2]  。
    * 選取 [儲存]  來儲存變更。 **停留時間介面**動作如下列螢幕擷取畫面所示：![顯示「停留時間介面」動作的螢幕擷取畫面。](./media/tutorial-in-store-analytics-visualize-insights/occupancy-action-1.png)
1. 選取 [人員計數介面]  動作，然後選取 [新增動作]  。
1. 在 [搜尋連接器和動作]  中，輸入 **Power BI**，然後按 **Enter**。
1. 選取 [將資料列新增至資料集 (預覽)]  動作。
1. 在 [將資料列新增至資料集]  動作中：
    * 選取 [店內分析 - 結帳]  作為工作區。
    * 選取 [佔用量感應器]  作為資料集。
    * 選取 [RealTimeData]  作為資料表。
    * 選取 [新增參數]  ，然後選取 [時間戳記]  、[佇列長度 1]  和 [佇列長度 2]  欄位。
    * 選取 [時間戳記]  欄位，然後從 [動態內容]  清單中選取 [x-opt-enqueuedtime]  。
    * 選取 [佇列長度 1]  欄位，然後選取 [剖析遙測]  旁的 [查看更多]  。 然後選取 [count1]  。
    * 選取 [佇列長度 2]  欄位，然後選取 [剖析遙測]  旁的 [查看更多]  。 然後選取 [count2]  。
    * 選取 [儲存]  來儲存變更。 **人員計數介面**動作如下列螢幕擷取畫面所示：![佔用量動作](./media/tutorial-in-store-analytics-visualize-insights/occupancy-action-2.png)

邏輯應用程式將自動執行。 若要查看每次執行的狀態，請在 Azure 入口網站中瀏覽至邏輯應用程式的 [概觀]  頁面：

## <a name="create-a-power-bi-dashboard"></a>建立 Power BI 儀表板

現在，您有從 IoT Central 應用程式透過事件中樞傳輸的遙測資料。 然後，您的邏輯應用程式將剖析事件中樞訊息，並將這些訊息新增至 Power BI 串流資料集。 現在，您可以建立 Power BI 儀表板以視覺化方式呈現遙測資料：

1. 登入您的 **Power BI** 帳戶。
1. 選取 [工作區] > [店內分析 - 結帳]  。
1. 選取 [建立] > [儀表板]  。
1. 輸入**商店分析**作為儀表板名稱，然後選取 [建立]  。

### <a name="add-line-charts"></a>新增折線圖

新增四個折線圖圖格，以顯示兩個環境感應器的溫度和濕度。 使用下表中的資訊建立圖格。 若要新增每個圖格，請先選取 [...(更多選項)] > [新增圖格]  。 選取 [自訂串流資料]  ，然後選取 [下一步]  ：

| 設定 | 圖表 #1 | 圖表 #2 | 圖表 #3 | 圖表 #4 |
| ------- | -------- | -------- | -------- | -------- |
| 資料集 | 區域 1 感應器 | 區域 1 感應器 | 區域 2 感應器 | 區域 2 感應器 |
| 視覺效果類型 | 折線圖 | 折線圖 | 折線圖 | 折線圖 |
| 軸 | 時間戳記 | 時間戳記 | 時間戳記 | 時間戳記 |
| 值 | 溫度 | 溼度 | 溫度 | 溼度 |
| 時間範圍 | 60 Minuten | 60 Minuten | 60 Minuten | 60 Minuten |
| Title | 溫度 (1 小時) | 濕度 (1 小時) | 溫度 (1 小時) | 濕度 (1 小時) |
| 副標題 | 區域 1 | 區域 1 | 區域 2 | 區域 2 |

下列螢幕擷取畫面顯示第一個圖表的設定：

![折線圖設定](./media/tutorial-in-store-analytics-visualize-insights/line-chart.png)

### <a name="add-cards-to-show-environmental-data"></a>新增卡片以顯示環境資料

新增四個卡片圖格，以顯示兩個環境感應器的最新溫度和濕度值。 使用下表中的資訊建立圖格。 若要新增每個圖格，請先選取 [...(更多選項)] > [新增圖格]  。 選取 [自訂串流資料]  ，然後選取 [下一步]  ：

| 設定 | 卡片 #1 | 卡片 #2 | 卡片 #3 | 卡片 #4 |
| ------- | ------- | ------- | ------- | ------- |
| 資料集 | 區域 1 感應器 | 區域 1 感應器 | 區域 2 感應器 | 區域 2 感應器 |
| 視覺效果類型 | 卡片 | 卡片 | 卡片 | 卡片 |
| 欄位 | 溫度 | 溼度 | 溫度 | 溼度 |
| Title | 溫度 (F) | 濕度 (%) | 溫度 (F) | 濕度 (%) |
| 副標題 | 區域 1 | 區域 1 | 區域 2 | 區域 2 |

下列螢幕擷取畫面顯示第一張卡的設定：

![卡片設定](./media/tutorial-in-store-analytics-visualize-insights/card-settings.png)

### <a name="add-tiles-to-show-checkout-occupancy-data"></a>新增圖格以顯示結帳佔用量資料

新增四個卡片圖格，以顯示在商店的兩個結帳處排隊的佇列長度和停留時間。 使用下表中的資訊建立圖格。 若要新增每個圖格，請先選取 [...(更多選項)] > [新增圖格]  。 選取 [自訂串流資料]  ，然後選取 [下一步]  ：

| 設定 | 卡片 #1 | 卡片 #2 | 卡片 #3 | 卡片 #4 |
| ------- | ------- | ------- | ------- | ------- |
| 資料集 | 佔用量感應器 | 佔用量感應器 | 佔用量感應器 | 佔用量感應器 |
| 視覺效果類型 | 群組直條圖 | 群組直條圖 | 量測計 | 量測計 |
| 軸    | 時間戳記 | 時間戳記 | N/A | N/A |
| 值 | 停留時間 1 | 停留時間 2 | 佇列長度 1 | 佇列長度 2 |
| 時間範圍 | 60 Minuten | 60 Minuten |  N/A | N/A |
| Title | 停留時間 | 停留時間 | 佇列長度 | 佇列長度 |
| 副標題 | 結帳 1 | 結帳 2 | 結帳 1 | 結帳 2 |

調整儀表板上的圖格大小並重新排列圖格，如下列螢幕擷取畫面所示：

![顯示 Power BI 儀表板 (具有已調整大小和重新排列磚) 的螢幕擷取畫面。](./media/tutorial-in-store-analytics-visualize-insights/pbi-dashboard.png)

您可以新增一些其他圖形資源以進一步自訂儀表板：

![Power BI 儀表板](./media/tutorial-in-store-analytics-visualize-insights/pbi-dashboard-graphics.png)

## <a name="clean-up-resources"></a>清除資源

如果已完成 IoT Central 應用程式，則可以登入應用程式並瀏覽至 [管理]  區段的 [應用程式設定]  頁面來予以刪除。

如果要保留應用程式但降低與其相關的成本，請停用將遙測資料傳送到事件中樞的資料匯出作業。

您可以刪除名為 **retail-store-analysis** 的資源群組，藉此刪除 Azure 入口網站中的事件中樞和邏輯應用程式。

您可以從工作區的 Power BI 設定頁面中刪除工作區，以刪除 Power BI 資料集和儀表板。

## <a name="next-steps"></a>後續步驟

這三篇教學課程展示使用**店內分析 - 結帳** IoT Central 應用程式範本的端對端解決方案。 您已將裝置連線至應用程式、使用 IoT Central 監視裝置，並使用 Power BI 建立儀表板，藉以查看來自裝置遙測資料的見解。 建議的下一個步驟是探索下列其他的 IoT Central 應用程式範本：

> [!div class="nextstepaction"]
> * [使用 IoT Central 建置能源解決方案](../energy/overview-iot-central-energy.md)
> * [使用 IoT Central 建置政府機關解決方案](../government/overview-iot-central-government.md)
> * [使用 IoT Central 建置醫療保健解決方案](../healthcare/overview-iot-central-healthcare.md)
