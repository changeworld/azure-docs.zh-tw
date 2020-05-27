---
title: 常見問題（FAQ）-LUIS
description: 本文包含 Language Understanding (LUIS) 常見問題集的解答。
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: diberry
ms.openlocfilehash: 79cee94a0f04cb89aa65f65cde63e48b6f078d2c
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/25/2020
ms.locfileid: "83834997"
---
# <a name="language-understanding-frequently-asked-questions-faq"></a>Language Understanding 常見問題集 (FAQ)

本文包含 Language Understanding (LUIS) 常見問題集的解答。

## <a name="whats-new"></a>新功能

[深入瞭解](whats-new.md)Language Understanding 的新功能（LUIS）。

<a name="luis-authoring"></a>

## <a name="authoring"></a>編寫

### <a name="what-are-the-luis-best-practices"></a>LUIS 最佳做法為何？
從[撰寫週期](luis-concept-app-iteration.md)開始，然後閱讀[最佳做法](luis-concept-best-practices.md)。

### <a name="what-is-the-best-way-to-start-building-my-app-in-luis"></a>在 LUIS 中開始建置應用程式的最佳方式為何？

建置應用程式的最佳方式是透過[累加程序](luis-concept-app-iteration.md)。

### <a name="what-is-a-good-practice-to-model-the-intents-of-my-app-should-i-create-more-specific-or-more-generic-intents"></a>什麼是建立應用程式意圖模型的良好做法？ 應該建立更具體或更通用的意圖嗎？

選擇較特定不易重疊的意圖，但也不要特別到 LUIS 難以區別類似的意圖。 建立不同的特定意圖是 LUIS 模型的其中一個最佳做法。

### <a name="is-it-important-to-train-the-none-intent"></a>訓練 None 意圖重要嗎？

是，將更多標籤新增至其他意圖時，最好使用更多的語句來訓練 **None** 意圖。 針對新增至意圖的每 10 個標籤，最好的比率是新增至 **None** 的 1 或 2 個標籤。 此比率可提升 LUIS 的辨别力。

### <a name="how-can-i-correct-spelling-mistakes-in-utterances"></a>如何更正語句中的拼字錯誤？

請參閱 [Bing 拼字檢查 API V7](luis-tutorial-bing-spellcheck.md) 教學課程。 LUIS 會強制執行 Bing 拼字檢查 API V7 所公開的限制。

