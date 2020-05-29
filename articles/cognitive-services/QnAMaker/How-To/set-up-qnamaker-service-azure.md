---
title: 設定 QnA Maker 服務-QnA Maker
description: 您必須先在 Azure 中設定 QnA Maker 服務，才能建立任何 QnA Maker 知識庫。 任何具備授權而可在訂用帳戶中建立新資源的人，皆可建立 QnA Maker 服務。
ms.topic: conceptual
ms.date: 05/28/2020
ms.openlocfilehash: 521d0388e4ee739b1ac840e482174ac466781f5f
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/29/2020
ms.locfileid: "84171169"
---
# <a name="manage-qna-maker-resources"></a>管理 QnA Maker 資源

您必須先在 Azure 中設定 QnA Maker 服務，才能建立任何 QnA Maker 知識庫。 任何具備授權而可在訂用帳戶中建立新資源的人，皆可建立 QnA Maker 服務。

在建立資源之前，瞭解下列概念會很有説明：

* [QnA Maker 資源](../Concepts/azure-resources.md)
* [撰寫和發佈金鑰](../Concepts/azure-resources.md#keys-in-qna-maker)

## <a name="create-a-new-qna-maker-service"></a>建立新的 QnA Maker 服務

此程式會建立管理知識庫內容所需的 Azure 資源。 完成這些步驟之後，您會在 [Azure 入口網站中資源的 [**金鑰**] 頁面上找到_訂_用帳戶金鑰。

1. 登入 Azure 入口網站並[建立 QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)資源。

1. 閱讀條款及條件之後，請選取 [**建立**]：

    ![建立新的 QnA Maker 服務](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. 在 [ **QnA Maker**中，選取適當的層級和區域：

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
    * 如需節省成本的方法，您可以[共用](#configure-qna-maker-to-use-different-cognitive-search-resource)一些 (不是全部) 為 QnA Maker 建立的 Azure 資源。

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
    > ![端點金鑰管理員](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >如果您認為金鑰已遭盜用，請重新整理。 為此，您可能需要對用戶端應用程式或 Bot 程式碼進行對應的變更。

## <a name="upgrade-qna-maker-sku"></a>升級 QnA Maker SKU

當您想要在您的知識庫中有更多問題和答案，而不是您目前的層級，請升級您的 QnA Maker 服務定價層。

若要升級 QnA Maker 管理 SKU：

1. 在 Azure 入口網站中移至 QnA Maker 資源，並選取 [定價層]****。

    ![QnA Maker 資源](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

1. 選擇適當的 SKU，並按下 [選取]****。

    ![QnA Maker 價格](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

## <a name="upgrade-app-service"></a>升級 App Service

 當您的知識庫需要服務來自用戶端應用程式的更多要求時，請升級您的 App Service 定價層。

您可以 App Service[擴充](https://docs.microsoft.com/azure/app-service/manage-scale-up)或相應放大。

移至 Azure 入口網站中的 [App Service] 資源，然後視需要選取 [相應**增加**] 或 [**相應**放大] 選項。

![QnA Maker App Service 規模](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

## <a name="upgrade-the-azure-cognitive-search-service"></a>升級 Azure 認知搜尋服務

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

您可以在上檢查目前的版本 https://www.qnamaker.ai/UserSettings 。 如果您的版本早于版本5.x，則必須重新開機 App Service，才能套用最新的更新：

1. 在[Azure 入口網站](https://portal.azure.com)中，移至您的 QnAMaker 服務（資源群組）。

    > [!div class="mx-imgBorder"]
    > ![QnAMaker Azure 資源群組](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. 選取 [App Service] 實例，然後開啟 [**總覽**] 區段。

    > [!div class="mx-imgBorder"]
    > ![QnAMaker App Service 實例](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)


1. 重新開機 App Service。 更新程式應該會在幾秒內完成。 在此重新開機期間，使用者將無法使用此 QnAMaker 服務的任何相依應用程式或 bot。

    ![重新開機 QnAMaker App Service 實例](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="configure-qna-maker-to-use-different-cognitive-search-resource"></a>設定 QnA Maker 以使用不同的認知搜尋資源

如果您透過入口網站建立 QnA 服務及其相依性（例如搜尋），系統就會為您建立搜尋服務，並連結至 QnA Maker 服務。 建立這些資源之後，您可以更新 App Service 設定，以使用先前現有的搜尋服務，並移除您剛建立的搜尋服務。

QnA Maker 的**App Service**資源會使用認知搜尋資源。 為了變更 QnA Maker 所使用的認知搜尋資源，您必須變更 Azure 入口網站中的設定。

1. 取得您想要 QnA Maker 使用之認知搜尋資源的系統**管理金鑰**和**名稱**。

1. 登入[Azure 入口網站](https://portal.azure.com)並尋找與您的 QnA Maker 資源相關聯的**App Service** 。 這兩個都具有相同的名稱。

1. 依序選取 [設定] 和 [**設定** **]。** 這會顯示 QnA Maker App Service 的所有現有設定。

    > [!div class="mx-imgBorder"]
    > ![顯示 App Service configuration 設定之 Azure 入口網站的螢幕擷取畫面](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-app-service-configuration.png)

1. 變更下列索引鍵的值：

    * **AzureSearchAdminKey**
    * **AzureSearchName**

1. 若要使用新的設定，您必須重新開機 App service。 選取 **[總覽**]，然後選取 [**重新開機**]。

    > [!div class="mx-imgBorder"]
    > ![在設定變更之後，Azure 入口網站重新開機 App Service 的螢幕擷取畫面](../media/qnamaker-how-to-upgrade-qnamaker/screenshot-azure-portal-restart-app-service.png)

如果您透過 Azure Resource Manager 範本建立 QnA 服務，您可以建立所有資源，並控制 App Service 建立，以使用現有的搜尋服務。

深入瞭解如何設定 App Service[應用程式設定](../../../app-service/configure-common.md#configure-app-settings)。

## <a name="configure-app-service-idle-setting-to-avoid-timeout"></a>設定 App service 閒置設定以避免超時

應用程式服務（為已發佈的知識庫提供 QnA Maker 的預測執行時間）具有閒置的 timeout 設定，其預設會在服務閒置時自動超時。 就 QnA Maker 而言，這表示您的預測執行時間 generateAnswer API 偶爾會在沒有流量的期間結束。

為了讓預測端點應用程式即使在沒有流量的情況下仍載入，請將閒置設定為 [永遠開啟]。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 搜尋並選取您的 QnA Maker 資源的應用程式服務。 它會與 QnA Maker 資源的名稱相同，但它會有不同**類型**的 App Service。
1. 尋找 [設定] **，然後**選取 [**設定**]。
1. 在 [設定] 窗格中，選取 **[一般設定**]，然後尋找 [ **alwayson**]，然後選取 [**開啟**] 做為值。

    > [!div class="mx-imgBorder"]
    > ![在 [設定] 窗格中，選取 [一般設定]，然後尋找 * * [永遠開啟]，並選取 * * On * * 作為值。](../media/qnamaker-how-to-upgrade-qnamaker/configure-app-service-idle-timeout.png)

1. 選取 [儲存]**** 以儲存組態。
1. 系統會詢問您是否要重新開機應用程式，以使用新的設定。 選取 [繼續]  。

深入瞭解如何設定 App Service[一般設定](../../../app-service/configure-common.md#configure-general-settings)。

## <a name="business-continuity-with-traffic-manager"></a>流量管理員的業務持續性

商務持續性方案的主要目標是建立彈性的知識庫端點，藉以確保聊天機器人或應用程式使用知識庫時不會停機。

> [!div class="mx-imgBorder"]
> ![QnA Maker bcp 方案](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

以上呈現的高階構想如下所示：

1. 在 [Azure 配對區域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)中設定兩個平行的 [QnA Maker 服務](set-up-qnamaker-service-azure.md)。

1. [備份](../../../app-service/manage-backup.md)您的主要 QnA Maker App service，並在次要設定中將它[還原](../../../app-service/web-sites-restore.md)。 這可確保這兩個程式都使用相同的主機名稱和金鑰。

1. 保持主要和次要 Azure 搜尋索引的同步。使用[這裡](https://github.com/pchoudhari/QnAMakerBackupRestore)的 GitHub 範例來瞭解如何備份-還原 Azure 索引。

1. 使用[連續匯出](../../../application-insights/app-insights-export-telemetry.md)備份 Application Insights。

1. 設定主要和次要堆疊後，使用[流量管理員](../../../traffic-manager/traffic-manager-overview.md)設定兩個端點，並設定路由方法。

1. 您必須為您的流量管理員端點建立傳輸層安全性（TLS），先前稱為安全通訊端層（SSL）憑證。 系結應用程式服務中[的 TLS/SSL 憑證](../../../app-service/configure-ssl-bindings.md)。

1. 最後，在聊天機器人或應用程式中使用流量管理員端點。

## <a name="delete-azure-resources"></a>刪除 Azure 資源

如果您刪除任何用於 QnA Maker 知識庫的 Azure 資源，知識庫將無法再運作。 在刪除任何資源之前，請務必先從 [設定]**** 頁面匯出知識庫。

## <a name="next-steps"></a>後續步驟

深入瞭解[App service](../../../app-service/index.yml)和[Search 服務](../../../search/index.yml)。

> [!div class="nextstepaction"]
> [瞭解如何與其他人一起撰寫](../how-to/collaborate-knowledge-base.md)
