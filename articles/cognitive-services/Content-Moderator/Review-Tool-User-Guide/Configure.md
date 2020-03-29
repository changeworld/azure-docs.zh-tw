---
title: 配置審閱工具設置 - 內容檢閱者
titleSuffix: Azure Cognitive Services
description: 使用"審閱"工具配置或檢索內容審閱人的團隊、標記、連接器、工作流和憑據。
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pafarley
ms.openlocfilehash: 2d685683bdc359b31a5a6c550c19e8c0d858f12a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221353"
---
# <a name="configure-the-review-tool"></a>設定檢閱工具

["審閱"工具](https://contentmoderator.cognitive.microsoft.com)具有幾個重要功能，您可以通過儀表板上的 **"設置"** 功能表訪問這些功能。

![內容審閱人 查看設置功能表](images/settings-1.png)

## <a name="manage-team-and-subteams"></a>管理團隊和子團隊

"**團隊"** 選項卡允許您管理團隊和子團隊&mdash;組的使用者組，這些使用者可在開始[某些人工評論](../review-api.md#reviews)時收到通知。 您只能有一個團隊（在使用"審閱"工具註冊時創建），但您可以創建多個子團隊。 團隊管理員可以邀請成員、設置其許可權並將他們分配給不同的子團隊。

![查看工具團隊設置](images/settings-2-team.png)

子小組適合用於建立擴大小組，或專門用來審核特定內容類別的小組。 例如，您可以將成人內容發送給單獨的團隊進行進一步審閱。

本節介紹如何創建子團隊並快速動態分配評論。 不過，您可以使用[工作流程](workflows.md)，根據特定準則來指派審核。

### <a name="create-a-subteam"></a>建立子小組

轉到**子團隊**部分，然後按一下"**添加子團隊**"。 在對話方塊中輸入子團隊名稱，然後按一下"**保存**"。

![子小組名稱](images/1-Teams-2.PNG)

#### <a name="invite-teammates"></a>邀請隊友

如果某人不是預設團隊的成員，則無法將某人分配給子團隊，因此您需要首先將檢閱者添加到預設團隊。 按一下"**團隊**"選項卡上的 **"邀請**"。

![邀請使用者](images/invite-users.png)

#### <a name="assign-teammates-to-subteam"></a>將隊友分配到子團隊

按一下"**添加成員"** 按鈕可將預設團隊的成員分配給一個或多個子團隊。 您只能將現有使用者新增至子小組。 如需新增不在審查工具中的新使用者，請使用 [小組設定] 頁面上的 [邀請] 按鈕來邀請他們。

![指派子小組成員](images/1-Teams-3.PNG)

### <a name="assign-reviews-to-subteams"></a>將審核分配給子團隊

創建子團隊和分配成員後，即可開始向這些子團隊分配內容[審核](../review-api.md#reviews)。 這是從網站的 **"審閱**"選項卡完成的。
要將內容分配給子團隊，請按一下右上角的省略號，選擇 **"移動到**"並選擇子組。

![將影像審核指派給子小組](images/3-review-image-subteam-1.png)

### <a name="switch-between-subteams"></a>切換子小組

如果您是多個子團隊的成員，則可以在這些子團隊之間切換，以更改為您顯示的內容評論。 在 **"審閱**"選項卡中，選擇標記為 **"預設"** 的下拉式功能表，然後**選擇"子團隊**"。 您可以查看不同子團隊的內容評論，但只能查看您屬於其成員的內容評論。

![切換子小組](images/3-review-image-subteam-2.png)

## <a name="tags"></a>Tags

"**標記"** 選項卡允許您定義自訂審核標記，除了兩個預設的審核標記&mdash;**是成人**（**a**） 和**isracy** （**r**） 創建自訂標記時，它與預設標記一起在評論中可用。 您可以通過切換這些標記的可見度設置來更改評論中顯示的標記。

![標記視圖，包括"可見"核取方塊](images/tags-4-disable.png)

### <a name="create-custom-tags"></a>創建自訂標記

要創建新標記，必須在相應的欄位中輸入短代碼、名稱和說明。

- **短代碼**：輸入標記的兩個字母代碼。 示例： **cb**
- **名稱**：在不帶空格小寫的情況下輸入簡短且描述性標記名稱。 示例：**是欺淩**。
- **說明**：（可選） 輸入標記目標內容類型的說明。 示例：**網路欺淩的描述或實例**。

按一下"**添加"** 以委任標記，並在創建標記後按一下"**保存**"。

![查看工具創建新標記對話方塊](images/settings-3-tags.png)

### <a name="delete-tags"></a>刪除標籤

您可以通過在"標記"清單中選擇其條目旁邊的垃圾標記圖示來刪除自訂標記，但不能刪除預設標記。

## <a name="connectors"></a>連接器

連接器**選項卡**允許您管理連接器，連接器是特定于服務的外掛程式，可以作為內容[工作流](../review-api.md#workflows)的一部分以不同的方式處理內容。

創建工作流時的預設連接器是內容檢閱者連接器，它可以將內容標記為**成人**或**流質**、查找褻瀆等。 但是，您可以使用此處列出的其他連接器，只要您具有其各自服務的憑據（例如，要使用 Face 連接器，則需要獲取[Face](https://docs.microsoft.com/azure/cognitive-services/face/overview)訂閱金鑰）。

["審閱"工具](./human-in-the-loop.md)包括以下連接器：

- 表情 API
- 臉部
- PhotoDNA Cloud Service
- 文字分析

### <a name="add-a-connector"></a>新增連接器

要添加連接器（並使它可用於內容[工作流](../review-api.md#workflows)），請選擇相應的**連接**按鈕。 在下一個對話方塊中，輸入該服務的訂閱金鑰。 完成後，新連接器應顯示在頁面頂部。

![Content Moderator 連接器設定](images/settings-4-connectors.png)

## <a name="workflows"></a>工作流程

**工作流**選項卡允許您管理[工作流](../review-api.md#workflows)。 工作流是內容的基於雲的篩選器，它們使用連接器以不同方式對內容進行排序並採取適當的操作。 在這裡，您可以定義、編輯和測試工作流。 有關如何執行此操作的指導[，請參閱定義和使用工作流](Workflows.md)。

![Content Moderator 工作流程設定](images/settings-5-workflows.png)

## <a name="credentials"></a>認證

"**憑據"** 選項卡提供對內容審閱人訂閱金鑰的快速訪問，您需要從 REST 調用或用戶端 SDK 訪問任何審核服務。

![Content Moderator 認證](images/settings-6-credentials.png)

### <a name="use-external-credentials-for-workflows"></a>將外部憑據用於工作流

["審閱"工具](https://contentmoderator.cognitive.microsoft.com)會在註冊時為 Azure 內容審閱人服務生成免費試用金鑰，但也可以將其配置為使用 Azure 帳戶中的現有金鑰。 對於大型方案，建議這樣做，因為免費試用金鑰具有嚴格的使用限制（[定價和限制](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/)）。

如果在 Azure 中創建了[內容審閱人資源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator)，請在 Azure 門戶中導航到該資源，然後選擇 **"金鑰"** 邊欄選項卡。 複製您的一個金鑰。

![Azure 入口網站中的 Content Moderator 金鑰](images/credentials-azure-portal-keys.PNG)

在["審閱"工具](https://contentmoderator.cognitive.microsoft.com)的 **"憑據"** 選項卡中，**轉到"工作流設置"** 窗格，選擇 **"編輯**"，並將金鑰粘貼到**Ocp-Apim-訂閱鍵**欄位中。 現在，調用審核 API 的工作流將使用 Azure 憑據。

> [!NOTE]
> **"工作流設置"** 窗格中的其他兩個欄位用於自訂術語和圖像清單。 請參閱[自訂術語](../try-terms-list-api.md)或[自訂圖像](../try-image-list-api.md)指南以瞭解這些。

### <a name="use-your-azure-account-with-the-review-apis"></a>將 Azure 帳戶與審核 API 一起使用

若要將 Azure 金鑰與審核 API 一起使用，需要檢索資源識別碼。 轉到 Azure 門戶中的內容審閱人資源，然後選擇 **"屬性**"邊欄選項卡。 複製資源識別碼 值並將其粘貼到"審閱"工具的**憑據**選項卡的 **"白名單資源識別碼"** 欄位中。

![Azure 入口網站中的 Content Moderator 資源識別碼](images/credentials-azure-portal-resourceid.PNG)

如果您在兩個位置輸入了訂閱金鑰，則不會使用"審閱"工具帳戶附帶的試用金鑰，但將保持可用狀態。

## <a name="next-steps"></a>後續步驟

按照["審閱"工具快速入門](../quick-start.md)開始在內容審核方案中使用"審閱"工具。