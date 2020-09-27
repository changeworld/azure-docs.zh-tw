---
title: 使用工作流程將您的 Azure IoT Central 應用程式與其他雲端服務整合 |Microsoft Docs
description: 本操作文章會示範身為建置人員的您應如何設定規則和動作，將 IoT Central 應用程式與其他雲端服務整合。 若要建立進階規則，您可以在 [Power Automate] 或 [Azure Logic Apps] 中使用 IoT Central 連接器。
author: dominicbetts
ms.author: dobett
ms.date: 05/12/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 037598212773ca75abbdd086fe0577e0660f2218
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2020
ms.locfileid: "91398573"
---
# <a name="use-workflows-to-integrate-your-azure-iot-central-application-with-other-cloud-services"></a>使用工作流程將您的 Azure IoT Central 應用程式與其他雲端服務整合

*本文適用於解決方案建置人員。*

您可以在 IoT Central 中建立規則，以觸發動作 (例如傳送電子郵件) 以回應以遙測為基礎的情況，例如裝置溫度超過閾值。

適用于 Power Automate 和 Azure Logic Apps 的 Azure IoT Central V3 連接器可讓您建立更多的 advanced 規則，以在 IoT Central 中將作業自動化：

- 當規則在您的 Azure IoT Central 應用程式中引發時，這會在 Power Automate 或 Azure Logic Apps 中觸發工作流程。 這些工作流程可以在其他雲端服務（例如 Microsoft 365 或協力廠商服務）中執行動作。
- 另一項雲端服務中的事件（例如 Microsoft 365）可以在 Power Automate 或 Azure Logic Apps 中觸發工作流程。 這些工作流程可以執行動作，或從您的 IoT Central 應用程式擷取資料。

## <a name="prerequisites"></a>必要條件

若要完成此操作指南中的步驟，您必須具備有效的 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

設定解決方案需要第3版 IoT Central 應用程式。 若要瞭解如何檢查您的應用程式版本，請參閱 [應用程式的相關資訊](./howto-get-app-info.md)。 若要瞭解如何建立 IoT Central 應用程式，請參閱 [建立 Azure IoT Central 應用程式](./quick-deploy-iot-central.md)。

