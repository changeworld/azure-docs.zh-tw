---
title: 遷移至 Azure 資源撰寫金鑰
titleSuffix: Azure Cognitive Services
description: 本文說明如何將 Language Understanding （LUIS）撰寫驗證從電子郵件帳戶遷移至 Azure 資源。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 06/17/2020
ms.author: diberry
ms.openlocfilehash: 338868c954df68fbf43bee9581a6afada58f7e97
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86055455"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>遷移至 Azure 資源撰寫金鑰

Language Understanding （LUIS）編寫驗證從電子郵件帳戶變更為 Azure 資源。 雖然目前不需要，但在未來將會強制切換至 Azure 資源。


## <a name="what-is-migration"></a>什麼是遷移？

「遷移」是將撰寫驗證從電子郵件帳戶變更為 Azure 資源的程式。 您的帳戶將會在您遷移之後連結至 Azure 訂用帳戶和 Azure 撰寫資源。 *所有 LUIS 使用者（擁有者或共同作業者）最終都必須進行遷移。*

必須從 LUIS 入口網站進行遷移。 例如，如果您使用 LUIS CLI 建立撰寫金鑰，您將需要在 LUIS 入口網站中完成遷移程式。 您仍然可以在遷移之後 greenfield 應用程式，但這些會新增至 Azure 資源層級，而不是應用層級。

> [!Note]
> 在遷移之前，greenfield 在 LUIS 應用層_級上稱為_「共同作業者」。 遷移之後，「_參與者_」的 azure 角色會用於 azure 資源層級上的相同功能。

## <a name="note-before-you-migrate"></a>請注意，在您遷移之前

* 遷移是單向程式。 您無法在遷移之後返回。
* 如果您是應用程式的擁有者，應用程式會自動與您一起遷移。
* 擁有者無法選擇要遷移的應用程式子集，而且進程也不可逆。
* 在擁有者遷移之後，應用程式將會從共同作業者端消失。
* 系統會提示擁有者傳送電子郵件給共同作業者，以通知他們有遷移。
* 如果您是應用程式的共同作業者，應用程式將不會與您一起遷移。
* 擁有者無法得知共同作業者是否已遷移。
* 遷移並不會自動收集共同作業者，並將其移動或新增至 Azure 撰寫資源。 應用程式擁有者就是在遷移之後需要完成此步驟的人員。 此步驟需要[Azure 撰寫資源的許可權](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-collaborate)。
* 將共同作業者指派給 Azure 資源之後，他們必須遷移以存取應用程式。 否則，他們將無法存取以編寫應用程式。
* 已遷移的使用者無法新增為應用程式的共同作業者。
* 如果您擁有指派給另一位使用者所擁有之應用程式的預測金鑰，這將會封鎖擁有者和共同作業者的遷移。 請參閱本文稍後的建議。

> [!Note]
> 如果您需要建立預測執行時間資源，有[個別](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal)的程式可以建立它。

## <a name="migration-prerequisites"></a>移轉必要條件

