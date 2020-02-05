---
title: 在 Azure Logic Apps 中使用 IoT Central 連接器建置工作流程 | Microsoft Docs
description: 在 Azure Logic Apps 中使用 IoT Central 連接器觸發工作流程，並在該工作流程中建立、更新和刪除裝置。
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 3625d7374090bca73e2f054f7da4b58e951e7719
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990261"
---
# <a name="build-workflows-with-the-iot-central-connector-in-azure-logic-apps"></a>在 Azure Logic Apps 中使用 IoT Central 連接器建置工作流程

*本主題適用於建置員和系統管理員。*

使用 Azure Logic Apps 來建置能整合雲端服務和內部部署系統上應用程式和資料的自動化可調整工作流程。 Azure Logic Apps 在眾多 Azure 服務、Microsoft 服務及其他軟體即服務 (SaaS) 產品上皆具有許多連接器可供使用。 在 Azure Logic Apps 中使用 IoT Central 連接器時，您可以在規則於 IoT Central 中被觸發時觸發工作流程。 您也可以使用 IoT Central 連接器中的動作建立裝置、更新裝置的屬性和設定，或刪除裝置。

您可以在 Microsoft Flow 中使用 IoT Central 連接器。 Azure Logic Apps 和 Microsoft Flow 皆為設計工具優先的整合服務，但目標對象稍有不同。 Flow 能為所有辦公室員工提供建置自己所需之商務工作流程的能力。 Logic Apps 能為 IT 專業人員提供建置連線資料所需之整合功能的能力。 您可以在[這裡](https://docs.microsoft.com/azure/azure-functions/functions-compare-logic-apps-ms-flow-webjobs)比較 Flow 和 Logic Apps。 在[這裡](howto-add-microsoft-flow.md)了解如何在 Microsoft Flow 中使用 IoT Central 連接器建置工作流程。

## <a name="prerequisites"></a>必要條件

- 使用標準定價方案建立的應用程式
- 用來建立及管理邏輯應用程式的 Azure 帳戶和訂用帳戶

## <a name="trigger-a-workflow-when-a-rule-is-triggered"></a>在觸發規則時觸發工作流程

本節會說明如何在規則被觸發的情況下將訊息張貼到 Microsoft Teams。 您可以設定工作流程以使用其他連接器來執行各種作業，例如將事件傳送到事件中樞、建立新的 Azure DevOps 工作項目，或是在 SQL 伺服器中插入新資料列。

1. 首先，[在 IoT Central 中建立規則](howto-create-telemetry-rules.md)。 儲存規則條件後，請選取 [ **Azure Logic Apps** ] 磚做為新動作。 **在 Azure 入口網站中**選取 [建立]。 您會進入 Azure 入口網站以建立新的邏輯應用程式。 您可能需要登入您的 Azure 帳戶。

1. 輸入必要資訊以建立新的邏輯應用程式。 您可以選擇 Azure 訂用帳戶來佈建新的邏輯應用程式。 該訂用帳戶不必和您用來建立 IoT Central 應用程式的訂用帳戶相同。 選取 [建立]。

    ![在 Azure 入口網站中建立邏輯應用程式](./media/howto-build-azure-logic-apps/createinazureportal.png)

1. 成功建立邏輯應用程式之後，您會自動流覽至 Logic Apps 設計工具。 選取 [空白邏輯應用程式]。 

    ![建立空白邏輯應用程式](./media/howto-build-azure-logic-apps/blanklogicapp.png)

1. 在設計工具中搜尋 "iot central"，然後選擇 [當規則觸發時] 觸發程序。 使用您登入 IoT Central 應用程式的帳戶登入連接器。

    ![登入 IoT Central 連接器](./media/howto-build-azure-logic-apps/addtrigger.png)

1. 成功登入之後，畫面應該會顯示欄位。 從下拉式清單選取 [應用程式] 和 [規則]。

    ![挑選應用程式和規則](./media/howto-build-azure-logic-apps/pickappandrule.png)

1. 新增動作。 搜尋「張貼訊息 teams」，然後從 Microsoft Teams 連接器選擇 [張貼訊息]。 使用您用於 Microsoft Teams 的帳戶登入連接器。

1. 在該動作中，選擇 [小組] 和 [通道]。 將所需的訊息內容填入 [訊息] 欄位。 您可以納入 IoT Central 規則中的「動態內容」，將裝置名稱和時間戳記等重要資訊傳至您的通知。
    > [!NOTE]
    > 選取 [動態內容] 視窗中的 [**查看更多**文字]，以取得可觸發規則的測量和屬性值。

    ![動態窗格開啟時的邏輯應用程式編輯動作](./media/howto-build-azure-logic-apps/buildworkflow.png)

1. 當您完成編輯動作時，請選取 [**儲存**]。

1. 如果您回到 IoT Central 應用程式，您將會在 [動作] 區域下看到此規則具有 Azure Logic Apps 動作。

您一律可以使用 Azure 入口網站中 Logic Apps 的 IoT Central 連接器開始建立工作流程。 接著，您可以選擇要連接的 IoT Central 應用程式和規則，而且仍然以相同的方式運作。 您並不需要每次都從 IoT Central 規則頁面開始。

## <a name="create-update-and-delete-a-device-in-a-workflow"></a>在工作流程中建立、更新及刪除裝置

當您在邏輯應用程式中建置工作流程時，您可以使用 IoT Central 連接器新增動作。 可用的動作包括 [**建立裝置**]、[**更新裝置**] 和 [**刪除裝置**]。

> [!NOTE]
> 針對 [更新裝置] 和 [刪除裝置]，您將會需要想要更新或刪除之現有裝置的識別碼。 您可以在**裝置總管**中取得 IoT Central 裝置的識別碼

![IoT Central Device Explorer 裝置識別碼](./media/howto-build-azure-logic-apps/iotcdeviceid.png)

## <a name="next-steps"></a>後續步驟

既然您已瞭解如何使用 Microsoft Flow 來建立工作流程，建議的下一個步驟是[管理裝置](howto-manage-devices.md)。
