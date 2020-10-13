---
title: 遷移至 Azure 資源撰寫金鑰
titleSuffix: Azure Cognitive Services
description: 本文說明如何從電子郵件帳戶將 Language Understanding (LUIS) 撰寫驗證至 Azure 資源。
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 08/13/2020
ms.openlocfilehash: 926b79e672c14249ec7c2b053dba7eb3a31443a7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91536033"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>遷移至 Azure 資源撰寫金鑰

Language Understanding (LUIS) 撰寫驗證從電子郵件帳戶變更為 Azure 資源。 雖然目前不需要，但在未來將會強制切換至 Azure 資源。


## <a name="what-is-migration"></a>什麼是遷移？

遷移是從電子郵件帳戶將撰寫驗證變更為 Azure 資源的程式。 遷移之後，您的帳戶將會連結至 Azure 訂用帳戶和 Azure 撰寫資源。 * (擁有者或共同作業者) 的所有 LUIS 使用者最終都必須遷移。*

您必須從 LUIS 入口網站進行遷移。 例如，如果您使用 LUIS CLI 建立撰寫金鑰，您將需要在 LUIS 入口網站中完成遷移程式。 在遷移之後，您仍然可以在應用程式上 greenfield，但這些會新增至 Azure 資源層級，而不是應用層級。

> [!Note]
> 在遷移之前，greenfield 在 LUIS 應用層 _級上稱為_ 共同作業者。 遷移之後，會在 Azure 資源層級上使用 _參與者_ 的 azure 角色來取得相同的功能。

## <a name="note-before-you-migrate"></a>在遷移之前注意

* 您必須在 **11 月 2 2020 日**之前遷移您的撰寫體驗。 
* 遷移是單向流程。 遷移之後，您就無法返回。
* 如果您是應用程式的擁有者，應用程式會自動與您一起遷移。
* 擁有者無法選擇要遷移的應用程式子集，而且程式無法還原。
* 在擁有者遷移之後，應用程式會從共同作業者的端消失。
* 系統會提示擁有者傳送電子郵件給共同作業者，以通知他們遷移。
* 如果您是應用程式的共同作業者，應用程式將不會與您一起遷移。
* 沒有任何方法可讓擁有者知道共同作業者已遷移。
* 「遷移」不會自動收集共同作業者，也不會移動或將它們新增至 Azure 撰寫資源。 應用程式擁有者是在遷移之後需要完成此步驟的人員。 此步驟需要 [Azure 撰寫資源的許可權](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-collaborate)。
* 將共同作業者指派給 Azure 資源之後，他們需要遷移以存取應用程式。 否則，他們將無法存取來撰寫應用程式。
* 已遷移的使用者無法新增為應用程式的共同作業者。
* 如果您擁有指派給另一位使用者所擁有之應用程式的預測金鑰，這將會封鎖擁有者和共同作業者的遷移。 請參閱本文稍後的建議。

> [!Note]
> 如果您需要建立預測執行時間資源，則可以 [使用個別](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) 的程式來建立它。

## <a name="migration-prerequisites"></a>移轉必要條件

