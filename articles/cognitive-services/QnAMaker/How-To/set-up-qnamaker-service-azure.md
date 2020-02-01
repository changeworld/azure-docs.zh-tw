---
title: 設定 QnA Maker 服務-QnA Maker
titleSuffix: Azure Cognitive Services
description: 您必須先在 Azure 中設定 QnA Maker 服務，才能建立任何 QnA Maker 知識庫。 任何具備授權而可在訂用帳戶中建立新資源的人，皆可建立 QnA Maker 服務。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/28/2020
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 00b8e6d44ed8449aa4ddf8716039c8c85c558b8f
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901731"
---
# <a name="manage-qna-maker-resources"></a>管理 QnA Maker 資源

您必須先在 Azure 中設定 QnA Maker 服務，才能建立任何 QnA Maker 知識庫。 任何具備授權而可在訂用帳戶中建立新資源的人，皆可建立 QnA Maker 服務。

## <a name="types-of-keys-in-qna-maker"></a>QnA Maker 中的金鑰類型

您的 QnA Maker 服務會處理兩種金鑰：**訂**用帳戶金鑰和**端點金鑰**。

![金鑰管理](../media/qnamaker-how-to-key-management/key-management.png)

|名稱|位置|目的|
|--|--|--|
|訂用帳戶金鑰|[Azure 入口網站](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)|這些金鑰可用來存取[QnA Maker 管理服務 api](https://go.microsoft.com/fwlink/?linkid=2092179)。 這些 Api 可讓您編輯知識庫中的問題和答案，併發布您的知識庫。 當您建立新的 QnA Maker 服務時，會建立這些金鑰。<br><br>在 [**金鑰**] 頁面上的**認知服務**資源上尋找這些金鑰。|
|端點金鑰|[QnA Maker 入口網站](https://www.qnamaker.ai)|這些金鑰可用來存取已發佈的知識庫端點，以取得使用者問題的回應。 您通常會在聊天機器人或連接到 QnA Maker 服務的用戶端應用程式程式碼中使用此端點。 當您發佈 QnA Maker 知識庫時，會建立這些金鑰。<br><br>在 [**服務設定**] 頁面中尋找這些金鑰。 從下拉式選單上頁面右上方的使用者功能表中，尋找此頁面。|

## <a name="create-a-new-qna-maker-service"></a>建立新的 QnA Maker 服務

此程式會建立管理知識庫內容所需的 Azure 資源。 完成這些步驟之後，您會在 Azure 入口網站中資源的 **金鑰** 頁面上找到_訂_用帳戶金鑰。

1. 登入 Azure 入口網站並[建立 QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)資源。

1. 閱讀條款及條件之後，請選取 [**建立**]：

    ![建立新的 QnA Maker 服務](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. 在  **QnA Maker**中，選取適當的層級和區域：

    ![建立新的 QnA Maker 服務 - 定價層和區域](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * 在 [**名稱**] 欄位中，輸入唯一的名稱來識別此 QnA Maker 服務。 這個名稱也會識別您的知識庫將與之相關聯的 QnA Maker 端點。
    * 選擇將部署 QnA Maker 資源的**訂**用帳戶。
    * 選取 QnA Maker 管理服務（入口網站和管理 Api）的**定價層**。 查看[更多 SKU 定價的詳細資料](https://aka.ms/qnamaker-pricing)。
    * 建立新的**資源群組**（建議），或使用現有的資源群組來部署此 QnA Maker 資源。 QnA Maker 會建立數個 Azure 資源。 當您建立資源群組來保存這些資源時，您可以使用資源組名輕鬆地尋找、管理和刪除這些資源。
    * 選取**資源群組位置**。
    * 選擇 Azure 認知搜尋服務的 [**搜尋] 定價層**。 如果 [免費層] 選項無法使用（顯示為灰色），表示您已透過訂用帳戶部署免費服務。 在此情況下，您必須從基本層開始。 請參閱[Azure 認知搜尋定價詳細資料](https://azure.microsoft.com/pricing/details/search/)。
    * 選擇您想要部署 Azure 認知搜尋索引的**搜尋位置**。 必須儲存客戶資料的限制，將有助於判斷您選擇的 Azure 認知搜尋位置。
    * 在 [**應用程式名稱**] 欄位中，輸入 Azure App Service 實例的名稱。
    * 根據預設，App Service 會預設為標準（S1）層。 您可以在建立之後變更方案。 深入瞭解[App Service 定價](https://azure.microsoft.com/pricing/details/app-service/)。
    * 選擇將部署 App Service 的**網站位置**。

        > [!NOTE]
        > **搜尋位置**可能與**網站位置**不同。

    * 選擇您是否要啟用**Application Insights**。 如果啟用 **Application Insights**，QnA Maker 會收集流量、交談記錄和錯誤的遙測資料。
    * 選擇將部署 Application Insights 資源的**App insights 位置**。
    * 如需節省成本的方法，您可以[共用](#share-existing-services-with-qna-maker)一些 (不是全部) 為 QnA Maker 建立的 Azure 資源。

1. 驗證所有欄位之後，請選取 [**建立**]。 此程式可能需要幾分鐘的時間才能完成。

1. 部署完成之後，您將會看到在您的訂用帳戶中建立的下列資源：

   ![建立新 QnA Maker 服務的資源](../media/qnamaker-how-to-setup-service/resources-created.png)

    具有_認知服務_類型的資源具有您的_訂_用帳戶金鑰。

## <a name="find-subscription-keys-in-the-azure-portal"></a>尋找 Azure 入口網站中的訂用帳戶金鑰

您可以從建立 QnA Maker 資源的 Azure 入口網站中，查看和重設您的訂用帳戶金鑰。

1. 移至 Azure 入口網站中的 QnA Maker 資源，然後選取具有_認知服務_類型的資源：

    ![QnA Maker 資源清單](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. 移至 [**金鑰**]：

    ![訂用帳戶金鑰](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="find-endpoint-keys-in-the-qna-maker-portal"></a>在 QnA Maker 入口網站中尋找端點金鑰

端點與資源位於相同的區域，因為端點金鑰是用來呼叫知識庫。

您可以從 [QnA Maker 入口網站](https://qnamaker.ai)來管理端點金鑰。

1. 登入[QnA Maker 入口網站](https://qnamaker.ai)，移至您的設定檔，然後選取 [**服務設定**]：

    ![端點金鑰](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. 查看或重設您的金鑰：

    > [!div class="mx-imgBorder"]
    > [![在服務設定中顯示、複製或設定端點金鑰](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)](../media/qnamaker-how-to-key-management/Endpoint-keys1.png#lightbox)

    >[!NOTE]
    >如果您認為金鑰已遭盜用，請重新整理。 為此，您可能需要對用戶端應用程式或 Bot 程式碼進行對應的變更。

## <a name="share-existing-services-with-qna-maker"></a>與 QnA Maker 共用現有的服務

QnA Maker 會建立數個 Azure 資源。 若要減少管理和受益于成本共用，請使用下表來瞭解您可以和無法共用的內容：

|服務|共用|原因|
|--|--|--|
|辨識服務|X|設計不可行|
|App Service 方案|✔|已修正配置給 App Service 方案的磁碟空間。 如果其他共用相同 App Service 方案的應用程式使用大量磁碟空間，QnAMaker App Service 實例就會發生問題。|
|App Service|X|設計不可行|
|Application Insights|✔|可以共用|
|搜尋服務|✔|1. `testkb` 是 QnAMaker 服務的保留名稱;不能供其他人使用。<br>2. 依名稱 `synonym-map` 的同義字對應會保留給 QnAMaker 服務。<br>3. 已發佈的知識庫數目會受到搜尋服務層級的限制。 如果有可用的索引，其他服務就可以使用它們。|

深入瞭解[App service](../../../app-service/index.yml)和[Search 服務](../../../search/index.yml)。

## <a name="using-a-single-search-service"></a>使用單一搜尋服務

如果您透過入口網站建立 QnA 服務及其相依性（例如搜尋），系統就會為您建立搜尋服務，並連結至 QnA Maker 服務。 建立這些資源之後，您可以更新 App Service 設定，以使用先前現有的搜尋服務，並移除您剛建立的搜尋服務。

如果您透過 Azure Resource Manager 範本建立 QnA 服務，您可以建立所有資源，並控制 App Service 建立，以使用現有的搜尋服務。


## <a name="configure-qna-maker-to-use-different-cognitive-search-resource"></a>設定 QnA Maker 以使用不同的認知搜尋資源

如果您透過入口網站建立 QnA 服務及其相依性（例如搜尋），系統就會為您建立搜尋服務，並連結至 QnA Maker 服務。 建立這些資源之後，您可以更新 App Service 設定，以使用先前現有的搜尋服務，並移除您剛建立的搜尋服務。

QnA Maker 的**App Service**資源會使用認知搜尋資源。 為了變更 QnA Maker 所使用的認知搜尋資源，您必須變更 Azure 入口網站中的設定。

1. 取得您想要 QnA Maker 使用之認知搜尋資源的系統**管理金鑰**和**名稱**。

1. 登入[Azure 入口網站](https://portal.azure.com)並尋找與您的 QnA Maker 資源相關聯的**App Service** 。 這兩個都具有相同的名稱。

1. 依序選取 [設定] 和 [**設定** **]。** 這會顯示 QnA Maker App Service 的所有現有設定。

    > [!div class="mx-imgBorder"]
    > [![顯示 App Service 設定的 Azure 入口網站螢幕擷取畫面](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-app-service-configuration.png)](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-app-service-configuration.png#lightbox)

1. 變更下列索引鍵的值：

    * **AzureSearchAdminKey**
    * **AzureSearchName**

1. 若要使用新的設定，您必須重新開機 App service。 選取 **[總覽**]，然後選取 [**重新開機**]。

    > [!div class="mx-imgBorder"]
    > [在設定變更之後，Azure 入口網站重新開機 App Service 的 ![螢幕擷取畫面](../media/qnamaker-how-to-upgrade-qnamaker/screenshot-azure-portal-restart-app-service.png)](../media/qnamaker-how-to-upgrade-qnamaker/screenshot-azure-portal-restart-app-service.png)

如果您透過 Azure Resource Manager 範本建立 QnA 服務，您可以建立所有資源，並控制 App Service 建立，以使用現有的搜尋服務。

## <a name="upgrade-qna-maker"></a>升級 QnA Maker

|升級|原因|
|--|--|
|[升級](#upgrade-qna-maker-sku)QnA Maker 管理 SKU|您想要在知識庫中有更多問題和答案。|
|[升級](#upgrade-app-service)App Service SKU|您的知識庫需要為來自用戶端應用程式的更多要求提供服務，例如聊天機器人。|
|[升級](#upgrade-the-azure-cognitive-search-service)Azure 認知搜尋服務|您打算有許多知識庫。|


### <a name="upgrade-qna-maker-sku"></a>升級 QnA Maker SKU

當您想要在您的知識庫中有更多問題和答案，而不是您目前的層級，請升級您的 QnA Maker 服務定價層。

若要升級 QnA Maker 管理 SKU：

1. 在 Azure 入口網站中移至 QnA Maker 資源，並選取 [定價層]。

    ![QnA Maker 資源](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

1. 選擇適當的 SKU，並按下 [選取]。

    ![QnA Maker 價格](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

### <a name="upgrade-app-service"></a>升級 App Service

 當您的知識庫需要服務來自用戶端應用程式的更多要求時，請升級您的 App Service 定價層。

您可以 App Service[擴充](https://docs.microsoft.com/azure/app-service/manage-scale-up)或相應放大。

移至 Azure 入口網站中的 [App Service] 資源，然後視需要選取 [相應**增加**] 或 [**相應**放大] 選項。

![QnA Maker App Service 規模](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

### <a name="upgrade-the-azure-cognitive-search-service"></a>升級 Azure 認知搜尋服務

如果您打算有許多知識庫，請升級您的 Azure 認知搜尋服務定價層。

目前，您無法執行 Azure 搜尋服務 SKU 的就地升級。 不過，您可以使用所需的 SKU 建立新的 Azure 搜尋服務資源，並將資料還原到新的資源，然後再將新的資源連結至 QnA Maker 堆疊。 若要這樣做，請遵循下列步驟：

1. 在 Azure 入口網站中建立新的 Azure 搜尋服務資源，然後選取所需的 SKU。

    ![QnA Maker Azure 搜尋服務資源](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. 從原始的 Azure 搜尋服務資源將索引還原到新的 Azure 搜尋服務資源。 請參閱[備份還原範例程式碼](https://github.com/pchoudhari/QnAMakerBackupRestore)。

1. 還原資料之後，請移至新的 Azure 搜尋服務資源，並選取 [**金鑰**]，然後記下**名稱**和系統**管理金鑰**：

    ![QnA Maker Azure 搜尋服務索引鍵](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

1. 若要將新的 Azure 搜尋服務資源連結至 QnA Maker 堆疊，請移至 QnA Maker App Service 實例。

    ![QnA Maker App Service 實例](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

1. 選取 [**應用程式設定**]，並修改步驟3中 [ **AzureSearchName** ] 和 [ **AzureSearchAdminKey** ] 欄位的設定。

    ![QnA Maker App Service 設定](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

1. 重新開機 App Service 實例。

    ![重新開機 QnA Maker App Service 實例](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="get-the-latest-runtime-updates"></a>取得最新的執行時間更新

QnAMaker 執行時間是您在 Azure 入口網站中[建立 QnAMaker 服務](./set-up-qnamaker-service-azure.md)時所部署 Azure App Service 實例的一部分。 執行階段會定期進行更新。 2019年4月網站延伸模組版本（第5版 +）之後，QnA Maker App Service 實例處於自動更新模式。 此更新的設計目的是要在升級期間處理零停機時間。

您可以在 https://www.qnamaker.ai/UserSettings 檢查目前的版本。 如果您的版本早于版本5.x，則必須重新開機 App Service，才能套用最新的更新：

1. 在[Azure 入口網站](https://portal.azure.com)中，移至您的 QnAMaker 服務（資源群組）。

    ![QnAMaker Azure 資源群組](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. 選取 [App Service] 實例，然後開啟 [**總覽**] 區段。

    ![QnAMaker App Service 實例](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. 重新開機 App Service。 更新程式應該會在幾秒內完成。 在此重新開機期間，使用者將無法使用此 QnAMaker 服務的任何相依應用程式或 bot。

    ![重新開機 QnAMaker App Service 實例](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="management-service-region"></a>管理服務區域

QnA Maker 的管理服務僅適用于 QnA Maker 入口網站，以及用於初始資料處理。 此服務僅適用于美國西部區域。 此美國西部服務中不會儲存任何客戶資料。

## <a name="next-steps"></a>後續步驟

深入瞭解[App service](../../../app-service/index.yml)和[Search 服務](../../../search/index.yml)。

> [!div class="nextstepaction"]
> [建立和發佈知識庫](../Quickstarts/create-publish-knowledge-base.md)
