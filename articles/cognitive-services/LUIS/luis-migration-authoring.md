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
ms.date: 12/07/2020
ms.openlocfilehash: 243c9834aa256e26d620c00ac0fa7a262919aabd
ms.sourcegitcommit: d6e92295e1f161a547da33999ad66c94cf334563
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2020
ms.locfileid: "96762629"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>遷移至 Azure 資源撰寫金鑰

> [!IMPORTANT]
>  自12月3日起，現有的 LUIS 使用者必須完成遷移程式，才能繼續撰寫 LUIS 應用程式。

Language Understanding (LUIS) 撰寫驗證已從電子郵件帳戶變更為 Azure 資源。 您可以使用本文來瞭解如何遷移您的帳戶（如果您還沒有遷移）。  


## <a name="what-is-migration"></a>什麼是遷移？

遷移是從電子郵件帳戶將撰寫驗證變更為 Azure 資源的程式。 遷移之後，您的帳戶將會連結至 Azure 訂用帳戶和 Azure 撰寫資源。 *所有 LUIS 使用者 (應用程式擁有者和共同作業者) 最後都需要遷移。*

您必須從 [LUIS 入口網站](https://www.luis.ai)進行遷移。 例如，如果您使用 LUIS CLI 建立撰寫金鑰，您將需要在 LUIS 入口網站中完成遷移程式。 在遷移後，您仍然可以在應用程式上擁有共同作者，但這些會新增至 Azure 資源層級，而不是在應用層級上。

> [!Note]
> 在遷移之前，greenfield 在 LUIS 應用層 _級上稱為_ 共同作業者。 遷移之後，會在 Azure 資源層級上使用 _參與者_ 的 azure 角色來取得相同的功能。

## <a name="notes-before-you-migrate"></a>遷移之前的注意事項

* 無法反轉遷移。
* 如果您已登入多個 [LUIS 區域入口網站](./luis-reference-regions.md#luis-authoring-regions)，系統會要求您在多個區域中一次遷移。
* 如果您是應用程式的擁有者，應用程式會自動與您一起遷移。
* 擁有者無法選擇要遷移的應用程式子集，而且程式無法還原。
* 在擁有者遷移之後，應用程式將會從共同作業者的帳戶中消失。
* 系統會提示擁有者傳送電子郵件給共同作業者，以通知他們遷移。
* 如果您是應用程式的共同作業者，應用程式將不會與您一起遷移。 不過，系統會提示共同作業者匯出所需的應用程式。
* 沒有任何方法可讓擁有者知道共同作業者是否已遷移。
* 遷移不會自動將共同作業者移至 Azure 撰寫資源或將其新增至 Azure 撰寫資源。 應用程式擁有者是在遷移之後需要完成此步驟的人員。 此步驟需要 [Azure 撰寫資源的許可權](./luis-how-to-collaborate.md)。
* 將共同作業者指派給 Azure 資源之後，他們必須先遷移，才能存取應用程式。 否則，他們將不會有撰寫應用程式的存取權。
* 已遷移的使用者無法新增為應用程式的共同作業者。


> [!Note]
> 如果您需要建立預測執行時間資源，則可以 [使用個別](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) 的程式來建立它。

## <a name="migration-prerequisites"></a>移轉必要條件

* 您必須與有效的 Azure 訂用帳戶相關聯。 要求您的租使用者系統管理員將您新增至訂用帳戶，或 [註冊免費](https://azure.microsoft.com/free/cognitive-services)帳戶。
* 您必須從 LUIS 入口網站或從 [Azure 入口網站](https://portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)建立 LUIS Azure 撰寫資源。 從 LUIS 入口網站建立撰寫資源是下一節所述之遷移程式的一部分。
* 如果您是應用程式的共同作業者，應用程式將不會自動遷移。 當您進行遷移流程時，系統會提示您匯出這些應用程式。 您也可以使用 [匯出 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)。 您可以在遷移後將應用程式匯入回 LUIS。 匯入程式會使用新的應用程式識別碼建立新的應用程式，您是擁有者。
* 如果您是應用程式的擁有者，則不需要匯出您的應用程式，因為它們會自動遷移。 系統會提供包含每個應用程式之所有共同作業者清單的電子郵件範本，讓他們可以收到遷移程式的通知。

> [!Note]
> 撰寫您的 LUIS 應用程式是免費的，如同 F0 層所表示。 深入瞭解 [定價層](luis-limits.md#key-limits)。


## <a name="migration-steps"></a>移轉步驟

1. 當您登入 [LUIS 入口網站](https://www.luis.ai)時，Azure 遷移視窗將會開啟，並包含遷移步驟。 如果您關閉它，將無法繼續撰寫您的 LUIS 應用程式，而且所顯示的唯一動作將是繼續進行遷移。

    > [!div class="mx-imgBorder"]
    > ![遷移視窗簡介](./media/migrate-authoring-key/notify-azure-migration.png)

2. 如果您在任何應用程式上都有共同作業者，則會顯示您所擁有的應用程式名稱清單，以及每個應用程式上的撰寫區域和共同作業者電子郵件。 建議您將電子郵件傳送給您的共同作業者，方法是按一下應用程式名稱左邊的 [ **傳送** 符號] 按鈕，通知他們有關于遷移的電子郵件。
如果共同作業者 `*` 有指派給您應用程式的預測資源，則符號會出現在應用程式名稱的旁邊。 在遷移之後，這些應用程式仍會將這些預測資源指派給它們，即使共同作業者將無法存取來撰寫您的應用程式也一樣。 不過，如果預測資源的擁有者從 Azure 入口網站重新產生 [金鑰](./luis-how-to-azure-subscription.md#regenerate-an-azure-key) ，則此指派會中斷。  

   > [!div class="mx-imgBorder"]
   > ![通知共同作業者](./media/migrate-authoring-key/notify-azure-migration-collabs.png)


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

3. 如果您是任何應用程式上的共同作業者，則會在每個應用程式上顯示與您共用的應用程式名稱清單以及撰寫區域和擁有者電子郵件。 建議您按一下應用程式名稱左邊的 [匯出] 按鈕，匯出應用程式的複本。 您可以在遷移後將這些應用程式匯回，因為它們不會與您自動遷移。
`*`如果您已將預測資源指派給應用程式，則符號會出現在應用程式名稱的旁邊。 在遷移後，即使您不再有權撰寫這些應用程式，您的預測資源仍會指派給這些應用程式。 如果您想要中斷預測資源與應用程式之間的指派，您必須移至 Azure 入口網站並 [重新產生金鑰](./luis-how-to-azure-subscription.md#regenerate-an-azure-key)。

   > [!div class="mx-imgBorder"]
   > ![匯出您的應用程式。](./media/migrate-authoring-key/migration-export-apps.png)


4. 在遷移區域的視窗中，系統會要求您將應用程式遷移至其所撰寫之相同區域中的 Azure 資源。 LUIS 有三個撰寫區域 [和入口網站](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-authoring-regions)。 此視窗會顯示您所擁有之應用程式的撰寫區域。 根據您所使用的區域入口網站和您所撰寫的應用程式而定，顯示的遷移區域可能會不同。 

   > [!div class="mx-imgBorder"]
   > ![多區域遷移。](./media/migrate-authoring-key/migration-regional-flow.png)

5. 針對每個區域，選擇建立新的 LUIS 撰寫資源，或使用按鈕遷移至現有的資源。

   > [!div class="mx-imgBorder"]
   > ![選擇建立或現有的撰寫資源](./media/migrate-authoring-key/migration-multiregional-resource.png)

   請提供下列資訊：

   * **租使用者名稱**：與您的 Azure 訂用帳戶相關聯的租使用者。 根據預設，這會設定為您目前使用的租使用者。 您可以關閉此視窗，然後選取畫面右上方的 [頭像] （包含您的姓名縮寫）來切換租使用者。 按一下 [ **遷移至 Azure** ] 以重新開啟視窗。
   * **Azure 訂** 用帳戶名稱：將與資源相關聯的訂用帳戶。 如果您有多個屬於您租使用者的訂用帳戶，請從下拉式清單中選取您想要的訂用帳戶。
   * **撰寫資源名稱**：您選擇的自訂名稱。 它是用來作為撰寫和預測端點查詢 URL 的一部分。 如果您要建立新的撰寫資源，請注意資源名稱只能包含英數位元、 `-` 和開頭或結尾不能是 `-` 。 如果名稱中包含任何其他符號，則建立和遷移資源將會失敗。
   * **Azure 資源組名**：您從下拉式清單中選擇的自訂資源組名。 資源群組可讓您將 Azure 資源分組以方便存取和管理。 如果您的訂用帳戶中目前沒有資源群組，您將無法在 LUIS 入口網站中建立一個資源群組。 移至 [Azure 入口網站](https://ms.portal.azure.com/#create/Microsoft.ResourceGroup) 建立一個，然後移至 LUIS 以繼續登入程式。

6. 在所有區域中成功遷移之後，請按一下 [完成]。 您現在可以存取您的應用程式。 您可以繼續在入口網站內的所有區域中撰寫和維護所有應用程式。


## <a name="using-apps-after-migration"></a>在遷移後使用應用程式

在遷移程式之後，您目前擁有者的所有 LUIS 應用程式，現在都會指派給單一 LUIS 撰寫資源。
**我的應用程式** 清單會顯示遷移至新撰寫資源的應用程式。 在您存取應用程式之前，請選取 [ **選擇不同的撰寫資源** ] 來選取訂用帳戶，並撰寫資源來查看可以撰寫的應用程式。

> [!div class="mx-imgBorder"]
> ![選取訂用帳戶和撰寫資源](./media/migrate-authoring-key/select-sub-and-resource.png)


如果您想要以程式設計方式編輯您的應用程式，則需要撰寫索引鍵值。 在 LUIS 入口網站中，按一下畫面頂端的 [ **管理** ]，然後選取 [ **Azure 資源**]，即可顯示這些值。 您也可以在資源的 [ **金鑰和端點** ] 頁面的 [Azure 入口網站] 中找到它們。 您也可以建立更多撰寫資源，並從相同的頁面指派。

## <a name="adding-contributors-to-authoring-resources"></a>新增參與者以撰寫資源

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

瞭解如何在您的撰寫資源上 [新增參與者](luis-how-to-collaborate.md) 。 參與者將可存取該資源下的所有應用程式。

您可以在該資源的 **存取控制 (IAM)** 頁面上，從 Azure 入口網站將參與者新增至撰寫資源。 如需詳細資訊，請參閱 [將參與者新增至您的應用程式](luis-how-to-collaborate.md)。

> [!Note]
> 如果已遷移 LUIS 應用程式的擁有者，並將共同作業者新增為 Azure 資源的參與者，則共同作業者仍無法存取該應用程式，除非他們也遷移。


## <a name="troubleshooting-the-migration-process"></a>針對遷移程式進行疑難排解

如果您在下拉式清單中找不到您的 Azure 訂用帳戶：
* 確定您有已獲授權可建立認知服務資源的有效 Azure 訂用帳戶。 移至 [Azure 入口網站](https://ms.portal.azure.com) 並檢查訂用帳戶的狀態。 如果您沒有帳戶，請 [建立一個免費的 Azure 帳戶](https://azure.microsoft.com/free/cognitive-services/)。
* 確定您是在與有效訂用帳戶相關聯的正確租使用者中。 您可以切換租使用者，選取畫面右上方的 [頭像]，包含您的姓名縮寫。

  > [!div class="mx-imgBorder"]
  > ![切換目錄的頁面](./media/migrate-authoring-key/switch-directories.png)

如果您有現有的撰寫資源，但在選取 [ **使用現有的撰寫資源** ] 選項時找不到它：
* 您的資源可能是在與您嘗試遷移的不同區域中建立的。
* 改為從 LUIS 入口網站建立新資源。

如果您選取 [ **建立新的撰寫資源** ] 選項，而遷移失敗，並出現錯誤訊息「無法抓取使用者的 Azure 資訊，請稍後再試一次」：
* 針對每個訂用帳戶，您的訂用帳戶可能會有10個以上的撰寫資源。 如果是這種情況，您將無法建立新的撰寫資源。
* 選取 [ **使用現有的撰寫資源** ] 選項，然後選取您訂用帳戶下的其中一個現有資源來進行遷移。

## <a name="create-new-support-request"></a>建立新的支援要求

如果您在「疑難排解」一節中沒有解決任何遷移問題，請 [建立支援主題](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) ，並在下面提供下欄欄位的資訊：

   * **問題類型**：技術
   * **訂** 用帳戶：從下拉式清單中選擇訂用帳戶
   * **服務**：搜尋並選取 [認知服務]
   * **資源**：如果有現有資源，請選擇 LUIS 資源。 如果沒有，請選取 [一般問題]。

## <a name="next-steps"></a>後續步驟

* 查看 [撰寫和執行時間金鑰的概念](luis-how-to-azure-subscription.md)
* 複習如何 [指派金鑰](luis-how-to-azure-subscription.md) 和 [新增參與者](luis-how-to-collaborate.md)