* 您必須與有效的 Azure 訂用帳戶相關聯。 要求您的租使用者系統管理員將您新增至訂用帳戶，或 [註冊免費](https://azure.microsoft.com/free/cognitive-services)帳戶。
* 您必須從 LUIS 入口網站或從 Azure 入口網站建立 LUIS Azure 撰寫資源。 從 LUIS 入口網站建立撰寫資源是下一節所討論之遷移流程的一部分。
* 如果您是應用程式的共同作業者，應用程式將不會自動遷移。 建議您將這些應用程式匯出或使用 [匯出 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)來加以備份。 您可以在遷移後將應用程式匯入回 LUIS。 匯入程式會使用新的應用程式識別碼建立新的應用程式，您是擁有者。
* 如果您是應用程式的擁有者，則不需要匯出您的應用程式，因為它們會自動遷移。 建議您儲存每個應用程式的共同作業者清單。 包含此清單的電子郵件範本，會在遷移過程中選擇性地提供。


|入口網站|目的|
|--|--|
|[Azure](https://azure.microsoft.com/free/cognitive-services)| 建立預測和撰寫資源。<br> 指派資源的參與者。|
|[LUIS](https://www.luis.ai)| 遷移至新的撰寫資源。<br> 在遷移流程中建立新的撰寫資源。<br> 從 [**管理**  >  **Azure 資源**] 頁面指派或取消指派預測和撰寫資源至應用程式。 <br> 將應用程式從一個撰寫資源移至另一個。  |

> [!Note]
> 撰寫您的 LUIS 應用程式是免費的，如同 F0 層所表示。 深入瞭解 [定價層](luis-limits.md#key-limits)。


## <a name="migration-steps"></a>移轉步驟

1. 在您正在處理的 LUIS 入口網站中，您可以從頂端工具列上的 **Azure** 圖示開始遷移程式。

   > [!div class="mx-imgBorder"]
   > ![遷移圖示](./media/migrate-authoring-key/migration-button.png)

2. [遷移] 快顯視窗可讓您繼續進行遷移，或稍後再遷移。 選取 [ **立即遷移**]。

   > [!div class="mx-imgBorder"]
   > ![遷移程式中的第一個快顯視窗，您可以在其中選取立即遷移](./media/migrate-authoring-key/prompt-when-migrating-2.png)

3. 如果有任何應用程式具有共同作業者，系統會提示您傳送電子郵件給他們，讓他們知道遷移的相關資訊。 這是選擇性步驟。

   針對每個共同作業者和應用程式，預設電子郵件應用程式會以輕量格式的電子郵件開啟。 您可以在傳送電子郵件之前進行編輯。 電子郵件範本包含確切的應用程式識別碼和應用程式名稱。

   ```html
   Dear Sir/Madam,

   I will be migrating my LUIS account to Azure. Consequently, you will no longer have access to the following app:

   App Id: <app-ID-omitted>
   App name: Human Resources

   Thank you
   ```

   > [!Note]
   > 將您的帳戶遷移至 Azure 之後，共同作業者將無法再使用您的應用程式。

4. 如果您是任何應用程式上的共同作業者，系統會在遷移流程中選取此選項，提示您匯出應用程式的複本。 這是選擇性步驟。

   如果您選取此選項，則會出現下列頁面。 選取左側的 [下載] 按鈕，匯出您要的應用程式。 您可以在遷移後將這些應用程式匯回，因為它們不會與您自動遷移。

   > [!div class="mx-imgBorder"]
   > ![提示匯出您的應用程式。](./media/migrate-authoring-key/export-app-for-collabs-2.png)

5. 如果您已經從 Azure 建立新的 LUIS 撰寫資源或遷移至現有的撰寫資源，您可以選擇建立該資源。 選取下列其中一個按鈕，選擇您想要的選項。

   > [!div class="mx-imgBorder"]
   > ![用來建立新撰寫資源和使用現有撰寫資源的按鈕](./media/migrate-authoring-key/choose-existing-authoring-resource.png)

### <a name="create-new-authoring-resource-from-luis-to-migrate"></a>從 LUIS 建立新的撰寫資源以進行遷移

如果您想要建立新的撰寫資源，請選取 [ **建立新的撰寫資源** ]，然後在下一個視窗中提供下列資訊。 然後選取 [完成]****。

> [!div class="mx-imgBorder"]
> ![建立撰寫資源的視窗](./media/migrate-authoring-key/create-new-authoring-resource-2.png)

* **租使用者名稱**：與您的 Azure 訂用帳戶相關聯的租使用者。 預設會將此設定為您目前使用的租使用者。 您可以選取最右邊的圖片（包含您的姓名縮寫）來切換租使用者。
* **資源名稱**：您選擇的自訂名稱。 它是用來作為撰寫和預測端點查詢 URL 的一部分。
* **訂**用帳戶名稱：將與資源相關聯的訂用帳戶。 如果您有多個屬於您租使用者的訂用帳戶，請從下拉式清單中選取您想要的訂用帳戶。
* **Azure 資源組名**：您從下拉式清單中選擇的自訂資源組名。 資源群組可讓您將 Azure 資源分組以方便存取和管理。

請注意，每個訂用帳戶在每個區域可以有10個免費的撰寫資源。 如果您的訂用帳戶在相同區域中有10個以上的撰寫資源，您將無法建立新的資源。

建立撰寫資源時，會顯示成功訊息。 選取 [ **關閉** ] 以關閉快顯視窗。

  > [!div class="mx-imgBorder"]
  > ![表示已成功建立撰寫資源的訊息](./media/migrate-authoring-key/migration-success-2.png)


### <a name="use-existing-authoring-resource-to-migrate"></a>使用現有的撰寫資源進行遷移

如果您的訂用帳戶已經與 LUIS 撰寫 Azure 資源相關聯，或您已從 Azure 入口網站建立資源，而您想要遷移至該資源，而不是建立新資源，請選取 [ **使用現有的撰寫資源**]。 在下一個視窗中提供下列資訊，然後選取 [ **完成**]。

> [!div class="mx-imgBorder"]
>![變更現有撰寫資源的視窗](./media/migrate-authoring-key/choose-existing-authoring-resource-2.png)

* **租使用者名稱**：與您的 Azure 訂用帳戶相關聯的租使用者。 預設會將此設定為您目前使用的租使用者。
* **訂**用帳戶名稱：將與資源相關聯的訂用帳戶。 如果您有多個屬於您租使用者的訂用帳戶，請從下拉式清單中選取您想要的訂用帳戶。
* **資源名稱**：您想要遷移的撰寫資源。

> [!Note]
> 如果您在下拉式清單中看不到您的撰寫資源，請確定您已根據已登入的 LUIS 入口網站，在適當的位置中建立它。 此外，請確定您建立的是撰寫資源，而不是預測資源。


驗證您的撰寫資源名稱，然後選取 [ **遷移** ] 按鈕。

> [!div class="mx-imgBorder"]
> ![選擇撰寫資源的視窗，現在可使用 [遷移] 按鈕](./media/migrate-authoring-key/choose-authoring-resource-and-migrate-2.png)

成功訊息隨即出現。 選取 [ **關閉** ] 以關閉快顯視窗。

> [!div class="mx-imgBorder"]
> ![指出您的撰寫資源已成功遷移的訊息](./media/migrate-authoring-key/migration-success-2.png)

## <a name="using-apps-after-migration"></a>在遷移後使用應用程式

在遷移程式之後，您目前擁有者的所有 LUIS 應用程式，現在都會指派給單一 LUIS 撰寫資源。

**我的應用程式**清單會顯示遷移至新撰寫資源的應用程式。 存取應用程式之前，請先選取訂用帳戶和 LUIS 撰寫資源，以查看您可以撰寫的應用程式。

 > [!div class="mx-imgBorder"]
 > ![訂用帳戶和製作資源的方塊](./media/create-app-in-portal-select-subscription-luis-resource.png)

您不需要知道撰寫資源的金鑰，即可繼續在 LUIS 入口網站中編輯您的應用程式。

如果您想要以程式設計方式編輯您的應用程式，則需要撰寫索引鍵值。 這些值會顯示在 LUIS 入口網站中的 [**管理**  >  **Azure 資源**] 頁面上。 您也可以在資源的 [ **金鑰** ] 頁面上取得 Azure 入口網站。 您也可以建立更多撰寫資源，並從相同的頁面指派。

 > [!div class="mx-imgBorder"]
 > ![管理撰寫資源的頁面](./media/migrate-authoring-key/manage-authoring-resource-2.png)

## <a name="adding-contributors-to-authoring-resources"></a>新增參與者以撰寫資源

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

瞭解如何在您的撰寫資源上 [新增參與者](luis-how-to-collaborate.md) 。 參與者將可存取該資源下的所有應用程式。

您可以在該資源的 **存取控制 (IAM) ** 頁面上，從 Azure 入口網站將參與者新增至撰寫資源。 如需詳細資訊，請參閱 [新增參與者存取權](luis-migration-authoring-steps.md#after-the-migration-process-add-contributors-to-your-authoring-resource)。

> [!Note]
> 如果已遷移 LUIS 應用程式的擁有者，並將共同作業者新增為 Azure 資源的參與者，則共同作業者仍無法存取該應用程式，除非他們也遷移。

## <a name="luis-portal-migration-reminders"></a>LUIS 入口網站遷移提醒

[LUIS 入口網站](https://www.luis.ai)會提供遷移程式。

如果這兩個條件都成立，系統會要求您進行遷移：
* 您在電子郵件驗證系統上有用於撰寫的應用程式。
* 您是應用程式擁有者。

每週都會提示您遷移您的應用程式。 您可以關閉此視窗而不進行遷移。 如果您想要在下一個排程的期間之前遷移，您可以從 LUIS 入口網站頂端工具列上的 **Azure** 圖示開始遷移程式。

## <a name="prediction-resources-blocking-migration"></a>封鎖遷移的預測資源
您的遷移會對任何應用程式的執行時間造成負面影響。 當您遷移時，會從您的應用程式中移除任何共同作業者，並從其他應用程式移除為共同作業者。 此程式表示也會移除共同作業者指派的金鑰，如果您的應用程式在生產環境中，這可能會中斷您的應用程式。 這是我們封鎖遷移的原因，除非您手動移除指派給他們的共同作業者或金鑰。

如果下列任一條件成立，則會封鎖遷移：

* 您已在您不擁有的應用程式中指派預測/執行時間資源。
* 您有其他使用者將預測/執行時間資源指派給您所擁有的應用程式。

### <a name="recommended-steps-if-youre-the-owner-of-the-app"></a>如果您是應用程式的擁有者，建議的步驟
如果您是某些應用程式的擁有者，而且您有共同作業者將預測/執行時間金鑰指派給這些應用程式，則在遷移時會出現錯誤。 此錯誤會列出已指派預測金鑰給其他使用者擁有的應用程式識別碼。

建議您：
* 通知共同作業者有關遷移。
* 從錯誤所顯示的應用程式中移除所有共同作業者。
* 進行遷移程式，如果您手動移除共同作業者，這應該會成功。
* 將共同作業者指派為新撰寫資源的參與者。 共同作業者將會遷移預測資源，然後重新指派給應用程式。 請注意，這會暫時導致應用程式中斷，直到重新指派預測資源為止。

這裡還有另一個可能的解決方案。 在擁有者遷移之前，共同作業者可以從 Azure 入口網站將應用程式擁有者新增為其 Azure 訂用帳戶的參與者。 此步驟會將執行時間預測資源的存取權授與擁有者。 如果擁有者使用新的訂用帳戶進行遷移， (這些訂用帳戶會在新的租使用者) 下找到，此步驟並不只會解除封鎖共同作業者和應用程式擁有者的遷移程式。 它也可讓您順暢地遷移應用程式，但仍會將預測金鑰指派給他們，而不會中斷應用程式。


### <a name="recommended-steps-if-youre-a-collaborator-on-an-app"></a>如果您是應用程式上的共同作業者，建議的步驟
如果您要在應用程式上進行共同作業，並將預測/執行時間索引鍵指派給這些應用程式，則在遷移時會出現錯誤。 此錯誤會列出封鎖遷移的應用程式識別碼和金鑰路徑。

建議您：
* 將應用程式匯出為備份。 這是遷移過程中的選擇性步驟。
* 從 [**管理**  >  **Azure 資源**] 頁面取消指派預測資源。
* 經歷了遷移過程。
* 在遷移之後，匯入應用程式。
* 從 [**管理**  >  **Azure 資源**] 頁面將預測金鑰重新指派給您的應用程式。

> [!Note]
> 當您在遷移後將應用程式匯入後，它們會有不同的應用程式識別碼。 它們也會與在生產環境中點擊的不同。 您現在會是這些應用程式的擁有者。

## <a name="troubleshooting-the-migration-process"></a>針對遷移程式進行疑難排解

當您嘗試遷移但在下拉式清單中找不到您的 Azure 訂用帳戶時：
* 確定您有已獲授權可建立認知服務資源的有效 Azure 訂用帳戶。 移至 [Azure 入口網站](https://ms.portal.azure.com) 並檢查訂用帳戶的狀態。 如果您沒有帳戶，請 [建立一個免費的 Azure 帳戶](https://azure.microsoft.com/free/cognitive-services/)。
* 確定您是在與有效訂用帳戶相關聯的正確租使用者中。 您可以在此工具列上，將租使用者從您姓名縮寫左邊的位置切換至您 ![ 可以切換租使用者的工具列：](./media/migrate-authoring-key/switch-user-tenant-2.png)

如果您有現有的撰寫資源，但在選取 [ **使用現有的撰寫資源** ] 選項時找不到它：
* 您的資源可能建立所在的位置與您登入的入口網站不同。 檢查 [LUIS 撰寫區域和入口網站](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-authoring-regions)。
* 改為從 LUIS 入口網站建立新資源。

如果您選取 [ **建立新的撰寫資源** ] 選項，而遷移失敗，並出現錯誤訊息「無法抓取使用者的 Azure 資訊，請稍後再試一次」：
* 針對每個訂用帳戶，您的訂用帳戶可能會有10個以上的撰寫資源。 如果是這種情況，您將無法建立新的撰寫資源。
* 選取 [ **使用現有的撰寫資源** ] 選項，然後選取您訂用帳戶下的其中一個現有資源來進行遷移。

如果您看到下列錯誤，請檢查 [建議的步驟（如果您是應用程式的擁有](#recommended-steps-if-youre-the-owner-of-the-app)者）。
![顯示擁有者的遷移失敗的錯誤](./media/migrate-authoring-key/migration-failed-for-owner-2.png)

如果您看到下列錯誤，請檢查 [建議的步驟（如果您是應用程式的](#recommended-steps-if-youre-a-collaborator-on-an-app)共同作業者）。
![顯示共同作業者的遷移失敗的錯誤](./media/migrate-authoring-key/migration-failed-for-collab-2.png)


## <a name="next-steps"></a>後續步驟

* 複習 [有關撰寫和執行時間金鑰的概念](luis-how-to-azure-subscription.md)。
* 複習如何 [指派金鑰](luis-how-to-azure-subscription.md) 和 [新增參與者](luis-how-to-collaborate.md)。
