---
title: 設定審核工具設定-內容仲裁
titleSuffix: Azure Cognitive Services
description: 使用審核工具來設定或取得內容仲裁的小組、標記、連接器、工作流程及認證。
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pafarley
ms.openlocfilehash: 2d685683bdc359b31a5a6c550c19e8c0d858f12a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84689838"
---
# <a name="configure-the-review-tool"></a>設定檢閱工具

[審核工具](https://contentmoderator.cognitive.microsoft.com)有幾個重要的功能，您可以透過儀表板上的 [**設定**] 功能表來存取這些功能。

![內容仲裁審核設定功能表](images/settings-1.png)

## <a name="manage-team-and-subteams"></a>管理小組和子小組

[ **小組** ] 索引標籤可讓您管理小組和子小組 &mdash; 使用者的群組，這些使用者可以在某些 [人為評論](../review-api.md#reviews) 啟動時收到通知。 當您使用審核工具) 註冊時，您只能建立一個小組 (，但您可以建立多個子團隊。 小組系統管理員可以邀請成員、設定其許可權，並將其指派給不同的子小組。

![審核工具小組設定](images/settings-2-team.png)

子小組適合用於建立擴大小組，或專門用來審核特定內容類別的小組。 例如，您可能會將成人內容傳送給不同的小組，以進行進一步的評論。

本節說明如何建立子小組，以及快速地立即指派評論。 不過，您可以使用[工作流程](workflows.md)，根據特定準則來指派審核。

### <a name="create-a-subteam"></a>建立子小組

移 **至 [子** 小組] 區段，然後按一下 [ **加入子小組**]。 在對話方塊中輸入您的子小組名稱，然後按一下 [ **儲存**]。

![子小組名稱](images/1-Teams-2.PNG)

#### <a name="invite-teammates"></a>邀請小組成員

如果某人還不是預設小組的成員，您就無法將它指派給子小組，因此您必須先將審核者新增至預設小組。 按一下 [**小組**] 索引標籤上的 [**邀請**]。

![邀請使用者](images/invite-users.png)

#### <a name="assign-teammates-to-subteam"></a>將小組指派給子小組

按一下 [ **新增成員** ] 按鈕，將預設小組的成員指派給一個或多個子小組。 您只能將現有使用者新增至子小組。 如需新增不在審查工具中的新使用者，請使用 [小組設定] 頁面上的 [邀請] 按鈕來邀請他們。

![指派子小組成員](images/1-Teams-3.PNG)

### <a name="assign-reviews-to-subteams"></a>將評論指派給子小組

一旦您建立了子小組和指派的成員之後，就可以開始將內容 [評論](../review-api.md#reviews) 指派給這些子小組。 這是透過網站的 [ **審核** ] 索引標籤來完成。
若要將內容指派給子小組，請按一下右上角的省略號，選取 [ **移至**]，然後選取子小組。

![將影像審核指派給子小組](images/3-review-image-subteam-1.png)

### <a name="switch-between-subteams"></a>切換子小組

如果您是多個子小組的成員，您可以在這些子小組之間切換，以變更為您顯示的內容審核。 在 [ **審核** ] 索引標籤中，選取標示為 [ **預設** ] 的下拉式功能表，然後選取 **[選擇子小組**]。 您可以針對不同的子小組查看內容審查，但只有您所屬的子小組才能查看。

![切換子小組](images/3-review-image-subteam-2.png)

## <a name="tags"></a>標籤

[**標記**] 索引標籤可讓您定義自訂仲裁標記，除了兩個預設仲裁標記 &mdash; **isadult** () 和 **>isracy** (**r**) 。 **a** 當您建立自訂標記時，它會與預設標記一起在評論中提供。 您可以藉由切換可見度設定，來變更哪些標記會顯示在評論中。

![標記視圖，包括 [可見] 核取方塊](images/tags-4-disable.png)

### <a name="create-custom-tags"></a>建立自訂標記

若要建立新的標記，您必須在各自的欄位中輸入簡短的代碼、名稱和描述。

- **簡短程式碼**：輸入您標記的兩個字母代碼。 範例： **cb**
- **名稱**：輸入簡短且描述性的標記名稱（小寫），不含空格。 範例： **isbullying**。
- **描述**： (選擇性) 輸入標記目標內容類型的描述。 範例： **網路霸淩的描述或實例**。

按一下 [ **新增** ] 以新增標記，然後在完成建立標記時按一下 [ **儲存** ]。

![審核工具建立新標記對話方塊](images/settings-3-tags.png)

### <a name="delete-tags"></a>刪除標記

您可以藉由在 [標記] 清單中選取其專案旁的垃圾桶圖示來刪除自訂標籤，但無法刪除預設標記。

## <a name="connectors"></a>連接器

[ **連接器** ] 索引標籤可讓您管理連接器，也就是服務特定的外掛程式，可在內容 [工作流程](../review-api.md#workflows)中以不同的方式處理內容。

當您建立工作流程時，預設連接器是內容仲裁連接器，可將內容標示為 **成人** 或 **猥褻**、找出不雅內容等等。 不過，您可以使用此處所列的其他連接器，只要您有各自服務的認證 (使用臉部連接器，例如，您將需要取得 [臉部](https://docs.microsoft.com/azure/cognitive-services/face/overview) 訂用帳戶金鑰) 。

[審核工具](./human-in-the-loop.md)包括下列連接器：

- 表情
- 臉部
- PhotoDNA Cloud Service
- 文字分析

### <a name="add-a-connector"></a>新增連接器

若要新增連接器 (並讓它可在內容 [工作流程](../review-api.md#workflows)) 中使用，請選取適當的 **[連線]** 按鈕。 在下一個對話方塊中，輸入該服務的訂用帳戶金鑰。 當您完成時，新的連接器應該會出現在頁面頂端。

![Content Moderator 連接器設定](images/settings-4-connectors.png)

## <a name="workflows"></a>工作流程

[ **工作流程** ] 索引標籤可讓您管理 [工作流程](../review-api.md#workflows)。 工作流程是以雲端為基礎的內容篩選，並使用連接器以不同方式排序內容，並採取適當的動作。 您可以在這裡定義、編輯和測試您的工作流程。 如需如何執行此動作的指引，請參閱 [定義和使用工作流程](Workflows.md) 。

![Content Moderator 工作流程設定](images/settings-5-workflows.png)

## <a name="credentials"></a>認證

[ **認證** ] 索引標籤可讓您快速存取您的內容仲裁訂用帳戶金鑰，您將需要從 REST 呼叫或用戶端 SDK 存取任何審核服務。

![Content Moderator 認證](images/settings-6-credentials.png)

### <a name="use-external-credentials-for-workflows"></a>使用工作流程的外部認證

當您註冊時， [審核工具](https://contentmoderator.cognitive.microsoft.com) 會為 Azure 內容仲裁服務產生免費試用金鑰，但您也可以將其設定為使用 azure 帳戶中的現有金鑰。 這是適用于大規模案例的建議，因為免費試用版金鑰有嚴格的使用量限制 ([定價和限制](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/)) 。

如果您已在 Azure 中建立 [內容仲裁資源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) ，請在 Azure 入口網站中流覽至該資源，然後選取 [ **金鑰** ] 分頁。 複製其中一個金鑰。

![Azure 入口網站中的 Content Moderator 金鑰](images/credentials-azure-portal-keys.PNG)

在 [審核工具](https://contentmoderator.cognitive.microsoft.com)的 [ **認證** ] 索引標籤中，移至 [ **工作流程設定** ] 窗格，並選取 [ **編輯**]，然後將金鑰貼到 **Apim-訂** 用帳戶金鑰欄位中。 現在，呼叫審核 Api 的工作流程將會使用您的 Azure 認證。

> [!NOTE]
> [ **工作流程設定** ] 窗格中的其他兩個欄位適用于自訂字詞和影像清單。 請參閱 [自訂條款](../try-terms-list-api.md) 或 [自訂影像](../try-image-list-api.md) 指南以瞭解這些資訊。

### <a name="use-your-azure-account-with-the-review-apis"></a>使用您的 Azure 帳戶搭配審核 Api

若要使用您的 Azure 金鑰搭配審核 Api，您需要取出資源識別碼。 移至 Azure 入口網站中的內容仲裁資源，然後選取 [ **屬性** ] 分頁。 複製資源識別碼值，並將它貼到審核工具的 [**認證**] 索引標籤的 [允許清單的**資源識別碼] (s) **欄位。

![Azure 入口網站中的 Content Moderator 資源識別碼](images/credentials-azure-portal-resourceid.PNG)

如果您在這兩個地方都輸入了訂用帳戶金鑰，將不會使用審核工具帳戶隨附的試用版金鑰，但仍可供使用。

## <a name="next-steps"></a>接下來的步驟

遵循 [審核工具快速入門](../quick-start.md) ，開始使用內容仲裁案例中的審核工具。