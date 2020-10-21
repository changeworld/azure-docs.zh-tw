---
title: 與 Logic Apps 整合
titleSuffix: Azure Digital Twins
description: 瞭解如何使用自訂連接器將 Logic Apps 連線到 Azure 數位 Twins
author: baanders
ms.author: baanders
ms.date: 9/11/2020
ms.topic: how-to
ms.service: digital-twins
ms.reviewer: baanders
ms.openlocfilehash: 292305c3f899ac4156fd84e8edcb0d6e9a3c0b34
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92280858"
---
# <a name="integrate-with-logic-apps-using-a-custom-connector"></a>使用自訂連接器與 Logic Apps 整合

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) 是一項雲端服務，可協助您將應用程式和服務之間的工作流程自動化。 藉由將 Logic Apps 連接到 Azure 數位 Twins Api，您可以建立 Azure 數位 Twins 和其資料這類自動化流程。

Azure 數位 Twins 目前沒有認證 (預先建立的) 連接器可供 Logic Apps。 相反地，目前使用 Logic Apps 搭配 Azure 數位 Twins 的程式是使用已修改為使用 Logic Apps 的[自訂 Azure 數位 Twins Swagger](/samples/azure-samples/digital-twins-custom-swaggers/azure-digital-twins-custom-swaggers/)來建立[**自訂 Logic Apps 連接器**](../logic-apps/custom-connector-overview.md)。

> [!NOTE]
> 上面連結的自訂 Swagger 範例中包含多個 Swagger 版本。 最新版本會在最新日期的子資料夾中找到，但仍會支援範例中包含的較舊版本。

