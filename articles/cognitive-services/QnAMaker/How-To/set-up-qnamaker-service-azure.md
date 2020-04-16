---
title: 設定 QnA 製造商服務 - QnA 製造商
description: 您必須先在 Azure 中設定 QnA Maker 服務，才能建立任何 QnA Maker 知識庫。 任何具備授權而可在訂用帳戶中建立新資源的人，皆可建立 QnA Maker 服務。
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 563a56fdb288568e7fe667fa54658400064a560f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402993"
---
# <a name="manage-qna-maker-resources"></a>管理 QnA 製造商資源

您必須先在 Azure 中設定 QnA Maker 服務，才能建立任何 QnA Maker 知識庫。 任何具備授權而可在訂用帳戶中建立新資源的人，皆可建立 QnA Maker 服務。

在創建資源之前,深入瞭解以下概念很有説明:

* [QnA Maker 資源](../Concepts/azure-resources.md)
* [撰寫並發佈金鑰](../Concepts/azure-resources.md#keys-in-qna-maker)

## <a name="create-a-new-qna-maker-service"></a>建立新的 QnA Maker 服務

此過程創建管理知識庫內容所需的 Azure 資源。 完成這些步驟後,您將在 Azure 門戶中的「**密鑰」** 頁上找到資源的_訂閱_密鑰。

1. 登錄到 Azure 門戶並[創建 QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)資源。

1. 在閱讀條款與條件後選擇 **「 建立**」

    ![建立新的 QnA Maker 服務](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. 在**QnA 製造商中**,選擇相應的層和區域:

    ![建立新的 QnA Maker 服務 - 定價層和區域](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * 在 **「名稱」** 欄位中,輸入唯一名稱以識別此 QnA Maker 服務。 此名稱還標識知識庫將關聯的 QnA Maker 終結點。
    * 選擇設定 QnA Maker 資源的**訂閱**。
    * 選擇 QnA 製造商管理服務(門戶與管理 API)的**定價層**。 檢視[有關 SKU 定價的詳情](https://aka.ms/qnamaker-pricing)。
    * 創建新的資源群組(建議)或使用現有**資源組**來部署此 QnA Maker 資源。 QnA Maker 創建多個 Azure 資源。 建立資源群組以保存這些資源時,可以輕鬆地通過資源組名稱查找、管理和刪除這些資源。
    * 選擇**資源群組位置**。
    * 選擇 Azure 認知搜尋服務的**搜尋定價層**。 如果"免費套餐"選項不可用(顯示為灰色),則意味著您已經通過訂閱部署了免費服務。 在這種情況下,您需要從基本層開始。 請參考[Azure 認知搜尋定價詳細資訊](https://azure.microsoft.com/pricing/details/search/)。
    * 選擇要部署 Azure 認知搜尋索引的**搜尋位置**。 對客戶資料的儲存位置的限制將有助於確定為 Azure 認知搜尋選擇的位置。
    * 在 **"應用名稱**"欄位中,輸入 Azure 應用服務實例的名稱。
    * 默認情況下,應用服務預設為標準 (S1) 層。 您可以在建立之後變更方案。 瞭解有關[應用服務定價](https://azure.microsoft.com/pricing/details/app-service/)的更多。
    * 選擇將部署應用程式服務**的網站位置**。

        > [!NOTE]
        > **搜索位置**可能與**網站位置**不同。

    * 選擇是否要啟用**應用程式的解解**。 如果啟用 **Application Insights**，QnA Maker 會收集流量、交談記錄和錯誤的遙測資料。
    * 選擇應用**見解位置**,其中將部署應用見解資源。
    * 如需節省成本的方法，您可以[共用](#configure-qna-maker-to-use-different-cognitive-search-resource)一些 (不是全部) 為 QnA Maker 建立的 Azure 資源。

1. 驗證所有欄位後,選擇 **"創建**"。 此過程可能需要幾分鐘才能完成。

1. 部署完成後,您將看到訂閱中建立的以下資源:

   ![建立新 QnA Maker 服務的資源](../media/qnamaker-how-to-setup-service/resources-created.png)

    具有_認知服務_類型的資源具有_訂閱_密鑰。

## <a name="find-subscription-keys-in-the-azure-portal"></a>在 Azure 門戶中尋找訂閱金鑰

您可以在 Azure 門戶中查看和重置訂閱密鑰,並創建 QnA Maker 資源。

1. 跳到 Azure 門戶中 QnA Maker 資源,然後選擇具有_認知服務_類型的資源:

    ![QnA Maker 資源清單](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. 跳到**金鑰**:

    ![訂用帳戶金鑰](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="find-endpoint-keys-in-the-qna-maker-portal"></a>在 QnA 製造商門戶中尋找終結點鍵

終結點與資源位於同一區域中,因為終結點鍵用於調用知識庫。

您可以從 [QnA Maker 入口網站](https://qnamaker.ai)來管理端點金鑰。

1. 登入[QnA Maker 門戶](https://qnamaker.ai),轉到您的個人資料,然後選擇 **"服務設置**":

    ![端點金鑰](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. 檢視或重置金鑰:

    > [!div class="mx-imgBorder"]
    > ![端點金鑰管理員](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >如果您認為密鑰已洩露,請刷新您的密鑰。 為此，您可能需要對用戶端應用程式或 Bot 程式碼進行對應的變更。

## <a name="upgrade-qna-maker-sku"></a>升級 QnA 製造商 SKU

當您希望在知識庫中有更多的問題和答案時,除了當前層之外,請升級 QnA Maker 服務定價層。

若要升級 QnA Maker 管理 SKU：

1. 在 Azure 入口網站中移至 QnA Maker 資源，並選取 [定價層]****。

    ![QnA Maker 資源](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

1. 選擇適當的 SKU，並按下 [選取]****。

    ![QnA Maker 價格](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

## <a name="upgrade-app-service"></a>升級應用服務

 當您的知識庫需要從用戶端應用提供更多請求時,請升級應用服務定價層。

您可以[擴展](https://docs.microsoft.com/azure/app-service/manage-scale-up)或擴展應用服務。

跳到 Azure 門戶中的應用服務資源,然後根據需要選擇 **「向上縮放**」或 **「橫向延伸**」選項。

![QnA 製造商應用服務規模](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

## <a name="upgrade-the-azure-cognitive-search-service"></a>升級 Azure 認知搜尋服務

如果計劃擁有許多知識庫,請升級 Azure 認知搜索服務定價層。

目前,無法對 Azure 搜索 SKU 執行就地升級。 不過，您可以使用所需的 SKU 建立新的 Azure 搜尋服務資源，並將資料還原到新的資源，然後再將新的資源連結至 QnA Maker 堆疊。 若要這樣做，請遵循下列步驟：

1. 在 Azure 門戶中創建新的 Azure 搜索資源,然後選擇所需的 SKU。

    ![QnA Maker Azure 搜尋服務資源](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. 從原始的 Azure 搜尋服務資源將索引還原到新的 Azure 搜尋服務資源。 請參考[備份回復範例代碼](https://github.com/pchoudhari/QnAMakerBackupRestore)。

1. 回復資料後,到新的 Azure 搜尋資源,選擇**鍵**,然後記下**名稱****與管理金鑰**:

    ![QnA Maker Azure 搜尋服務索引鍵](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

1. 要將新的 Azure 搜尋資源連結到 QnA Maker 堆疊,請造訪 QnA Maker 應用服務實例。

    ![QnA 製造商應用服務實體](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

1. 選擇**應用程式設定**並修改步驟 3 中的**Azure 搜尋名稱**和**Azure 搜索管理鍵**欄位中的設定。

    ![QnA 製造商應用服務設定](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

1. 重新啟動應用服務實例。

    ![重新啟動 QnA 製造商應用服務實體](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="get-the-latest-runtime-updates"></a>取得最新的執行時更新

QnAMaker 執行時是在 Azure 門戶中[創建 QnAMaker 服務](./set-up-qnamaker-service-azure.md)時部署的 Azure 應用服務實例的一部分。 執行階段會定期進行更新。 QnA Maker 應用服務實例在 2019 年 4 月網站擴展版本(版本 5+)後處於自動更新模式。 此更新旨在處理升級期間的零停機時間。

您可以在https://www.qnamaker.ai/UserSettings上 檢查目前的版本。 如果版本早於版本 5.x,則必須重新啟動應用服務以應用最新更新:

1. 轉到[Azure 門戶](https://portal.azure.com)中的 QnAMaker 服務(資源組)。

    > [!div class="mx-imgBorder"]
    > ![QnAMaker Azure 資源群組](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. 選擇應用服務實例並打開 **「概述」** 部分。

    > [!div class="mx-imgBorder"]
    > ![QnAMaker 應用服務實體](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)


1. 重新啟動應用服務。 更新過程應在幾秒鐘後完成。 在此重新啟動期間,使用此 QnAMaker 服務的任何從屬應用程式或自動程式都將對最終使用者不可用。

    ![重新啟動 QnAMaker 應用服務實體](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="configure-qna-maker-to-use-different-cognitive-search-resource"></a>設定 QnA 製造商以使用不同的認知搜尋資源

如果透過門戶建立 QnA 服務及其依賴項(如搜尋),則為您創建搜尋服務並連結到 QnA Maker 服務。 創建這些資源後,可以更新應用服務設置以使用以前存在的搜索服務,並刪除剛剛創建的搜索服務。

QnA Maker**的應用服務**資源使用認知搜索資源。 為了更改 QnA Maker 使用的認知搜索資源,您需要更改 Azure 門戶中的設置。

1. 取得您希望 QnA Maker 使用的認知搜尋資源的**管理金鑰**與**名稱**。

1. 登入 Azure[門戶](https://portal.azure.com)並找到與您的 QnA Maker 資源關聯的**應用服務**。 兩者具有相同的名稱。

1. 選擇**設定值,** 然後**選擇設定**。 這將顯示QnA製造商應用服務的所有現有設置。

    > [!div class="mx-imgBorder"]
    > ![顯示應用服務設定設定的 Azure 門戶螢幕截圖](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-app-service-configuration.png)

1. 變更以下鍵的值:

    * **Azure 搜尋管理金鑰**
    * **Azure 搜尋名稱**

1. 要使用新設置,您需要重新啟動應用服務。 選擇 **「概覽**」,然後選擇 **「重新啟動**」。

    > [!div class="mx-imgBorder"]
    > ![設定設定變更後 Azure 門戶重新啟動應用服務的螢幕截圖](../media/qnamaker-how-to-upgrade-qnamaker/screenshot-azure-portal-restart-app-service.png)

如果透過 Azure 資源管理器樣本建立 QnA 服務,則可以創建所有資源並控制應用服務創建以使用現有的搜尋服務。

詳細瞭解如何設定應用程式[設定](../../../app-service/configure-common.md#configure-app-settings)。

## <a name="configure-app-service-idle-setting-to-avoid-timeout"></a>設定應用服務空閒設定以避免逾時

應用服務為已發佈的知識庫的 QnA Maker 預測運行時提供服務,具有空閒超時配置,如果服務處於空閒狀態,則默認為自動超時。 對於 QnA Maker,這意味著預測運行時在無流量期間後偶爾會超時應答 API。

為了即使在沒有流量的情況下也能載入預測終結點應用,請將空閒設置為始終打開。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 搜索並選擇QnAMaker資源的應用服務。 它將具有與 QnA Maker 資源相同的名稱,但它將具有**不同類型的**應用服務。
1. 尋找**設定**,然後選擇 **「設定」**。。
1. 在"配置"窗格中,選擇 **"常規設置**",然後查找 **"始終打開**",然後選擇 **"作為**值"。

    > [!div class="mx-imgBorder"]
    > ![在"配置"窗格中,選擇[常規設置],然後查找 [始終打開],然後選擇 [打開] 作為值。](../media/qnamaker-how-to-upgrade-qnamaker/configure-app-service-idle-timeout.png)

1. 選取 [儲存]**** 以儲存組態。
1. 系統會詢問您是否要重新啟動應用以使用新設置。 選取 [繼續]  。

詳細瞭解如何設定服務[一般設定](../../../app-service/configure-common.md#configure-general-settings)。

## <a name="delete-azure-resources"></a>刪除 Azure 資源

如果您刪除任何用於 QnA Maker 知識庫的 Azure 資源，知識庫將無法再運作。 在刪除任何資源之前，請務必先從 [設定]**** 頁面匯出知識庫。

## <a name="next-steps"></a>後續步驟

瞭解有關[應用](../../../app-service/index.yml)服務和[搜索服務的更多。](../../../search/index.yml)

> [!div class="nextstepaction"]
> [建立和發佈知識庫](../Quickstarts/create-publish-knowledge-base.md)
