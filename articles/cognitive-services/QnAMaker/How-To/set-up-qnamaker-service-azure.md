---
title: 設定 QnA Maker 服務-QnA Maker
description: 您必須先在 Azure 中設定 QnA Maker 服務，才能建立任何 QnA Maker 知識庫。 任何具備授權而可在訂用帳戶中建立新資源的人，皆可建立 QnA Maker 服務。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: 5185e7d0bd60eec239f1233db7f9789cbefc2c10
ms.sourcegitcommit: 642988f1ac17cfd7a72ad38ce38ed7a5c2926b6c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94873534"
---
# <a name="manage-qna-maker-resources"></a>管理 QnA Maker 資源

您必須先在 Azure 中設定 QnA Maker 服務，才能建立任何 QnA Maker 知識庫。 任何具備授權而可在訂用帳戶中建立新資源的人，皆可建立 QnA Maker 服務。

在建立您的資源之前，對下列概念有充分的瞭解：

* [QnA Maker 資源](../Concepts/azure-resources.md)
* [撰寫和發佈金鑰](../Concepts/azure-resources.md#keys-in-qna-maker)

## <a name="create-a-new-qna-maker-service"></a>建立新的 QnA Maker 服務

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker 正式發行 (穩定版本)](#tab/v1)

此程式會建立管理知識庫內容所需的 Azure 資源。 完成這些步驟之後，您會在 Azure 入口網站中資源的 [**金鑰**] 頁面上找到 _訂_ 用帳戶金鑰。

1. 登入 Azure 入口網站並 [建立 QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) 資源。

1. 閱讀條款及條件之後，請選取 [ **建立** ]：

    ![建立新的 QnA Maker 服務](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. 在 **QnA Maker** 中，選取適當的層級和區域：

    ![建立新的 QnA Maker 服務 - 定價層和區域](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * 在 [ **名稱** ] 欄位中，輸入用來識別此 QnA Maker 服務的唯一名稱。 此名稱也會識別您的知識庫將與之相關聯的 QnA Maker 端點。
    * 選擇將部署 QnA Maker 資源的 **訂** 用帳戶。
    * 選取 QnA Maker 管理服務 (入口網站和管理 Api) 的 **定價層** 。 查看 [SKU 定價的詳細資料](https://aka.ms/qnamaker-pricing)。
    *  (建議的) 建立新的資源群組，或使用現有的 **資源群組** 來部署此 QnA Maker 資源。 QnA Maker 會建立數個 Azure 資源。 當您建立資源群組來保存這些資源時，您可以透過資源組名輕鬆地尋找、管理和刪除這些資源。
    * 選取 **資源群組的位置**。
    * 選擇 Azure 認知搜尋服務的 **搜尋定價層** 。 如果無法使用 [免費層] 選項 (會呈現暗灰色的) ，表示您已有透過訂用帳戶部署的免費服務。 在這種情況下，您必須從基本層開始。 請參閱 [Azure 認知搜尋定價詳細資料](https://azure.microsoft.com/pricing/details/search/)。
    * 選擇您要在其中部署 Azure 認知搜尋索引的 **搜尋位置** 。 必須儲存客戶資料之位置的限制，將有助於判斷您為 Azure 認知搜尋選擇的位置。
    * 在 [ **應用程式名稱** ] 欄位中，輸入 Azure App Service 實例的名稱。
    * 根據預設，App Service 預設為標準 (S1) 層。 您可以在建立之後變更方案。 深入瞭解 [App Service 定價](https://azure.microsoft.com/pricing/details/app-service/)。
    * 選擇將部署 App Service 的 **網站位置** 。

        > [!NOTE]
        > **搜尋位置** 可能會與 **網站位置** 不同。

    * 選擇您是否要啟用 **Application Insights**。 如果啟用 **Application Insights**，QnA Maker 會收集流量、交談記錄和錯誤的遙測資料。
    * 選擇將部署 Application Insights 資源的 **App insights 位置** 。
    * 如需節省成本的方法，您可以[共用](#configure-qna-maker-to-use-different-cognitive-search-resource)一些 (不是全部) 為 QnA Maker 建立的 Azure 資源。

1. 驗證所有欄位之後，請選取 [ **建立**]。 此程序需要數分鐘的時間完成。

1. 部署完成後，您會看到在訂用帳戶中建立的下列資源：

   ![建立新 QnA Maker 服務的資源](../media/qnamaker-how-to-setup-service/resources-created.png)

    具有 _認知服務_ 類型的資源擁有您的 _訂_ 用帳戶金鑰。

### <a name="upgrade-qna-maker-sku"></a>升級 QnA Maker SKU

如果您想要在您的知識庫中有更多問題和答案，請將您的 QnA Maker 服務定價層升級，而不是目前的層級。

若要升級 QnA Maker 管理 SKU：

1. 在 Azure 入口網站中移至 QnA Maker 資源，並選取 [定價層]。

    ![QnA Maker 資源](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

1. 選擇適當的 SKU，並按下 [選取]。

    ![QnA Maker 價格](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

### <a name="upgrade-app-service"></a>升級 App Service

 當您的知識庫需要從用戶端應用程式提供更多要求時，請升級您的 App Service 定價層。

您可以 [擴大](https://docs.microsoft.com/azure/app-service/manage-scale-up) 或縮小 App Service。

移至 Azure 入口網站中的 App Service 資源，然後視需要選取 [ **擴大** ] 或 [ **scale out** ] 選項。

![QnA Maker App Service 調整規模](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

### <a name="get-the-latest-runtime-updates"></a>取得最新的執行時間更新

QnAMaker 執行時間是當您在 Azure 入口網站中 [建立 QnAMaker 服務](./set-up-qnamaker-service-azure.md) 時所部署 Azure App Service 實例的一部分。 執行階段會定期進行更新。 在2019年4月的網站延伸模組版本 (第5版 +) 之後，QnA Maker App Service 實例處於自動更新模式。 此更新的設計目的是要在升級期間處理零停機。

您可以在上檢查目前的版本 https://www.qnamaker.ai/UserSettings 。 如果您的版本早于5.x 版，則必須重新開機 App Service 以套用最新的更新：

1. 移至 [Azure 入口網站](https://portal.azure.com)中 (資源群組) 的 QnAMaker 服務。

    > [!div class="mx-imgBorder"]
    > ![QnAMaker Azure 資源群組](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. 選取 App Service 實例，然後開啟 [ **總覽** ] 區段。

    > [!div class="mx-imgBorder"]
    > ![QnAMaker App Service 實例](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)


1. 重新開機 App Service。 更新程式應該會在幾秒鐘內完成。 在此重新開機期間，終端使用者將無法使用此 QnAMaker 服務的任何相依應用程式或 bot。

    ![重新開機 QnAMaker App Service 實例](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

### <a name="configure-app-service-idle-setting-to-avoid-timeout"></a>設定 App service 閒置設定以避免超時

App service （為已發佈的知識庫提供 QnA Maker 的預測執行時間）具有閒置的 timeout 設定，預設為在服務閒置時自動超時。 針對 QnA Maker，這表示您的預測執行時間 generateAnswer API 偶爾會在沒有流量的期間結束。

若要在沒有流量的情況下，讓預測端點應用程式保持載入，請將閒置設定為 [永遠開啟]。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 搜尋並選取您 QnA Maker 資源的 app service。 它會與 QnA Maker 資源的名稱相同，但會有不同 **類型** 的 App Service。
1. 尋找 **設定** **，然後** 選取 [設定]。
1. 在 [設定] 窗格中，選取 **[一般設定**]，然後尋找 [ **永遠開啟**]，然後選取 [ **開啟** ] 作為值。

    > [!div class="mx-imgBorder"]
    > ![在 [設定] 窗格中，選取 [一般設定]，然後尋找 [永遠開啟] * *，然後選取 [開啟] * * 作為值。](../media/qnamaker-how-to-upgrade-qnamaker/configure-app-service-idle-timeout.png)

1. 選取 [ **儲存** ] 以儲存設定。
1. 系統會詢問您是否要重新開機應用程式，以使用新的設定。 選取 [繼續]  。

深入瞭解如何設定 App Service [一般設定](../../../app-service/configure-common.md#configure-general-settings)。

### <a name="configure-app-service-environment-to-host-qna-maker-app-service"></a>將 App Service 環境設定為裝載 QnA Maker App Service
App Service 環境可以用來裝載 QnA Maker App Service。 如果 App Service 環境是內部的，您就必須遵循下列步驟：
1. 建立 App service 和 Azure 搜尋服務。
2. 公開 app service，並允許 QnA Maker 可用性，如下所示：
    * 公開可用-預設值
    * DNS 服務標記： `CognitiveServicesManagement`
3. 使用 Azure Resource Manager 建立 QnA Maker 認知服務實例 (CognitiveServices/帳戶) ，其中 QnA Maker 端點應設定為 App Service 環境。

### <a name="network-isolation-for-app-service"></a>App Service 的網路隔離

QnA Maker 認知服務使用服務標記： `CognitiveServicesManagement` 。 請遵循下列步驟，將 IP 位址範圍新增至允許清單：

* 下載 [所有服務標記的 IP 範圍](https://www.microsoft.com/download/details.aspx?id=56519)。
* 選取 "CognitiveServicesManagement" 的 Ip。
* 流覽至您 App Service 資源的 [網路] 區段，然後按一下 [設定存取限制] 選項，將 Ip 新增至允許清單。

我們也有自動化腳本，可針對您的 App Service 進行相同的作業。 您可以在 GitHub 上找到用 [來設定允許清單的 PowerShell 腳本](https://github.com/pchoudhari/QnAMakerBackupRestore/blob/master/AddRestrictedIPAzureAppService.ps1) 。 您必須輸入訂用帳戶識別碼、資源群組，以及實際 App Service 名稱作為腳本參數。 執行腳本會自動將 Ip 新增至 App Service 允許清單。

### <a name="business-continuity-with-traffic-manager"></a>使用流量管理員進行商務持續性

商務持續性方案的主要目標是建立彈性的知識庫端點，藉以確保聊天機器人或應用程式使用知識庫時不會停機。

> [!div class="mx-imgBorder"]
> ![QnA Maker bcp 方案](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

以上呈現的高階構想如下所示：

1. 在 [Azure 配對區域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)中設定兩個平行的 [QnA Maker 服務](set-up-qnamaker-service-azure.md)。

1. [備份](../../../app-service/manage-backup.md) 您的主要 QnA Maker App service，並在次要設定中加以 [還原](../../../app-service/web-sites-restore.md) 。 這可確保這兩個程式都使用相同的主機名稱和金鑰。

1. 讓主要和次要 Azure 搜尋服務索引保持同步。您可以使用 [這裡](https://github.com/pchoudhari/QnAMakerBackupRestore) 的 GitHub 範例來瞭解如何備份-還原 Azure 索引。

1. 使用[連續匯出](../../../application-insights/app-insights-export-telemetry.md)備份 Application Insights。

1. 設定主要和次要堆疊後，使用[流量管理員](../../../traffic-manager/traffic-manager-overview.md)設定兩個端點，並設定路由方法。

1. 您必須為您的流量管理員端點建立傳輸層安全性 (TLS) ，之前稱為安全通訊端層 (SSL) 憑證。 將[TLS/SSL 憑證](../../../app-service/configure-ssl-bindings.md)系結到您的應用程式服務。

1. 最後，在聊天機器人或應用程式中使用流量管理員端點。

# <a name="qna-maker-managed-preview-release"></a>[受控 QnA Maker (預覽版本)](#tab/v2)

此程式會建立管理知識庫內容所需的 Azure 資源。 完成這些步驟之後，您會在 Azure 入口網站中資源的 [**金鑰**] 頁面上找到 *訂* 用帳戶金鑰。

1. 登入 Azure 入口網站並 [建立 QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) 資源。

1. 閱讀條款及條件之後，請選取 [ **建立** ]：

    ![建立新的 QnA Maker 服務](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. 在 **QnA Maker** 中，核取 [受控 (預覽]) 核取方塊，然後選取適當的層和區域：

    ![建立新的 QnA Maker 受控服務-定價層和區域](../media/qnamaker-how-to-setup-service/enter-qnamaker-v2-info.png)

    * 選擇將部署 QnA Maker 資源的 **訂** 用帳戶。
    *  (建議的) 建立新的資源群組，或使用現有的 **資源群組** ，將此 QnA Maker 受控 (Preview) 資源部署到其中。 QnA Maker 受控 (預覽) 會建立一些 Azure 資源。 當您建立資源群組來保存這些資源時，您可以透過資源組名輕鬆地尋找、管理和刪除這些資源。
    * 在 [ **名稱** ] 欄位中，輸入唯一名稱來識別此 QnA Maker 受控 (預覽) 服務。 
    * 選擇您要部署 QnA Maker 受控 (預覽版) 服務的 **位置** 。 管理 Api 和服務端點將裝載于此位置。 
    * 選取 QnA Maker 受控 (預覽版) 服務的 **定價層** ， (免費預覽) 。 查看 [SKU 定價的詳細資料](https://aka.ms/qnamaker-pricing)。
    * 選擇您要在其中部署 Azure 認知搜尋索引的 **搜尋位置** 。 必須儲存客戶資料之位置的限制，將有助於判斷您為 Azure 認知搜尋選擇的位置。
    * 選擇 Azure 認知搜尋服務的 **搜尋定價層** 。 如果無法使用 [免費層] 選項 (會呈現暗灰色的) ，表示您已有透過訂用帳戶部署的免費服務。 在這種情況下，您必須從基本層開始。 請參閱 [Azure 認知搜尋定價詳細資料](https://azure.microsoft.com/pricing/details/search/)。

1. 驗證所有欄位之後，請選取 [ **審核 + 建立**]。 此程序需要數分鐘的時間完成。

1. 部署完成後，您會看到在訂用帳戶中建立的下列資源：

    ![資源已建立新的 QnA Maker 受控 (預覽版) 服務](../media/qnamaker-how-to-setup-service/resources-created-v2.png)

    具有 _認知服務_ 類型的資源擁有您的 _訂_ 用帳戶金鑰。

---

## <a name="find-authoring-keys-in-the-azure-portal"></a>在 Azure 入口網站中尋找撰寫金鑰

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker 正式發行 (穩定版本)](#tab/v1)

您可以從建立 QnA Maker 資源的 Azure 入口網站，查看並重設您的撰寫金鑰。 這些金鑰可能稱為訂用帳戶金鑰。

1. 移至 Azure 入口網站中的 QnA Maker 資源，然後選取具有 _認知服務_ 類型的資源：

    ![QnA Maker 資源清單](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. 移至 [ **金鑰**]：

    ![訂用帳戶金鑰](../media/qnamaker-how-to-key-management/subscription-key.PNG)

### <a name="find-query-endpoint-keys-in-the-qna-maker-portal"></a>在 QnA Maker 入口網站中尋找查詢端點金鑰

端點與資源位於相同的區域中，因為端點金鑰是用來對知識庫進行呼叫。

您可以從 [QnA Maker 入口網站](https://qnamaker.ai)來管理端點金鑰。

1. 登入 [QnA Maker 入口網站](https://qnamaker.ai)，移至您的設定檔，然後選取 [ **服務設定**]：

    ![端點金鑰](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. 查看或重設您的金鑰：

    > [!div class="mx-imgBorder"]
    > ![端點金鑰管理員](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >如果您認為金鑰已遭入侵，請重新整理您的金鑰。 為此，您可能需要對用戶端應用程式或 Bot 程式碼進行對應的變更。

# <a name="qna-maker-managed-preview-release"></a>[受控 QnA Maker (預覽版本)](#tab/v2)

您可以從 Azure 入口網站（您在其中建立 QnA Maker 受控 (預覽) 資源），查看並重設您的撰寫金鑰。 這些金鑰可能稱為訂用帳戶金鑰。

1. 移至 Azure 入口網站中 QnA Maker 受控 (預覽) 資源，然後選取具有 *認知服務* 類型的資源：

    ![QnA Maker 受控 (預覽) 資源清單](../media/qnamaker-how-to-key-management/qnamaker-v2-resource-list.png)

2. 移至 **金鑰和端點**：

    ![QnA Maker 受控 (預覽) 訂用帳戶金鑰](../media/qnamaker-how-to-key-management/subscription-key-v2.png)

### <a name="update-the-resources"></a>更新資源

瞭解如何升級知識庫所使用的資源。 QnA Maker 受控 (預覽) 在預覽期間 **免費** 。 

---

## <a name="upgrade-the-azure-cognitive-search-service"></a>升級 Azure 認知搜尋服務

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker 正式發行 (穩定版本)](#tab/v1)

如果您打算有許多知識庫，請升級您的 Azure 認知搜尋服務定價層。

目前，您無法執行 Azure 搜尋服務 SKU 的就地升級。 不過，您可以使用所需的 SKU 建立新的 Azure 搜尋服務資源，並將資料還原到新的資源，然後再將新的資源連結至 QnA Maker 堆疊。 若要執行此動作，請依照下列步驟執行：

1. 在 Azure 入口網站中建立新的 Azure 搜尋服務資源，然後選取所需的 SKU。

    ![QnA Maker Azure 搜尋服務資源](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. 從原始的 Azure 搜尋服務資源將索引還原到新的 Azure 搜尋服務資源。 請參閱 [備份還原範例程式碼](https://github.com/pchoudhari/QnAMakerBackupRestore)。

1. 還原資料之後，請移至新的 Azure 搜尋服務資源、選取 **金鑰**，並記下 **名稱** 和 **管理金鑰**：

    ![QnA Maker Azure 搜尋服務索引鍵](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

1. 若要將新的 Azure 搜尋服務資源連結至 QnA Maker 堆疊，請移至 QnA Maker App Service 實例。

    ![QnA Maker App Service 實例](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

1. 選取 [ **應用程式設定** ]，並修改步驟 3 **AzureSearchName** 和 **AzureSearchAdminKey** 欄位中的設定。

    ![QnA Maker App Service 設定](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

1. 重新開機 App Service 實例。

    ![重新開機 QnA Maker App Service 實例](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

### <a name="cognitive-search-consideration"></a>認知搜尋考慮

認知搜尋作為個別資源，有一些您應該注意的不同設定。

### <a name="configure-qna-maker-to-use-different-cognitive-search-resource"></a>將 QnA Maker 設定為使用不同的認知搜尋資源

如果您透過入口網站建立 QnA 服務及其相依性 (例如搜尋) ，系統就會為您建立一個搜尋服務，並連結到 QnA Maker 服務。 建立這些資源之後，您可以將 App Service 設定更新為使用先前現有的搜尋服務，並移除您剛才建立的。

QnA Maker 的 **App Service** 資源使用認知搜尋資源。 為了變更 QnA Maker 所使用的認知搜尋資源，您需要變更 Azure 入口網站中的設定。

1. 取得您想要 QnA Maker 使用之認知搜尋資源的系統 **管理金鑰** 和 **名稱** 。

1. 登入 [Azure 入口網站](https://portal.azure.com) ，並尋找與您的 QnA Maker 資源相關聯的 **App Service** 。 兩者都有相同的名稱。

1. 選取 [設定] **，然後選取**[**設定**]。 這會顯示 QnA Maker 的 App Service 的所有現有設定。

    > [!div class="mx-imgBorder"]
    > ![顯示 App Service 設定設定 Azure 入口網站的螢幕擷取畫面](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-app-service-configuration.png)

1. 變更下列索引鍵的值：

    * **AzureSearchAdminKey**
    * **AzureSearchName**

1. 若要使用新的設定，您必須重新開機 App service。 選取 **[總覽**]，然後選取 [ **重新開機**]。

    > [!div class="mx-imgBorder"]
    > ![Azure 入口網站在設定設定變更後重新開機 App Service 的螢幕擷取畫面](../media/qnamaker-how-to-upgrade-qnamaker/screenshot-azure-portal-restart-app-service.png)

如果您透過 Azure Resource Manager 範本建立 QnA 服務，您可以建立所有資源，並控制 App Service 建立，以使用現有的搜尋服務。

深入瞭解如何設定 App Service 的 [應用程式設定](../../../app-service/configure-common.md#configure-app-settings)。

### <a name="configuring-cognitive-search-as-a-private-endpoint-inside-a-vnet"></a>將認知搜尋設定為 VNET 內的私人端點

在 QnA Maker 資源建立期間建立搜尋實例時，您可以強制認知搜尋支援完全在客戶的 VNet 內建立的私人端點設定。

所有資源都必須建立在相同的區域中，才能使用私人端點。

* QnA Maker 資源
* 新的認知搜尋資源
* 新的虛擬網路資源

在 [Azure 入口網站](https://portal.azure.com)中完成下列步驟：

1. 建立 [QnA Maker 資源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)。
1. 建立新的認知搜尋資源， (資料) 設定為 _私_ 用端點連線能力。 在與步驟1中建立的 QnA Maker 資源相同的區域中建立資源。 深入瞭解如何 [建立認知搜尋資源](../../../search/search-create-service-portal.md)，然後使用此連結直接移至資源的 [ [建立] 頁面](https://ms.portal.azure.com/#create/Microsoft.Search)。
1. 建立新的 [虛擬網路資源](https://ms.portal.azure.com/#create/Microsoft.VirtualNetwork-ARM)。
1. 在此程式的步驟1中建立的 App service 資源上設定 VNET。
    1. 針對步驟2中建立的新認知搜尋資源，在 VNET 中建立新的 DNS 專案。 至認知搜尋 IP 位址。
1. [將 App service 與步驟2中建立的新認知搜尋資源產生關聯](#configure-qna-maker-to-use-different-cognitive-search-resource) 。 然後，您可以刪除在步驟1中建立的原始認知搜尋資源。

在 [QnA Maker 入口網站](https://www.qnamaker.ai/)中，建立您的第一個知識庫。


### <a name="inactivity-policy-for-free-search-resources"></a>免費搜尋資源的無活動原則

如果您不是使用 QnA maker 資源，則應移除所有資源。 如果您未移除未使用的資源，當您建立免費搜尋資源時，知識庫將會停止運作。

免費搜尋資源會在90天后刪除，而不會收到 API 呼叫。

# <a name="qna-maker-managed-preview-release"></a>[受控 QnA Maker (預覽版本)](#tab/v2)

如果您打算有許多知識庫，請升級您的 Azure 認知搜尋服務定價層。

目前，您無法執行 Azure 搜尋服務 SKU 的就地升級。 不過，您可以使用所需的 SKU 建立新的 Azure 搜尋服務資源，並將資料還原到新的資源，然後再將新的資源連結至 QnA Maker 堆疊。 若要執行此動作，請依照下列步驟執行：

1. 在 Azure 入口網站中建立新的 Azure 搜尋服務資源，然後選取所需的 SKU。

    ![QnA Maker Azure 搜尋服務資源](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. 從原始的 Azure 搜尋服務資源將索引還原到新的 Azure 搜尋服務資源。 請參閱 [備份還原範例程式碼](https://github.com/pchoudhari/QnAMakerBackupRestore)。

1. 若要將新的 Azure 搜尋服務資源連結至 QnA Maker 受控 (Preview) 服務，請參閱下列主題。

### <a name="configure-qna-maker-managed-preview-service-to-use-different-cognitive-search-resource"></a>設定 QnA Maker 受控 (Preview) 服務使用不同的認知搜尋資源

如果您建立 QnA service 管理 (預覽) 及其相依性 (例如透過入口網站) 搜尋，則會為您建立一個搜尋服務，並連結至 QnA Maker 受控 (Preview) 服務。 建立這些資源之後，您 **可以在 [設定] 索引** 標籤中更新搜尋服務。

1. 移至 Azure 入口網站中 QnA Maker 受控 (Preview) 服務。

1. 選取 [設定] **，然後選取** 您想要與 QnA Maker 受控 (Preview) 服務連結的 Azure 認知搜尋服務。

    ![QnA Maker 受控 (預覽) 設定頁面的螢幕擷取畫面](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-configuration.png)

1. 按一下 [檔案] 。

> [!NOTE]
> 如果您變更與 QnA Maker 相關聯的 Azure 搜尋服務，您將無法存取所有已存在的知識庫。 變更 Azure 搜尋服務之前，請務必先匯出現有的知識庫。
### <a name="inactivity-policy-for-free-search-resources"></a>免費搜尋資源的無活動原則

如果您不是使用 QnA maker 資源，則應移除所有資源。 如果您未移除未使用的資源，當您建立免費搜尋資源時，知識庫將會停止運作。

免費搜尋資源會在90天后刪除，而不會收到 API 呼叫。

---

## <a name="delete-azure-resources"></a>刪除 Azure 資源

如果您刪除任何用於 QnA Maker 知識庫的 Azure 資源，知識庫將無法再運作。 在刪除任何資源之前，請務必先從 [設定] 頁面匯出知識庫。

## <a name="next-steps"></a>後續步驟

深入瞭解 [App service](../../../app-service/index.yml) 及 [搜尋服務](../../../search/index.yml)。

> [!div class="nextstepaction"]
> [瞭解如何與其他人進行撰寫](../how-to/collaborate-knowledge-base.md)