在本文中，您將使用 [Azure 入口網站](https://portal.azure.com) 來 **建立自訂連接器** ，以用來將 Logic Apps 連線到 Azure 數位 Twins 實例。 然後，您將建立一個使用此連線的 **邏輯應用程式** 來進行範例案例，其中計時器觸發的事件將會自動更新 Azure 數位 Twins 實例中的對應項。 

## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂閱，請在開始前**建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)** 。
使用此帳戶登入 [Azure 入口網站](https://portal.azure.com) 。 

您也需要在必要條件設定中完成下列專案。 本節的其餘部分將逐步引導您完成下列步驟：
- 設定 Azure Digital Twins 執行個體
- 取得應用程式註冊用戶端密碼
- 新增數位對應項

### <a name="set-up-azure-digital-twins-instance"></a>設定 Azure Digital Twins 執行個體

若要將 Azure 數位 Twins 實例連線到本文中的 Logic Apps，您必須已設定 **Azure 數位 Twins 實例** 。 

首先， **設定 Azure 數位 Twins 實例** 和必要的驗證，以便能夠使用它。 若要這麼做，請依照指示操作：[*操作說明：設定執行個體和驗證*](how-to-set-up-instance-portal.md)。 您可以根據本身偏好的體驗，選擇參閱針對 [Azure 入口網站](how-to-set-up-instance-portal.md)、[CLI](how-to-set-up-instance-cli.md) 或[自動化 Cloud Shell 部署指令碼範例](how-to-set-up-instance-scripted.md)而提供的設定文章。 各種版本的指示也包含可驗證您已成功完成每個步驟，並已準備好繼續使用新執行個體的步驟。
* 設定您的 Azure 數位 Twins 實例之後，您將需要實例的 **_主機名稱_** ([在 Azure 入口網站) 中找到](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values) 。

若要驗證 ADT Explorer 應用程式，您也需要設定 **應用程式註冊**。 遵循 [*如何：建立應用程式註冊*](how-to-create-app-registration.md) 來設定此功能的指示。 
* 註冊應用程式之後，您將需要註冊的 **_應用程式 (用戶端) 識別碼_** 和 **_目錄 (租使用者) 識別碼_** ([在 Azure 入口網站) 中找到](how-to-create-app-registration.md#collect-client-id-and-tenant-id) 。

### <a name="get-app-registration-client-secret"></a>取得應用程式註冊用戶端密碼

您也需要建立 Azure AD 應用程式註冊的 **_用戶端密碼_** 。 若要這樣做，請流覽至 Azure 入口網站中的 [ [應用程式註冊](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) ] 頁面 (您可以使用此連結，或是在入口網站的搜尋列) 中尋找它。 從清單中選取您在上一節中建立的註冊，以便開啟其詳細資料。 

從註冊的功能表點擊 *憑證和密碼* ，然後選取 [ *+ 新增用戶端密碼*]。

:::image type="content" source="media/how-to-integrate-logic-apps/client-secret.png" alt-text="Azure AD 應用程式註冊的入口網站觀看。[資源] 功能表中的 [憑證和秘密] 周圍有一個醒目提示，並在 [新用戶端密碼] 周圍的頁面上醒目提示。":::

輸入您想要用於描述和到期的任何值，然後按 [ *新增*]。

:::image type="content" source="media/how-to-integrate-logic-apps/add-client-secret.png" alt-text="Azure AD 應用程式註冊的入口網站觀看。[資源] 功能表中的 [憑證和秘密] 周圍有一個醒目提示，並在 [新用戶端密碼] 周圍的頁面上醒目提示。":::

現在，請在 [ _憑證 & 秘密_ ] 頁面上確認用戶端密碼顯示為 [ _到期_ ] 和 [ _值_ ] 欄位。 記下其 _值_ 以供稍後使用 (您也可以使用複製圖示將它複製到剪貼簿) 

:::image type="content" source="media/how-to-integrate-logic-apps/client-secret-value.png" alt-text="Azure AD 應用程式註冊的入口網站觀看。[資源] 功能表中的 [憑證和秘密] 周圍有一個醒目提示，並在 [新用戶端密碼] 周圍的頁面上醒目提示。":::

### <a name="add-a-digital-twin"></a>新增數位對應項

本文使用 Logic Apps 來更新 Azure 數位 Twins 實例中的對應項。 若要繼續，您應該在實例中新增至少一個對應項。 

您可以使用 [DigitalTwins api](how-to-use-apis-sdks.md)、 [.Net (c # ) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)或 [Azure 數位 twins CLI](how-to-use-cli.md)來新增 twins。 如需有關如何使用這些方法建立 twins 的詳細步驟，請參閱 [*如何：管理數位 twins*](how-to-manage-twin.md)。

您必須在您所建立的實例中，對應項的對應項 **_識別碼_** 。

## <a name="create-custom-logic-apps-connector"></a>建立自訂 Logic Apps 連接器

在此步驟中，您將建立 Azure 數位 Twins Api 的 [自訂 Logic Apps 連接器](../logic-apps/custom-connector-overview.md) 。 這麼做之後，在下一節中建立邏輯應用程式時，您將能夠連結 Azure 數位 Twins。

流覽至 Azure 入口網站中的 [ [Logic Apps 自訂連接器](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Web%2FcustomApis) ] 頁面 (您可以使用此連結，或是在入口網站的搜尋列中搜尋) 。 按 *+ 新增*。

:::image type="content" source="media/how-to-integrate-logic-apps/logic-apps-custom-connector.png" alt-text="Azure AD 應用程式註冊的入口網站觀看。[資源] 功能表中的 [憑證和秘密] 周圍有一個醒目提示，並在 [新用戶端密碼] 周圍的頁面上醒目提示。":::

在接下來的 [ *建立 Logic Apps 自訂連接器* ] 頁面中，選取您的訂用帳戶和資源群組，以及新連接器的名稱和部署位置。 點擊 *檢查 + 建立*。 

:::image type="content" source="media/how-to-integrate-logic-apps/create-logic-apps-custom-connector.png" alt-text="Azure AD 應用程式註冊的入口網站觀看。[資源] 功能表中的 [憑證和秘密] 周圍有一個醒目提示，並在 [新用戶端密碼] 周圍的頁面上醒目提示。":::

這會將您帶到 [ *審核 + 建立* ] 索引標籤，您可以在底部點擊 [ *建立* ] 以建立您的資源。

:::image type="content" source="media/how-to-integrate-logic-apps/review-logic-apps-custom-connector.png" alt-text="Azure AD 應用程式註冊的入口網站觀看。[資源] 功能表中的 [憑證和秘密] 周圍有一個醒目提示，並在 [新用戶端密碼] 周圍的頁面上醒目提示。":::

您將會進入連接器的 [部署] 頁面。 完成部署後，請按 [ *移至資源* ] 按鈕，以在入口網站中查看連接器的詳細資料。

### <a name="configure-connector-for-azure-digital-twins"></a>設定連接器以進行 Azure 數位 Twins

接下來，您將設定您所建立的連接器，以連線到 Azure 數位 Twins。

首先，下載已修改為使用 Logic Apps 的自訂 Azure 數位 Twins Swagger。 藉由按下 [*下載 ZIP* ] 按鈕，從此[**連結**](/samples/azure-samples/digital-twins-custom-swaggers/azure-digital-twins-custom-swaggers/)下載**Azure 數位 Twins 自訂 swagger (Logic Apps connector) **範例。 流覽至下載的 *Azure_Digital_Twins_custom_Swaggers__Logic_Apps_connector_.zip* 資料夾並解壓縮。 

本教學課程的自訂 Swagger 位於 _* * Azure_Digital_Twins_custom_Swaggers__Logic_Apps_connector_\LogicApps **_ 資料夾中。此資料夾包含名為 [ *穩定* ] 和 [ *預覽*] 的子資料夾，其中包含依日期組織的不同版本的 Swagger。具有最新日期的資料夾會包含 Swagger 的最新複本。無論您選取哪一個版本，Swagger 檔案都會** 在 * * _ 上命名為 _digitaltwins.js。

> [!NOTE]
> 除非您使用的是預覽功能，否則通常建議使用最新 *穩定* 版本的 Swagger。 不過，仍支援較早版本和預覽版本的 Swagger。 

接下來，移至 [ [Azure 入口網站](https://portal.azure.com) 中您連接器的 [總覽] 頁面，然後按 [ *編輯*]。

:::image type="content" source="media/how-to-integrate-logic-apps/edit-connector.png" alt-text="Azure AD 應用程式註冊的入口網站觀看。[資源] 功能表中的 [憑證和秘密] 周圍有一個醒目提示，並在 [新用戶端密碼] 周圍的頁面上醒目提示。":::

在接下來的 [ *編輯 Logic Apps 自訂連接器* ] 頁面中，設定下列資訊：
* **自訂連接器**
    - API 端點： REST (保留預設值) 
    - 匯入模式： OpenAPI 檔案 (保留預設) 
    - 檔案：這會是您稍早下載的自訂 Swagger 檔案。 點擊 [匯 *入*]，在您的電腦上找出 (*Azure_Digital_Twins_custom_Swaggers__Logic_Apps_connector_ \LogicApps \...\digitaltwins.js) 上* 的檔案，然後按 [ *開啟*]。
* **一般資訊**
    - 圖示：上傳您喜歡的圖示
    - 圖示背景色彩：輸入格式為 ' #xxxxxx ' 的十六進位代碼，以供您的色彩。
    - 描述：填寫任何您想要的值。
    - 配置： HTTPS (保留預設值) 
    - Host： Azure 數位 Twins 實例的 *主機名稱* 。
    - 基底 URL：/ (保留預設值) 

然後，按下視窗底部的 [ *安全性* ] 按鈕，以繼續進行下一個設定步驟。

:::image type="content" source="media/how-to-integrate-logic-apps/configure-next.png" alt-text="Azure AD 應用程式註冊的入口網站觀看。[資源] 功能表中的 [憑證和秘密] 周圍有一個醒目提示，並在 [新用戶端密碼] 周圍的頁面上醒目提示。":::

在 [安全性] 步驟中，按 [ *編輯* ] 並設定這項資訊：
* **驗證類型**： OAuth 2。0
* **OAuth 2.0**：
    - 身分識別提供者： Azure Active Directory
    - 用戶端識別碼： Azure AD 應用程式註冊的*應用程式 (用戶端) 識別碼*
    - 用戶端密碼：您在 Azure AD 應用程式註冊的[*必要條件*](#prerequisites)中所建立的*用戶端密碼*
    - 登入 URL： https://login.windows.net (保留預設值) 
    - 租使用者識別碼： Azure AD 應用程式註冊的* (租使用者) 識別碼目錄*
    - 資源 URL： 0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    - 範圍： AccessAsUser。全部
    - 重新導向 URL： (保留預設值) 

請注意，[重新導向 URL] 欄位會顯示 [ *儲存自訂連接器] 以產生重新導向 URL*。 現在請在窗格頂端點擊 *Update 連接器* ，確認您的連接器設定。

:::image type="content" source="media/how-to-integrate-logic-apps/update-connector.png" alt-text="Azure AD 應用程式註冊的入口網站觀看。[資源] 功能表中的 [憑證和秘密] 周圍有一個醒目提示，並在 [新用戶端密碼] 周圍的頁面上醒目提示。":::

<!-- Success message? didn't see one -->

返回 [重新導向 URL] 欄位，然後複製已產生的值。 您將會在下一個步驟中使用它。

:::image type="content" source="media/how-to-integrate-logic-apps/copy-redirect-url.png" alt-text="Azure AD 應用程式註冊的入口網站觀看。[資源] 功能表中的 [憑證和秘密] 周圍有一個醒目提示，並在 [新用戶端密碼] 周圍的頁面上醒目提示。":::

這是建立連接器所需的所有資訊 (不需要繼續) 定義步驟的安全性。 您可以關閉 [ *編輯 Logic Apps 自訂連接器* ] 窗格。

>[!NOTE]
>回到您的連接器總覽頁面，您最初按下 [ *編輯*] 時，請注意再次按下 [ *編輯* ]，將會重新開機整個輸入設定選項的流程。 它不會在您上一次執行時填入您的值，因此，如果您想要使用任何變更的值來儲存更新的設定，您也必須重新輸入所有其他值，以避免預設值覆寫這些值。

### <a name="grant-connector-permissions-in-the-azure-ad-app"></a>在 Azure AD 應用程式中授與連接器許可權

接下來，使用您在上一個步驟中複製的自訂連接器的重新 *導向 URL* 值，在您的 Azure AD 應用程式註冊中授與連接器許可權。

流覽至 Azure 入口網站中的 [ [應用程式註冊](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) ] 頁面，然後從清單中選取您的註冊。 

在註冊功能表的 [ *驗證* ] 底下，新增 URI。

:::image type="content" source="media/how-to-integrate-logic-apps/add-uri.png" alt-text="Azure AD 應用程式註冊的入口網站觀看。[資源] 功能表中的 [憑證和秘密] 周圍有一個醒目提示，並在 [新用戶端密碼] 周圍的頁面上醒目提示。"::: 

在新欄位中輸入自訂連接器的重新 *導向 URL* ，然後按下 [ *儲存* ] 圖示。

:::image type="content" source="media/how-to-integrate-logic-apps/save-uri.png" alt-text="Azure AD 應用程式註冊的入口網站觀看。[資源] 功能表中的 [憑證和秘密] 周圍有一個醒目提示，並在 [新用戶端密碼] 周圍的頁面上醒目提示。":::

您現在已完成設定可存取 Azure 數位 Twins Api 的自訂連接器。 

## <a name="create-logic-app"></a>建立邏輯應用程式

接下來，您將建立邏輯應用程式，以使用新的連接器來自動化 Azure 數位 Twins 更新。

在 [Azure 入口網站](https://portal.azure.com)中，于入口網站的搜尋列中搜尋 *邏輯應用程式* 。 選取它應該會帶您前往 [ *邏輯應用程式* ] 頁面。 點擊 [ *建立邏輯應用程式* ] 按鈕，以建立新的邏輯應用程式。

:::image type="content" source="media/how-to-integrate-logic-apps/create-logic-app.png" alt-text="Azure AD 應用程式註冊的入口網站觀看。[資源] 功能表中的 [憑證和秘密] 周圍有一個醒目提示，並在 [新用戶端密碼] 周圍的頁面上醒目提示。":::

在接下來的 [ *邏輯應用程式* ] 頁面中，輸入您的訂用帳戶和資源群組。 此外，請選擇邏輯應用程式的名稱，然後選取部署位置。

點擊 [ _審核 + 建立_ ] 按鈕。

這會將您帶到 [審核] 和 [ *建立* ] 索引標籤，您可以在此查看詳細資料，並在底部點擊 [ *建立* ] 以建立您的資源。

您將會進入邏輯應用程式的部署頁面。 完成部署後，請按 [ *移至資源* ] 按鈕繼續進行 *Logic Apps 設計*工具，您將在其中填入工作流程的邏輯。

### <a name="design-workflow"></a>設計工作流程

在 [ *Logic Apps] 設計*工具的 [ *開始使用一般觸發*程式] 下，選取 _**[週期**_]。

:::image type="content" source="media/how-to-integrate-logic-apps/logic-apps-designer-recurrence.png" alt-text="Azure AD 應用程式註冊的入口網站觀看。[資源] 功能表中的 [憑證和秘密] 周圍有一個醒目提示，並在 [新用戶端密碼] 周圍的頁面上醒目提示。":::

在接下來的 *Logic Apps 設計* 工具] 頁面中，將 **週期** 頻率變更為 *第二個*，讓事件每3秒觸發一次。 這可讓您在稍後輕鬆查看結果，而不需要等待很長的時間。

按 *+ 新增步驟*。

這會開啟 *[選擇動作* ] 方塊。 切換至 [ *自訂* ] 索引標籤。您應該會在頂端的方塊中看到您先前的自訂連接器。

:::image type="content" source="media/how-to-integrate-logic-apps/custom-action.png" alt-text="Azure AD 應用程式註冊的入口網站觀看。[資源] 功能表中的 [憑證和秘密] 周圍有一個醒目提示，並在 [新用戶端密碼] 周圍的頁面上醒目提示。":::

選取它以顯示該連接器所包含的 Api 清單。 使用搜尋列或在清單中滾動，以選取 **DigitalTwins_Add**。  (這是本文中使用的 API，但您也可以選取任何其他 API，作為 Logic Apps 連線) 的有效選擇。

系統可能會要求您使用您的 Azure 認證登入，以連線到連接器。 如果您取得 [ *要求的許可權* ] 對話方塊，請依照提示來授與您應用程式的同意並接受。

在 [新增 *DigitalTwinsAdd* ] 方塊中，填寫欄位，如下所示：
* _識別碼_：在您想要邏輯應用程式更新的實例中，填入數位對應項的對應項 *識別碼* 。
* 對應_項：此_欄位可讓您輸入所選 API 要求所需的主體。 針對 *DigitalTwinsUpdate*，此主體是 JSON 修補程式碼的形式。 如需有關結構化 JSON 修補程式以更新對應項的詳細資訊，請參閱 how *to：管理數位 twins*的[更新數位](how-to-manage-twin.md#update-a-digital-twin)對應項一節。
* _api 版本_：最新的 api 版本。 目前，此值為 *2020-10-31*。

點擊 Logic Apps 設計工具中的 [ *儲存* ]。

您可以在同一個視窗中選取 [ _+ 新增步驟_ ] 來選擇其他作業。

:::image type="content" source="media/how-to-integrate-logic-apps/save-logic-app.png" alt-text="Azure AD 應用程式註冊的入口網站觀看。[資源] 功能表中的 [憑證和秘密] 周圍有一個醒目提示，並在 [新用戶端密碼] 周圍的頁面上醒目提示。":::

## <a name="query-twin-to-see-the-update"></a>查詢對應項以查看更新

既然已建立邏輯應用程式，您在 Logic Apps 設計工具中定義的對應項更新事件應該會在每隔三秒的週期發生。 這表示，在三秒鐘之後，您應該能夠查詢對應項，並看到新的修補值反映出來。

您可以透過您選擇的方法來查詢對應項 (例如 [自訂用戶端應用程式](tutorial-command-line-app.md)、 [Azure 數位 Twins Explorer 範例應用程式](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/)、 [Sdk 和 api](how-to-use-apis-sdks.md)，或 [CLI](how-to-use-cli.md)) 。 

如需查詢 Azure 數位 Twins 實例的詳細資訊，請參閱 how [*to：查詢對應項圖形*](how-to-query-graph.md)。

## <a name="next-steps"></a>後續步驟

在本文中，您已建立邏輯應用程式，以使用您提供的修補程式定期更新 Azure 數位 Twins 實例中的對應項。 您可以嘗試在自訂連接器中選取其他 Api，以針對實例上的各種動作建立 Logic Apps。

若要深入瞭解可用的 Api 作業以及它們所需的詳細資料，請參閱作法 [*：使用 Azure 數位 Twins api 和 sdk*](how-to-use-apis-sdks.md)。