### <a name="how-do-i-edit-my-luis-app-programmatically"></a>如何以程式設計方式編輯 LUIS 應用程式？
若要以程式設計方式編輯 LUIS 應用程式，請使用[撰寫 API](https://go.microsoft.com/fwlink/?linkid=2092087)。 如需如何呼叫撰寫 API 的範例，請參閱[呼叫 LUIS 撰寫 API](./get-started-get-model-rest-apis.md)和[使用 Node.js 以程式設計方式建置 LUIS 應用程式](./luis-tutorial-node-import-utterances-csv.md)。 撰寫 API 需要您使用[撰寫金鑰](luis-concept-keys.md#azure-resources-for-luis)，而非端點金鑰。 以程式設計撰寫允許每個月有最多 1,000,000 次呼叫，而且每秒五次交易。 如需與 LUIS 搭配使用之金鑰的詳細資訊，請參閱[管理金鑰](./luis-concept-keys.md)。

### <a name="where-is-the-pattern-feature-that-provided-regular-expression-matching"></a>提供規則運算式比對的模式功能位置為何？
先前的**模式功能**目前已淘汱，並取代為**[模式](luis-concept-patterns.md)**。

### <a name="how-do-i-use-an-entity-to-pull-out-the-correct-data"></a>如何使用實體拉出正確的資料？
請參閱[實體](luis-concept-entity-types.md)和[資料擷取](luis-concept-data-extraction.md)。

### <a name="should-variations-of-an-example-utterance-include-punctuation"></a>範例語句的變化應該包括標點符號嗎？
使用下列其中一個解決方案：
* 忽略[標點符號](luis-reference-application-settings.md#punctuation-normalization)
* 將不同的變化當做範例語句新增至意圖
* [以忽略](luis-concept-patterns.md#pattern-syntax)標點符號的語法，新增範例語句的模式。

### <a name="does-luis-currently-support-cortana"></a>LUIS 目前支援 Cortana 嗎？

Cortana 預先建置的應用程式已在 2017 年被取代。 它們不再受到支援。

### <a name="how-do-i-transfer-ownership-of-a-luis-app"></a>如何移轉 LUIS 應用程式的擁有權？
若要將 LUIS 應用程式傳送至不同的 Azure 訂用帳戶，請匯出 LUIS 應用程式，並使用新的帳戶匯入它。 在呼叫 LUIS 應用程式的用戶端應用程式中，更新 LUIS 應用程式識別碼。 新應用程式可能會傳回與原始應用程式略為不同的 LUIS 分數。

### <a name="a-prebuilt-entity-is-tagged-in-an-example-utterance-instead-of-my-custom-entity-how-do-i-fix-this"></a>預建實體會在範例語句中加上標籤，而不是我的自訂實體。 我要如何修正此問題？

在 LUIS 入口網站中，您可以針對您想要解壓縮的確切實體，為文字加上標籤。 如果 LUIS 入口網站未顯示正確的實體預測，您可能需要新增更多語句，並在文字內標記實體，或加入功能。

### <a name="i-tried-to-import-an-app-or-version-file-but-i-got-an-error-what-happened"></a>我嘗試匯入應用程式或版本檔案，但收到錯誤訊息，發生了什麼事？

深入閱讀[版本匯入錯誤](luis-how-to-manage-versions.md#import-errors)。

<a name="luis-collaborating"></a>

## <a name="collaborating-and-contributing"></a>共同作業和貢獻

### <a name="how-do-i-give-collaborators-access-to-luis-with-azure-active-directory-azure-ad-or-role-based-access-control-rbac"></a>如何使用 Azure Active Directory (Azure AD) 或角色型存取控制 (RBAC)，來為共同作業者提供存取 LUIS 的權限？

若要了解如何為共同作業者提供存取權限，請參閱 [Azure Active Directory 資源](luis-how-to-collaborate.md#azure-active-directory-resources)和 [Azure Active Directory 租用戶使用者](luis-how-to-collaborate.md#azure-active-directory-tenant-user)。

<a name="luis-endpoint"></a>

## <a name="endpoint"></a>端點

### <a name="i-received-an-http-403-error-status-code-how-do-i-fix-it"></a>我收到 HTTP 403 錯誤狀態碼。 如何修正問題？

當您超過每秒的交易數，或您的定價層每月筆交易時，您會收到403和429錯誤狀態碼。 增加您的定價層，或使用 Language Understanding[容器](luis-container-howto.md)。

當您使用所有免費的1000端點查詢，或超出定價層的每月交易配額時，您會收到 HTTP 403 錯誤狀態碼。

若要修正此錯誤，您必須將[定價層變更](luis-how-to-azure-subscription.md#change-pricing-tier)為較高的層級，或[建立新的資源](get-started-portal-deploy-app.md#create-the-endpoint-resource)，並[將它指派給您的應用程式](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal)。

此錯誤的解決方案包括：

* 在[Azure 入口網站](https://portal.azure.com)的 Language Understanding 資源上，在**資源管理-> 定價層**上，將定價層變更為較高的 TPS 層。 如果您的資源已指派給您的 Language Understanding 應用程式，您就不需要在 Language Understanding 入口網站中執行任何動作。
*  如果您的使用量超過最高的定價層，請新增更多 Language Understanding 資源，並在其前面加上負載平衡器。 具有 Kubernetes 或 Docker Compose 的[Language Understanding 容器](luis-container-howto.md)可以協助進行此工作。

### <a name="i-received-an-http-429-error-status-code-how-do-i-fix-it"></a>我收到 HTTP 429 錯誤狀態碼。 如何修正問題？

當您超過每秒的交易數，或您的定價層每月筆交易時，您會收到403和429錯誤狀態碼。 增加您的定價層，或使用 Language Understanding[容器](luis-container-howto.md)。

當您的每秒交易數超過您的定價層時，就會傳回此狀態碼。

解決方案包括：

* 如果您不是最高層級，您可以[增加定價層](luis-how-to-azure-subscription.md#change-pricing-tier)。
* 如果您的使用量超過最高的定價層，請新增更多 Language Understanding 資源，並在其前面加上負載平衡器。 具有 Kubernetes 或 Docker Compose 的[Language Understanding 容器](luis-container-howto.md)可以協助進行此工作。
* 當您取得此狀態碼時，您可以使用您自行執行的[重試原則](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults#general-guidelines)來閘道用戶端應用程式要求。

### <a name="my-endpoint-query-returned-unexpected-results-what-should-i-do"></a>端點查詢傳回非預期的結果。 我該怎麼辦？

非預期查詢預測結果是根據已發佈模型的狀態而定。 若要更正模型，您可能需要變更模型、定型和重新發行。

模型更正要先從[主動式學習](luis-how-to-review-endpoint-utterances.md)開始。

您可以更新[應用程式版本設定 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings)，移除非確定性的定型，以便使用所有定型資料。

如需其他提示，請檢閱[最佳做法](luis-concept-best-practices.md)。

### <a name="why-does-luis-add-spaces-to-the-query-around-or-in-the-middle-of-words"></a>為什麼 LUIS 在查詢周圍或字組中間新增空格？
LUIS 會根據[文化特性 (Culture)](luis-language-support.md#tokenization) 將語句[權杖化](luis-glossary.md#token)。 原始值和權杖化值可供[資料擷取](luis-concept-data-extraction.md#tokenized-entity-returned)。

### <a name="how-do-i-create-and-assign-a-luis-endpoint-key"></a>如何建立和指派 LUIS 端點金鑰？
Azure 中針對[服務](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)層級[建立端點金鑰](luis-how-to-azure-subscription.md)。 在 [ **[Azure 資源](luis-how-to-azure-subscription.md)**] 頁面上[指派金鑰](luis-how-to-azure-subscription.md)。 此動作沒有任何對應的 API。 您接著必須將 HTTP 要求變更為端點，以[使用新端點金鑰](luis-concept-keys.md)。

### <a name="how-do-i-interpret-luis-scores"></a>如何解譯 LUIS 分數？
您的系統應該使用最高分數意圖，而不論其值為何。 例如，低於 0.5 (小於 50%) 的分數不一定表示 LUIS 具有低的信賴度。 提供更多訓練資料有助於提高最可能意圖的[分數](luis-concept-prediction-score.md)。

### <a name="why-dont-i-see-my-endpoint-hits-in-my-apps-dashboard"></a>為什麼在應用程式儀表板中看不到我的端點叫用？
應用程式儀表板中的端點叫用總數均會定期更新，但會更頻繁地更新 Azure 入口網站中與 LUIS 端點金鑰相關聯的計量。

若您在儀表板中看不到已更新的端點叫用，請登入 Azure 入口網站，並找到與您 LUIS 端點金鑰相關聯的資源，然後開啟 [計量]**** 來選取 [呼叫總計]**** 計量。 若將端點金鑰用於多個 LUIS 應用程式，Azure 入口網站中的計量會顯示使用該計量之所有 LUIS 應用程式的呼叫彙總次數。

### <a name="is-there-a-powershell-command-get-to-the-endpoint-quota"></a>端點配額是否有 PowerShell 命令？

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

您可以使用 PowerShell 命令查看端點配額：

```powershell
Get-AzCognitiveServicesAccountUsage -ResourceGroupName <your-resource-group> -Name <your-resource-name>
```

### <a name="my-luis-app-was-working-yesterday-but-today-im-getting-403-errors-i-didnt-change-the-app-how-do-i-fix-it"></a>LUIS 應用程式昨天還可以運作，但現在我收到 403 錯誤。 我未變更應用程式。 如何修正問題？
請遵循這些[指示](#how-do-i-create-and-assign-a-luis-endpoint-key)來建立 LUIS 端點金鑰，並將它指派給應用程式。 接著，您必須將用戶端應用程式的 HTTP 要求變更為端點，以[使用新的端點金鑰](luis-concept-keys.md)。 如果您在不同的區域中建立了新的資源，也請變更 HTTP 用戶端要求的區域。

### <a name="how-do-i-secure-my-luis-endpoint"></a>如何保護 LUIS 端點？
請參閱[保護端點](luis-concept-keys.md#securing-the-endpoint)。

## <a name="working-within-luis-limits"></a>在 LUIS 限制內運作

### <a name="what-is-the-maximum-number-of-intents-and-entities-that-a-luis-app-can-support"></a>LUIS 應用程式可支援的意圖和實體數目上限為何？
請參閱[界限](luis-limits.md)參考。

### <a name="i-want-to-build-a-luis-app-with-more-than-the-maximum-number-of-intents-what-should-i-do"></a>我想要建置具有超過意圖數目上限的 LUIS 應用程式。 我該怎麼辦？

請參閱[意圖最佳做法](luis-concept-intent.md#if-you-need-more-than-the-maximum-number-of-intents)。

### <a name="i-want-to-build-an-app-in-luis-with-more-than-the-maximum-number-of-entities-what-should-i-do"></a>我想要在 LUIS 中建置具有超過實體數目上限的應用程式。 我該怎麼辦？

請參閱[實體最佳做法](luis-concept-entity-types.md#if-you-need-more-than-the-maximum-number-of-entities)

### <a name="what-are-the-limits-on-the-number-and-size-of-phrase-lists"></a>片語清單數目和大小的限制為何？
如需[片語清單](./luis-concept-feature.md)的最大長度，請參閱[界限](luis-limits.md)參考。

### <a name="what-are-the-limits-on-example-utterances"></a>範例語句的限制有哪些？
請參閱[界限](luis-limits.md)參考。

## <a name="testing-and-training"></a>測試和訓練

### <a name="i-see-some-errors-in-the-batch-testing-pane-for-some-of-the-models-in-my-app-how-can-i-address-this-problem"></a>我在批次測試窗格中看到應用程式中某些模型的一些錯誤。 如何解決此問題？

這些錯誤指出您的標籤與您模型中的預測有些不一致。 若要解決此問題，請執行下列其中一或兩項工作：
* 為了協助 LUIS 改善意圖的區別，請新增更多標籤。
* 為了協助 LUIS 更快速地學習，請新增片語清單功能，以引進定義域專屬詞彙。

請參閱[批次測試](luis-tutorial-batch-testing.md)教學課程。

### <a name="when-an-app-is-exported-then-reimported-into-a-new-app-with-a-new-app-id-the-luis-prediction-scores-are-different-why-does-this-happen"></a>應用程式在匯出後再重新匯入至新應用程式 (具有新的應用程式識別碼) 時，LUIS 預測分數會不同。 為何發生這種情況？

請參閱[相同應用程式之不同複本的預測差異](luis-concept-prediction-score.md#review-intents-with-similar-scores)。

### <a name="some-utterances-go-to-the-wrong-intent-after-i-made-changes-to-my-app-the-issue-seems-to-disappear-at-random-how-do-i-fix-it"></a>我對應用程式進行變更後，一些語句的意圖錯誤。 此問題似乎會隨機消失。 如何修正問題？

請參閱[以所有資料進行訓練](luis-how-to-train.md#train-with-all-data)。

## <a name="app-publishing"></a>應用程式發佈

### <a name="what-is-the-tenant-id-in-the-add-a-key-to-your-app-window"></a>[Add a key to your app] \(將金鑰新增至應用程式\) 視窗中的租用戶識別碼是什麼？
在 Azure 中，租用戶代表與服務建立關聯的用戶端或組織。 選取 [Azure Active Directory]**** > [管理]**** > [內容]****，以在 Azure 入口網站的 [目錄識別碼]**** 方塊中找到您的租用戶識別碼。

![Azure 入口網站中的租用戶識別碼](./media/luis-manage-keys/luis-assign-key-tenant-id.png)

<a name="why-are-there-more-subscription-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>
<a name="why-are-there-more-endpoint-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>


### <a name="why-are-there-more-endpoint-keys-assigned-to-my-app-than-i-assigned"></a>為什麼指派給應用程式的端點金鑰數目比我指派的還多？
每個 LUIS 應用程式都會在端點清單中納入撰寫/起始金鑰，以便使用。 此金鑰僅允許數次端點叫用，以便讓您試用 LUIS。

如果您的應用程式在 LUIS 正式推出 (GA) 之前即已存在，就會自動指派您訂用帳戶中的 LUIS 端點金鑰。 完成這項作業，即可簡化 GA 移轉。 Azure 入口網站中任何新的 LUIS 端點金鑰都_不會_自動指派給 LUIS。

## <a name="key-management"></a>金鑰管理

### <a name="how-do-i-know-what-key-i-need-where-i-get-it-and-what-i-do-with-it"></a>如何知道我需要哪個金鑰、可在何處取得，以及如何運用它？

請參閱[LUIS 中的撰寫與查詢預測端點金鑰](luis-concept-keys.md)，以瞭解撰寫金鑰和預測執行時間金鑰之間的差異。

### <a name="i-got-an-error-about-being-out-of-quota-how-do-i-fix-it"></a>我收到了有關超出配額的錯誤。 如何修正問題？

若要深入瞭解，請參閱修正 HTTP 狀態碼[403](#i-received-an-http-403-error-status-code-how-do-i-fix-it)和[429](#i-received-an-http-429-error-status-code-how-do-i-fix-it) 。

### <a name="i-need-to-handle-more-endpoint-queries-how-do-i-do-that"></a>我需要處理更多端點查詢。 該怎麼做？

若要深入瞭解，請參閱修正 HTTP 狀態碼[403](#i-received-an-http-403-error-status-code-how-do-i-fix-it)和[429](#i-received-an-http-429-error-status-code-how-do-i-fix-it) 。

### <a name="i-created-an-authoring-key-but-it-isnt-showing-in-the-luis-portal-what-happened"></a>我建立了撰寫金鑰，但它並未顯示在 LUIS 入口網站中。 發生什麼事？

在[遷移至撰寫金鑰體驗](luis-migration-authoring.md)之後，可在 LUIS 入口網站中取得撰寫金鑰。

## <a name="app-management"></a>應用程式管理

### <a name="how-do-i-download-a-log-of-user-utterances"></a>如何下載使用者語句的記錄？
LUIS 應用程式預設會記錄使用者的語句。 若要下載使用者傳送給 LUIS 應用程式的語句記錄，請前往 [我的應用程式]****，然後選取應用程式。 在關聯式工作列中，選取 [匯出端點記錄]****。 記錄會格式化為逗號分隔值 (CSV) 檔案。

### <a name="how-can-i-disable-the-logging-of-utterances"></a>如何停用語句的記錄？
您可以在用戶端應用程式用來查詢 LUIS 的端點 URL 中設定 `log=false`，以關閉使用者語句記錄。 不過，關閉記錄會停用 LUIS 應用程式建議語句的能力，或根據[主動學習](luis-concept-review-endpoint-utterances.md#what-is-active-learning)來改善效能。 若您因資料隱私考量而設定 `log=false`，則無法從 LUIS 下載這些使用者語句的記錄，或使用者這些語句來改善應用程式。

記錄是唯一的語句儲存體。

### <a name="why-dont-i-want-all-my-endpoint-utterances-logged"></a>為什麼不想要記錄我的所有端點語句？
若您使用記錄來分析預測，則不會擷取您記錄中的測試語句。

## <a name="data-management"></a>資料管理

### <a name="can-i-delete-data-from-luis"></a>可以從 LUIS 刪除資料嗎？

* 您一律可以刪除用於訓練 LUIS 的範例語句。 若您從 LUIS 應用程式刪除範例語句，則會從 LUIS Web 服務中予以移除，而且無法用於匯出。
* 您可以在 [**審查端點語句**] 頁面中，從 LUIS 建議的使用者語句清單中刪除語句。 從此清單中刪除語句可防止系統建議這些語句，但並不會從記錄中刪除它們。
* 如果您刪除帳戶，系統就會刪除所有應用程式及其範例語句和記錄。 資料會在伺服器上保留 60 天後再永久刪除。

### <a name="how-does-microsoft-manage-data-i-send-to-luis"></a>Microsoft 如何管理我傳送至 LUIS 的資料？

[信任中心](https://www.microsoft.com/trustcenter)會說明我們的承諾，以及您在 Azure 服務中可用的資料管理和存取選項。

## <a name="language-and-translation-support"></a>語言和翻譯支援

### <a name="i-have-an-app-in-one-language-and-want-to-create-a-parallel-app-in-another-language-what-is-the-easiest-way-to-do-so"></a>我有某種語言的應用程式，且想要建立另一種語言的平行應用程式。 若要這麼做的最簡單方式為何？
1. 匯出應用程式。
2. 將已匯出應用程式之 JSON 檔案中加上標籤的語句翻譯為目標語言。
3. 您可能需要變更意圖和實體名稱，或將它們保持不變。
4. 最後，匯入應用程式，以具有目標語言的 LUIS 應用程式。

## <a name="app-notification"></a>應用程式通知

### <a name="why-did-i-get-an-email-saying-im-almost-out-of-quota"></a>為什麼收到指出幾乎超出配額的電子郵件？
撰寫/起始金鑰每個月只允許 1000 次端點查詢。 建立 LUIS 端點金鑰 (免費或付費)，並在查詢端點時使用該金鑰。 若您要從 Bot 或另一個用戶端應用程式查詢端點，則需要在該處變更 LUIS 端點金鑰。

## <a name="bots"></a>Bot

### <a name="my-luis-bot-isnt-working-what-do-i-do"></a>我的 LUIS Bot 無法運作。 該怎麼辦？

第一個問題是找出問題是否與 LUIS 有關，或發生在 LUIS 中介軟體之外。

#### <a name="resolve-issue-in-luis"></a>解決 LUIS 中的問題
從 [LUIS 端點](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) 傳遞相同的語句至 LUIS。 如果您收到錯誤，請解決 LUIS 中的問題，直到錯誤不再傳回。 常見錯誤包括：

* `Out of call volume quota. Quota will be replenished in <time>.` - 此問題表示您須從撰寫金鑰變更為[端點金鑰](luis-how-to-azure-subscription.md)，或者您必須變更[服務層級](luis-how-to-azure-subscription.md#change-pricing-tier)。

#### <a name="resolve-issue-in-azure-bot-service"></a>解決 Azure Bot Service 中的問題

如果您使用的是 Azure Bot Service，且問題是**網路聊天中的測試**傳回 `Sorry, my bot code is having an issue`，請檢查您的記錄：

1. 在 Azure 入口網站中，針對您的 Bot，從 [Bot 管理]**** 區段中選取 [建置]****。
1. 開啟線上程式碼編輯器。
1. 在最上層的藍色導覽列中，選取 Bot 名稱 (右側的第二個項目)。
1. 在產生的下拉式清單中，選取 [開啟 Kudu 主控台]****。
1. 選取 [記錄檔]****，然後選取 [應用程式]****。 檢閱所有記錄檔。 如果您在應用程式資料夾中沒有看到錯誤，請檢閱 [記錄檔]****。
1. 如果您正在使用已編譯的語言，例如 C#，請記得重建您的專案。

> [!Tip]
> 主控台也會安裝封裝。

#### <a name="resolve-issue-while-debugging-on-local-machine-with-bot-framework"></a>使用 Bot Framework 在本機電腦上偵錯時解決問題。

若要深入了解的本機對 Bot 進行偵錯，請參閱[對 Bot 進行偵錯](https://docs.microsoft.com/azure/bot-service/bot-service-debug-bot?view=azure-bot-service-4.0)。

## <a name="integrating-luis"></a>整合 LUIS

### <a name="where-is-my-luis-app-created-during-the-azure-web-app-bot-subscription-process"></a>在 Azure Web 應用程式 Bot 訂用帳戶程序期間，於何處建立 LUIS 應用程式？
若您選取 LUIS 範本，然後在範本窗格中選取 [選取]**** 按鈕，則左側窗格會變更成包括範本類型，並詢問在哪個區域建立 LUIS 範本。 Web 應用程式 Bot 程序並不會建立 LUIS 訂用帳戶。

![LUIS 範本 Web 應用程式 Bot 區域](./media/luis-faq/web-app-bot-location.png)

### <a name="what-luis-regions-support-bot-framework-speech-priming"></a>哪些 LUIS 區域支援 Bot Framework 語音預備？
只有在美國中部執行個體中才支援 LUIS 應用程式的[語音預備](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming)。

## <a name="api-programming-strategies"></a>API 程式設計策略

### <a name="how-do-i-programmatically-get-the-luis-region-of-a-resource"></a>如何以程式設計方式取得資源的 LUIS 區域？

使用 LUIS 範例以程式設計方式[尋找區域](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/find-region) (使用 C# 或 Node.Js)。

## <a name="luis-service"></a>LUIS 服務

### <a name="is-language-understanding-luis-available-on-premises-or-in-private-cloud"></a>Language Understanding (LUIS) 可以在內部部署或私人雲端中使用嗎？

是，如果您必須測量連線使用量，可以在這些情況下，使用 LUIS [容器](luis-container-howto.md)。

## <a name="migrating-to-the-next-version"></a>遷移至下一個版本

### <a name="how-do-i-migrate-to-preview-v3-api"></a>如何? 遷移至預覽 V3 API 嗎？

請參閱[API v2 至 v3 的 LUIS 應用程式遷移指南](luis-migration-api-v3.md)

## <a name="build-2019-conference-announcements"></a>組建2019會議公告

組建2019會議已發行下列功能：

* [V3 API 遷移指南的預覽](luis-migration-api-v3.md)
* [改良的分析儀表板](luis-how-to-use-dashboard.md)
* [改良的預建網域](luis-reference-prebuilt-domains.md)
* [動態清單實體](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [外部實體](luis-migration-api-v3.md#external-entities-passed-in-at-prediction-time)

影片：

* [如何使用 Azure 對話式 AI 來調整您的企業以進行下一代](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="next-steps"></a>後續步驟

若要深入了解 LUIS，請參閱下列資源：
* [Stack Overflow questions tagged with LUIS](https://stackoverflow.com/questions/tagged/luis) (使用 LUIS 加上標籤的 Stack Overflow 問題)
* [Microsoft Q&MSDN Language Understanding 智慧型服務（LUIS）的問題頁面](https://docs.microsoft.com/answers/topics/azure-language-understanding.html)
