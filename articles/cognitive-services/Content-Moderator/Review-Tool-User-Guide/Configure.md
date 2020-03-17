---
title: 設定審核工具設定-內容仲裁
titleSuffix: Azure Cognitive Services
description: 使用審核工具來設定或抓取內容仲裁的小組、標記、連接器、工作流程和認證。
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pafarley
ms.openlocfilehash: 2d685683bdc359b31a5a6c550c19e8c0d858f12a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79221353"
---
# <a name="configure-the-review-tool"></a>設定檢閱工具

[審核工具](https://contentmoderator.cognitive.microsoft.com)有幾項重要功能，可讓您透過儀表板上的 [**設定**] 功能表來存取。

![內容仲裁審查太過設定功能表](images/settings-1.png)

## <a name="manage-team-and-subteams"></a>管理小組和子團隊

[**小組**] 索引標籤可讓您管理團隊和子小組&mdash;使用者群組，以便在某些[人為審查](../review-api.md#reviews)開始時收到通知。 您只能有一個小組（當您使用審核工具註冊時建立），但您可以建立多個子小組。 小組系統管理員可以邀請成員、設定其許可權，並將它們指派給不同的子團隊。

![審查工具小組設定](images/settings-2-team.png)

子小組適合用於建立擴大小組，或專門用來審核特定內容類別的小組。 例如，您可能會將成人內容傳送給另一個小組，以供進一步審查。

本節說明如何建立子小組，並快速地立即指派評論。 不過，您可以使用[工作流程](workflows.md)，根據特定準則來指派審核。

### <a name="create-a-subteam"></a>建立子小組

移至子**小組區段，** 然後按一下 [**新增子小組**]。 在對話方塊中輸入您的子小組名稱，然後按一下 [**儲存**]。

![子小組名稱](images/1-Teams-2.PNG)

#### <a name="invite-teammates"></a>邀請團隊成員

如果某人還不是預設小組的成員，您就無法將它指派給子小組，因此您必須先將審核者加入至預設小組。 按一下 [**小組**] 索引標籤上的 [**邀請**]。

![邀請使用者](images/invite-users.png)

#### <a name="assign-teammates-to-subteam"></a>將小組指派給子小組

按一下 [**新增成員**] 按鈕，將您的預設小組成員指派給一或多個子團隊。 您只能將現有使用者新增至子小組。 如需新增不在審查工具中的新使用者，請使用 [小組設定] 頁面上的 [邀請] 按鈕來邀請他們。

![指派子小組成員](images/1-Teams-3.PNG)

### <a name="assign-reviews-to-subteams"></a>將評論指派給子小組

建立子小組和指派的成員之後，您就可以開始將內容[審查](../review-api.md#reviews)指派給這些子小組。 這是從網站的 [**審核**] 索引標籤來完成。
若要將內容指派給子小組，請按一下右上角的省略號，選取 [**移至**]，然後選取子小組。

![將影像審核指派給子小組](images/3-review-image-subteam-1.png)

### <a name="switch-between-subteams"></a>切換子小組

如果您是多個子小組的成員，您可以在這些子小組之間切換，以變更要為您顯示哪些內容審查。 在 [**審核**] 索引標籤中，選取標示為 [**預設**] 的下拉式功能表，然後選取 **[選擇子小組**]。 您可以針對不同的子小組查看內容審查，但只能針對您的成員進行編輯。

![切換子小組](images/3-review-image-subteam-2.png)

## <a name="tags"></a>Tags

[**標籤] 索引**標籤可讓您定義自訂審核標記，除了兩個預設的仲裁標記&mdash;**isadult** （**a**）和**isracy** （**r**）。 當您建立自訂標籤時，該標籤會在評論中與預設標記一起使用。 您可以切換其可見度設定，以變更要在審查中顯示的標記。

![標記視圖，包括 [可見] 核取方塊](images/tags-4-disable.png)

### <a name="create-custom-tags"></a>建立自訂標記

若要建立新的標記，您必須在個別欄位中輸入簡短的代碼、名稱和描述。

- **簡短**的程式碼：輸入標記的兩個字母代碼。 範例： **cb**
- **名稱**：輸入不含空格的簡短描述性標記名稱（小寫）。 範例： **isbullying**。
- **描述**：（選擇性）輸入標記的目標內容類型的描述。 範例：**描述或網路 bullying 的實例**。

按一下 [**新增**] 以新增標籤，然後在完成標記的建立時，按一下 [**儲存**]。

![審查工具 [建立新標記] 對話方塊](images/settings-3-tags.png)

### <a name="delete-tags"></a>刪除標記

您可以在 [標記] 清單中選取專案旁邊的垃圾桶圖示來刪除自訂標記，但無法刪除預設標籤。

## <a name="connectors"></a>連接器

[**連接器**] 索引標籤可讓您管理連接器，這是服務專屬的外掛程式，可以用不同的方式處理內容[工作流程](../review-api.md#workflows)的一部分。

當您建立工作流程時，預設連接器是「內容仲裁者連接器」，可以將內容標示為**成人**或**猥褻**、尋找不雅內容等等。 不過，您可以使用此處所列的其他連接器，只要您有其個別服務的認證（例如，使用臉部連接器，就必須取得[臉部](https://docs.microsoft.com/azure/cognitive-services/face/overview)訂用帳戶金鑰）。

[審查工具](./human-in-the-loop.md)包含下列連接器：

- 表情 API
- 臉部
- PhotoDNA Cloud Service
- 文字分析

### <a name="add-a-connector"></a>新增連接器

若要新增連接器（並讓它可在內容[工作流程](../review-api.md#workflows)中使用），請選取適當的 **[連接]** 按鈕。 在下一個對話方塊中，為該服務輸入您的訂用帳戶金鑰。 當您完成時，您的新連接器應該會出現在頁面頂端。

![Content Moderator 連接器設定](images/settings-4-connectors.png)

## <a name="workflows"></a>工作流程

[**工作流程**] 索引標籤可讓您管理[工作流程](../review-api.md#workflows)。 工作流程是以雲端為基礎的內容篩選，並使用連接器以不同的方式排序內容，並採取適當的動作。 在這裡，您可以定義、編輯和測試工作流程。 如需如何執行此操作的指引，請參閱[定義和使用工作流程](Workflows.md)。

![Content Moderator 工作流程設定](images/settings-5-workflows.png)

## <a name="credentials"></a>認證

[**認證**] 索引標籤可讓您快速存取您的內容仲裁訂用帳戶金鑰，您將需要從 REST 呼叫或用戶端 SDK 存取任何審核服務。

![Content Moderator 認證](images/settings-6-credentials.png)

### <a name="use-external-credentials-for-workflows"></a>使用工作流程的外部認證

當您註冊時，[審核工具](https://contentmoderator.cognitive.microsoft.com)會為 Azure 內容仲裁服務產生免費試用金鑰，但您也可以將其設定為使用 azure 帳戶中的現有金鑰。 這建議用於大規模的案例，因為免費試用金鑰具有嚴格的使用限制（[定價和限制](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/)）。

如果您已在 Azure 中建立[內容仲裁資源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator)，請在 Azure 入口網站中流覽至它，然後選取 **金鑰** 分頁。 複製其中一個金鑰。

![Azure 入口網站中的 Content Moderator 金鑰](images/credentials-azure-portal-keys.PNG)

在[審核工具](https://contentmoderator.cognitive.microsoft.com)的 [**認證**] 索引標籤中，移至 [**工作流程設定**] 窗格，選取 [**編輯**]，然後將您的金鑰貼入 [ **Ocp-Apim-訂用帳戶-金鑰**] 欄位中。 現在，呼叫仲裁 Api 的工作流程會使用您的 Azure 認證。

> [!NOTE]
> [**工作流程設定**] 窗格中的其他兩個欄位適用于自訂字詞和影像清單。 若要深入瞭解，請參閱[自訂詞彙](../try-terms-list-api.md)或[自訂影像](../try-image-list-api.md)指南。

### <a name="use-your-azure-account-with-the-review-apis"></a>搭配審查 Api 使用您的 Azure 帳戶

若要使用您的 Azure 金鑰搭配審查 Api，您需要取得您的資源識別碼。 移至 Azure 入口網站中的內容仲裁資源，然後選取 [**屬性**] 分頁。 複製 [資源識別碼] 值，並將它貼到審核工具的 [**認證**] 索引標籤的 [允許清單的**資源識別碼**] 欄位中。

![Azure 入口網站中的 Content Moderator 資源識別碼](images/credentials-azure-portal-resourceid.PNG)

如果您已在這兩個地方輸入您的訂用帳戶金鑰，將不會使用您的審核工具帳戶所附的試用金鑰，但仍可繼續使用。

## <a name="next-steps"></a>後續步驟

請遵循[審核工具快速入門](../quick-start.md)，開始在內容仲裁案例中使用審核工具。