* 您必須與有效的 Azure 訂用帳戶相關聯。 請要求您的租使用者系統管理員將您新增到訂用帳戶，或[註冊免費](https://azure.microsoft.com/free/)帳戶。
* 您必須從 LUIS 入口網站或從 Azure 入口網站建立 LUIS Azure 撰寫資源。 從 LUIS 入口網站建立撰寫資源屬於遷移流程的一部分，將在下一節中討論。
* 如果您是應用程式的共同作業者，應用程式不會自動遷移。 我們建議您將這些應用程式匯出或使用[匯出 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)來進行備份。 您可以在遷移之後，將應用程式匯回 LUIS。 匯入程式會使用新的應用程式識別碼來建立新的應用程式，您是擁有者。
* 如果您是應用程式的擁有者，則不需要匯出應用程式，因為它們會自動遷移。 我們建議您儲存每個應用程式的共同作業者清單。 包含此清單的電子郵件範本會在遷移過程中選擇性地提供。


|入口網站|目的|
|--|--|
|[Azure](https://azure.microsoft.com/free/)| 建立預測和撰寫資源。<br> 指派資源的參與者。|
|[LUIS](https://www.luis.ai)| 遷移至新的撰寫資源。<br> 在遷移流程中建立新的撰寫資源。<br> 從 [**管理**  >  **Azure 資源**] 頁面，將預測和撰寫資源指派或取消指派給應用程式。 <br> 將應用程式從一個撰寫資源移到另一個。  |

> [!Note]
> 撰寫 LUIS 應用程式是免費的，如 F0 層所示。 深入瞭解[定價層](luis-limits.md#key-limits)。


## <a name="migration-steps"></a>移轉步驟

1. 在您所使用的 LUIS 入口網站中，您可以從頂端工具列上的**Azure**圖示開始進行遷移程式。

   > [!div class="mx-imgBorder"]
   > ![遷移圖示](./media/migrate-authoring-key/migration-button.png)

2. [遷移] 快顯視窗可讓您在稍後繼續進行遷移或遷移。 選取 [**立即遷移**]。

   > [!div class="mx-imgBorder"]
   > ![在遷移程式中的第一個快顯視窗，您可以在其中選取 [立即遷移]](./media/migrate-authoring-key/prompt-when-migrating-2.png)

3. 如果您的任何應用程式有共同作業者，系統就會提示您傳送電子郵件給他們，讓他們知道遷移的相關資訊。 這是選擇性步驟。

   針對每個共同作業者和應用程式，預設的電子郵件應用程式會以輕量格式的電子郵件開啟。 您可以在傳送電子郵件前先進行編輯。 電子郵件範本包含正確的應用程式識別碼和應用程式名稱。

   ```html
   Dear Sir/Madam,

   I will be migrating my LUIS account to Azure. Consequently, you will no longer have access to the following app:

   App Id: <app-ID-omitted>
   App name: Human Resources

   Thank you
   ```

   > [!Note]
   > 將帳戶遷移至 Azure 之後，共同作業者就無法再使用您的應用程式。

4. 如果您是任何應用程式的共同作業者，系統會提示您在遷移流程期間選取此選項來匯出應用程式的複本。 這是選擇性步驟。

   如果您選取此選項，則會出現下列頁面。 選取左側的 [下載] 按鈕，以匯出您想要的應用程式。 您可以在遷移之後重新匯入這些應用程式，因為它們不會隨您自動遷移。

   > [!div class="mx-imgBorder"]
   > ![提示匯出您的應用程式。](./media/migrate-authoring-key/export-app-for-collabs-2.png)

5. 如果您已從 Azure 建立新的 LUIS 撰寫資源，或將其遷移至現有的撰寫資源，您可以選擇建立它。 選取下列其中一個按鈕，以選擇您想要的選項。

   > [!div class="mx-imgBorder"]
   > ![用來建立新撰寫資源及使用現有撰寫資源的按鈕](./media/migrate-authoring-key/choose-existing-authoring-resource.png)

### <a name="create-new-authoring-resource-from-luis-to-migrate"></a>從 LUIS 建立新的撰寫資源以進行遷移

如果您想要建立新的撰寫資源，請選取 [**建立新的撰寫資源**]，並在下一個視窗中提供下列資訊。 然後選取 [完成]****。

> [!div class="mx-imgBorder"]
> ![建立撰寫資源的視窗](./media/migrate-authoring-key/create-new-authoring-resource-2.png)

* **租使用者名稱**：與您的 Azure 訂用帳戶相關聯的租使用者。 預設會將此設定為您目前正在使用的租使用者。 您可以選取最右邊的頭像（其中包含您的姓名縮寫）來切換租使用者。
* **資源名稱**：您選擇的自訂名稱。 它是用來作為撰寫和預測端點查詢 URL 的一部分。
* **訂**用帳戶名稱：將與資源相關聯的訂用帳戶。 如果您有多個訂用帳戶屬於您的租使用者，請從下拉式清單中選取您想要的訂用帳戶。
* **Azure 資源組名**：您從下拉式清單中選擇的自訂資源組名。 資源群組可讓您將 Azure 資源分組以方便存取和管理。

請注意，每個訂用帳戶的每個區域可以有10個免費的撰寫資源。 如果您的訂用帳戶在相同的區域中有10個以上的撰寫資源，您將無法建立新的資源。

建立撰寫資源時，會顯示成功訊息。 選取 [**關閉**] 以關閉快顯視窗。

  > [!div class="mx-imgBorder"]
  > ![表示已成功建立您的撰寫資源的訊息](./media/migrate-authoring-key/migration-success-2.png)


### <a name="use-existing-authoring-resource-to-migrate"></a>使用現有的撰寫資源進行遷移

如果您的訂用帳戶已與 LUIS authoring Azure 資源相關聯，或您已從 Azure 入口網站建立資源，而您想要將它遷移至它，而不是建立新的，請選取 [**使用現有的撰寫資源**]。 在下一個視窗中提供下列資訊，然後選取 [**完成**]。

> [!div class="mx-imgBorder"]
>![變更現有撰寫資源的視窗](./media/migrate-authoring-key/choose-existing-authoring-resource-2.png)

* **租使用者名稱**：與您的 Azure 訂用帳戶相關聯的租使用者。 預設會將此設定為您目前正在使用的租使用者。
* **訂**用帳戶名稱：將與資源相關聯的訂用帳戶。 如果您有多個訂用帳戶屬於您的租使用者，請從下拉式清單中選取您想要的訂用帳戶。
* **資源名稱**：您要遷移到的撰寫資源。

> [!Note]
> 如果您在下拉式清單中看不到您的撰寫資源，請確定您已根據登入的 LUIS 入口網站，在適當的位置中建立它。 此外，請確定您建立的是撰寫資源，而不是預測資源。


驗證您的撰寫資源名稱，然後選取 [**遷移**] 按鈕。

> [!div class="mx-imgBorder"]
> ![選擇撰寫資源的視窗，現在可以使用 [遷移] 按鈕](./media/migrate-authoring-key/choose-authoring-resource-and-migrate-2.png)

成功訊息隨即出現。 選取 [**關閉**] 以關閉快顯視窗。

> [!div class="mx-imgBorder"]
> ![指出您的撰寫資源已成功遷移的訊息](./media/migrate-authoring-key/migration-success-2.png)

## <a name="using-apps-after-migration"></a>在遷移之後使用應用程式

在遷移程式之後，您為其擁有者的所有 LUIS apps 現在都會指派給單一 LUIS 撰寫資源。

[**我的應用程式**] 清單會顯示遷移至新撰寫資源的應用程式。 存取您的應用程式之前，請先選取訂用帳戶，然後 LUIS 撰寫資源]，以查看您可以撰寫的應用程式。

 > [!div class="mx-imgBorder"]
 > ![訂用帳戶和撰寫資源的方塊](./media/create-app-in-portal-select-subscription-luis-resource.png)

您不需要知道撰寫資源的金鑰，即可在 LUIS 入口網站中繼續編輯您的應用程式。

如果您想要以程式設計方式編輯應用程式，您將需要撰寫金鑰值。 這些值會顯示在 LUIS 入口網站的 [**管理**  >  **Azure 資源**] 頁面上。 您也可以在 [資源**金鑰**] 頁面上的 [Azure 入口網站中找到它們。 您也可以建立更多撰寫資源，並從相同的頁面加以指派。

 > [!div class="mx-imgBorder"]
 > ![管理撰寫資源的頁面](./media/migrate-authoring-key/manage-authoring-resource-2.png)

## <a name="adding-contributors-to-authoring-resources"></a>新增參與者以撰寫資源

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

瞭解如何在您的撰寫資源上[新增參與者](luis-how-to-collaborate.md)。 參與者將可存取該資源下的所有應用程式。

您可以在該資源的 [**存取控制（IAM）** ] 頁面上，從 Azure 入口網站將參與者新增至撰寫資源。 如需詳細資訊，請參閱[新增參與者存取](luis-migration-authoring-steps.md#after-the-migration-process-add-contributors-to-your-authoring-resource)。

> [!Note]
> 如果 LUIS 應用程式的擁有者遷移並將共同作業者新增為 Azure 資源的參與者，則共同作業者仍無法存取應用程式，除非他們也進行遷移。

## <a name="luis-portal-migration-reminders"></a>LUIS 入口網站遷移提醒

[LUIS 入口網站](https://www.luis.ai)提供了遷移程式。

如果這兩個條件都成立，系統會要求您進行遷移：
* 您在電子郵件驗證系統上有用於撰寫的應用程式。
* 您是應用程式擁有者。

每週會提示您遷移應用程式。 您可以關閉此視窗而不進行遷移。 如果您想要在下一個排定的期間之前遷移，可以從 LUIS 入口網站頂端工具列上的**Azure**圖示開始進行遷移程式。

## <a name="prediction-resources-blocking-migration"></a>封鎖遷移的預測資源
您的遷移會對任何應用程式的執行時間造成負面影響。 當您遷移時，所有共同作業者都會從您的應用程式中移除，而您會被移除為其他應用程式的共同作業者。 此程式表示，共同作業者指派的金鑰也會一併移除，如果應用程式在生產環境中，可能會中斷您的應用程式。 這就是我們封鎖遷移的原因，直到您手動移除指派給他們的共同作業者或金鑰為止。

如果上述任一條件成立，則會封鎖遷移：

* 您已在您不擁有的應用程式中指派預測/執行時間資源。
* 您有其他使用者將預測/執行時間資源指派給您擁有的應用程式。

### <a name="recommended-steps-if-youre-the-owner-of-the-app"></a>如果您是應用程式的擁有者，建議的步驟
如果您是某些應用程式的擁有者，而且您有共同作業者將預測/執行時間索引鍵指派給這些應用程式，則當您遷移時，會出現錯誤。 此錯誤會列出已指派預測金鑰給其他使用者擁有的應用程式識別碼。

建議您：
* 通知共同作業者關於遷移。
* 從錯誤所顯示的應用程式中移除所有的共同作業者。
* 進行遷移程式，如果您手動移除共同作業者，這應該會成功。
* 將共同作業者指派為新撰寫資源的參與者。 共同作業者會將預測資源遷移並重新指派回應用程式。 請注意，這會暫時導致應用程式中斷，直到重新指派預測資源為止。

這裡有另一個可行的解決方案。 在擁有者遷移之前，共同作業者可以從 Azure 入口網站將應用程式擁有者新增為其 Azure 訂用帳戶的參與者。 此步驟會將「擁有者」存取權授與執行時間預測資源。 如果擁有者使用已新增至的新訂用帳戶（將在新的租使用者中找到）來進行遷移，此步驟不會只是針對共同作業者和應用程式擁有者解除封鎖遷移程式。 它也可以讓應用程式順暢地進行遷移，但仍會指派預測金鑰給它們，而不會中斷應用程式。


### <a name="recommended-steps-if-youre-a-collaborator-on-an-app"></a>如果您是應用程式的共同作業者的建議步驟
如果您要共同作業應用程式，而且已將預測/執行時間索引鍵指派給這些應用程式，則當您遷移時，會出現錯誤。 此錯誤會列出封鎖遷移的應用程式識別碼和金鑰路徑。

建議您：
* 將應用程式匯出為備份。 這是在遷移過程中的選擇性步驟。
* 從 [**管理**  >  **Azure 資源**] 頁面取消指派預測資源。
* 經歷了遷移程式。
* 在遷移後重新匯入應用程式。
* 從 [**管理**  >  **Azure 資源**] 頁面，將預測金鑰重新指派給您的應用程式。

> [!Note]
> 當您在遷移後重新匯入應用程式時，它們會有不同的應用程式識別碼。 它們也會與在生產環境中遇到的不同。 您現在將是這些應用程式的擁有者。

## <a name="troubleshooting-the-migration-process"></a>疑難排解遷移程式

當您嘗試在下拉式清單中遷移但找不到您的 Azure 訂用帳戶時：
* 請確定您有有效的 Azure 訂用帳戶，且已獲授權可建立認知服務資源。 移至 [ [Azure 入口網站](https://ms.portal.azure.com)並檢查訂用帳戶的狀態。 如果您沒有帳戶，請[建立免費試用](https://azure.microsoft.com/free/)。
* 請確定您是在與有效訂用帳戶相關聯的適當租使用者中。 您可以在這個工具列上，將租使用者從頭像切換到您的縮寫左邊：可以 ![ 切換租使用者的工具列](./media/migrate-authoring-key/switch-user-tenant-2.png)

如果您有現有的撰寫資源，但在選取 [**使用現有的撰寫資源**] 選項時找不到它：
* 您的資源可能建立所在的位置與您登入的入口網站不同。 檢查[LUIS 撰寫區域和入口網站](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-authoring-regions)。
* 改為從 LUIS 入口網站建立新的資源。

如果您選取 [**建立新的撰寫資源**] 選項，而且遷移失敗並出現錯誤訊息「無法抓取使用者的 Azure 資訊，請稍後再試一次」：
* 針對每個訂用帳戶，您的訂用帳戶可能會有10個以上的撰寫資源。 如果是這種情況，您將無法建立新的撰寫資源。
* 選取 [**使用現有的撰寫資源**] 選項，然後選取您訂用帳戶下的其中一個現有資源來進行遷移。

如果您看到下列錯誤，請檢查[建議的步驟（如果您是應用程式的擁有](#recommended-steps-if-youre-the-owner-of-the-app)者）。
![為擁有者顯示遷移失敗的錯誤](./media/migrate-authoring-key/migration-failed-for-owner-2.png)

如果您看到下列錯誤，[如果您是應用程式的](#recommended-steps-if-youre-a-collaborator-on-an-app)共同作業者，請檢查建議的步驟。
![針對共同作業者顯示遷移失敗的錯誤](./media/migrate-authoring-key/migration-failed-for-collab-2.png)


## <a name="next-steps"></a>後續步驟

* 請參閱[撰寫和執行時間金鑰的相關概念](luis-how-to-azure-subscription.md)。
* 回顧如何[指派金鑰](luis-how-to-azure-subscription.md)和[加入參與者](luis-how-to-collaborate.md)。