> [!NOTE]
> 如果您使用第2版 IoT Central 應用程式，請參閱舊版檔網站 [Azure Logic Apps 中的 IoT Central 連接器組建工作流程](https://docs.microsoft.com/previous-versions/azure/iot-central/core/howto-build-azure-logic-apps) ，並使用 Azure IoT Central V2 連接器

## <a name="trigger-a-workflow-from-a-rule"></a>從規則引發工作流程

您在 Power Automate 或 Azure Logic Apps 中觸發工作流程之前，您的 Azure IoT Central 應用程式中需要規則。 若要深入瞭解，請參閱[在 Azure IoT Central 中設定規則和動作](./howto-configure-rules.md)。

若要在 Power Automate 中新增 **Azure IoT Central V3-preview** 連接器作為觸發程式：

1. 在 [Power Automate] 中，選取 [+ 建立]，選取 [自訂] 索引標籤。
1. 搜尋 *IoT Central*，然後選取 **Azure IoT Central V3-preview** 連接器。
1. 在觸發清單中，選取 [引發規則時 (預覽)]。
1. 在 [引發規則時] 步驟中，選取您的 IoT Central 應用程式和您所使用的規則。

若要在 Azure Logic Apps 中新增 **Azure IoT Central V3-preview** 連接器作為觸發程式：

1. 在 **Logic Apps 設計工具**中，選取 [空白邏輯應用程式] 範本。
1. 在設計工具中，選取 [自訂] 索引標籤。
1. 搜尋 *IoT Central*，然後選取 **Azure IoT Central V3-preview** 連接器。
1. 在觸發清單中，選取 [引發規則時 (預覽)]。
1. 在 [引發規則時] 步驟中，選取您的 IoT Central 應用程式和您所使用的規則。

:::image type="content" source="./media/howto-configure-rules-advanced/triggers.png" alt-text="尋找 Azure IoT Central - 預覽連接器，然後選擇觸發程序":::

您現在可以在工作流程中新增更多步驟，以建立您的整合案例。

## <a name="run-an-action"></a>執行動作

您可以在 IoT Central 應用程式中執行來自 Power Automate 和 Azure Logic Apps 工作流程的動作。 首先，建立您的工作流程，並使用連接器來定義啟動工作流程的觸發。 然後使用 **Azure IoT Central V3-preview** 連接器作為動作。

若要在 Power Automate 中新增 **Azure IoT Central V3-preview** 連接器作為動作：

1. 在 Power Automate 的 **[選擇動作]** 面板中，選取 [自訂] 索引標籤。
1. 搜尋 *IoT Central* ，然後選取 **Azure IoT Central V3-preview** 連接器。
1. 在動作清單中，選取您想要使用的 IoT Central 動作。
1. 在 [動作] 步驟中，針對您所選擇的動作完成設定。 然後選取 [儲存]。

若要在 Azure Logic Apps 中新增 **Azure IoT Central V3-preview** 連接器作為動作：

1. 在 **Logic Apps 設計工具**的 [選擇動作] 面板中，選取 [自訂] 索引標籤。
1. 搜尋 *IoT Central*，然後選取 **Azure IoT Central V3-preview** 連接器。
1. 在動作清單中，選取您想要使用的 IoT Central 動作。
1. 在 [動作] 步驟中，針對您所選擇的動作完成設定。 然後選取 [儲存]。

:::image type="content" source="./media/howto-configure-rules-advanced/actions.png" alt-text="尋找 Azure IoT Central V3 連接器，並選擇動作":::

## <a name="list-of-actions"></a>動作清單

下列清單顯示 **Azure IoT Central V3-preview** 連接器中的所有可用 IoT Central 動作及其設定選項。 許多欄位都可以動態產生內容。 例如，上一個步驟可以決定目前步驟執行所在的裝置識別碼。

### <a name="create-or-update-a-device"></a>建立或更新裝置

使用此動作來建立或更新 IoT Central 應用程式中的裝置。

| 欄位 | 描述 |
| ----- | ----------- |
| Application | 從您的 IoT Central 應用程式清單中選擇。 |
| 裝置 | 要建立或更新的裝置所用的唯一識別碼。 |
| 已核准 | 選擇裝置是否已獲准連接至 IoT Central。 |
| 裝置描述 | 裝置的詳細描述。 |
| 裝置名稱 | 裝置的顯示名稱。 |
| 裝置範本 | 在 IoT Central 應用程式的裝置範本清單中選擇。 |
| 模擬 | 選擇裝置是否為模擬。 |

### <a name="delete-a-device"></a>刪除裝置

使用此動作從您的 IoT Central 應用程式刪除裝置。

| 欄位 | 描述 |
| ----- | ----------- |
| Application | 從您的 IoT Central 應用程式清單中選擇。 |
| 裝置 | 要刪除的裝置所用的唯一識別碼。 |

### <a name="execute-a-device-command"></a>執行裝置命令

使用此動作可執行在其中一個裝置介面中定義的命令。

| 欄位 | 描述 |
| ----- | ----------- |
| Application | 從您的 IoT Central 應用程式清單中選擇。 |
| 裝置 | 要刪除的裝置所用的唯一識別碼。 |
| 裝置元件 | 裝置範本中包含命令的介面。 |
| 裝置命令 | 選擇所選取介面上的其中一個命令。 |
| 裝置範本 | 在 IoT Central 應用程式的裝置範本清單中選擇。 |
| 裝置命令要求承載 | 如果命令需要要求承載，請在這裡新增。  |

> [!NOTE]
> 在您選擇裝置範本之前，無法選擇裝置元件。

### <a name="get-a-device-by-id"></a>依識別碼取得裝置

使用此動作來擷取裝置的詳細資料。

| 欄位 | 描述 |
| ----- | ----------- |
| Application | 從您的 IoT Central 應用程式清單中選擇。 |
| 裝置 | 要刪除的裝置所用的唯一識別碼。 |

您可以在其他動作的動態運算式中使用傳回的詳細資料。 傳回的裝置詳細資料包括：**已核准**、**主體**、**裝置描述**、**裝置名稱**、**裝置範本**、**已佈建**和**模擬**。

### <a name="get-device-cloud-properties"></a>取得裝置的雲端屬性

使用此動作可取得特定裝置的雲端屬性值。

| 欄位 | 描述 |
| ----- | ----------- |
| Application | 從您的 IoT Central 應用程式清單中選擇。 |
| 裝置 | 要刪除的裝置所用的唯一識別碼。 |
| 裝置範本 | 在 IoT Central 應用程式的裝置範本清單中選擇。 |

您可以在其他動作的動態運算式中使用傳回的雲端屬性值。

### <a name="get-device-properties"></a>取得裝置屬性

使用此動作可取得特定裝置的屬性值。

| 欄位 | 描述 |
| ----- | ----------- |
| Application | 從您的 IoT Central 應用程式清單中選擇。 |
| 裝置 | 要刪除的裝置所用的唯一識別碼。 |
| 裝置範本 | 在 IoT Central 應用程式的裝置範本清單中選擇。 |

您可以在其他動作的動態運算式中使用傳回的屬性值。

### <a name="get-device-telemetry-value"></a>取得裝置遙測值

使用此動作可取得特定裝置的遙測值。

| 欄位 | 描述 |
| ----- | ----------- |
| Application | 從您的 IoT Central 應用程式清單中選擇。 |
| 裝置 | 要刪除的裝置所用的唯一識別碼。 |
| 裝置範本 | 在 IoT Central 應用程式的裝置範本清單中選擇。 |

您可以在其他動作的動態運算式中使用傳回的遙測值。

### <a name="update-device-cloud-properties"></a>更新裝置雲端屬性

使用此動作可更新特定裝置的雲端屬性值。

| 欄位 | 描述 |
| ----- | ----------- |
| Application | 從您的 IoT Central 應用程式清單中選擇。 |
| 裝置 | 要刪除的裝置所用的唯一識別碼。 |
| 裝置範本 | 在 IoT Central 應用程式的裝置範本清單中選擇。 |
| 雲端屬性 | 選擇裝置範本之後，會為範本中定義的每個雲端屬性新增欄位。 |

### <a name="update-device-properties"></a>更新裝置屬性

使用此動作可更新特定裝置的可寫入屬性值。

| 欄位 | 描述 |
| ----- | ----------- |
| Application | 從您的 IoT Central 應用程式清單中選擇。 |
| 裝置 | 要刪除的裝置所用的唯一識別碼。 |
| 裝置範本 | 在 IoT Central 應用程式的裝置範本清單中選擇。 |
| 可寫入屬性 | 選擇裝置範本之後，會為範本中定義的每個可寫入屬性新增欄位。 |

## <a name="next-steps"></a>後續步驟

由於您已了解如何在 Azure IoT Central 應用程式中建立進階的規則，因此您可以了解如何[分析 Azure IoT Central 應用程式中的裝置資料](howto-create-analytics.md)
