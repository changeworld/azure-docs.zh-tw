---
title: 設定學習行為
description: 新手模式可讓您安心個人化工具服務及其機器學習功能，並提供服務所傳送的計量資訊，而不會有風險的線上流量。
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 05/01/2020
ms.openlocfilehash: 10e98cd2f0ad4793aa43f9bb3316c522b44f1d2c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91303531"
---
# <a name="configure-the-personalizer-learning-behavior"></a>設定個人化工具學習行為

[新手模式](concept-apprentice-mode.md) 為您提供個人化工具服務及其機器學習功能的信任和信心，並保證服務會傳送可從中學習的資訊，而不會有風險的線上流量。

[!INCLUDE [Important Blue Box - Apprentice mode pricing tier](./includes/important-apprentice-mode.md)]

## <a name="configure-apprentice-mode"></a>設定新手模式

1. 登入個人化工具資源的 [Azure 入口網站](https://portal.azure.com)。

1. **在 [設定] 頁面的**[**學習行為**] 索引標籤上，選取 [傳回**基準動作]、[以新手方式學習]，** 然後選取 [**儲存**]。

> [!div class="mx-imgBorder"]
> ![在 Azure 入口網站中設定新手模式學習行為的螢幕擷取畫面](media/settings/configure-learning-behavior-azure-portal.png)

## <a name="changes-to-the-existing-application"></a>對現有應用程式的變更

您現有的應用程式不應變更其目前選取動作以顯示的方式，或應用程式如何判斷值、該動作的 **獎勵** 。 應用程式的唯一變更可能是傳送至個人化工具排名 API 的動作順序。 您的應用程式目前所顯示的動作會以動作清單中的 _第一個動作_ 來傳送。 [排名 API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank)會使用第一個動作來定型您的個人化工具模型。

### <a name="configure-your-application-to-call-the-rank-api"></a>設定您的應用程式來呼叫排名 API

為了將個人化工具新增至您的應用程式，您需要呼叫排名和獎勵 Api。

1. 將 [排名 API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank) 呼叫新增至您現有應用程式邏輯中的點之後，您可以在其中判斷動作和其功能的清單。 動作清單中的第一個動作必須是您現有邏輯所選取的動作。

1. 設定您的程式碼，以顯示與排名 API 回應的 **獎勵動作識別碼**相關聯的動作。

### <a name="configure-your-application-to-call-reward-api"></a>設定您的應用程式以呼叫獎勵 API

1. 使用您現有的商務邏輯來計算所顯示動作的 **獎勵** 。 值必須在0到1的範圍內。 使用 [獎勵 API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward)將此獎勵傳送至個人化工具。 獎勵值不會立即出現，並可在一段時間內延遲，視您的商務邏輯而定。

1. 如果您未在設定的 **獎勵等候時間**內傳回獎勵，則會改用預設獎勵。

## <a name="evaluate-apprentice-mode"></a>評估新手模式

在 [Azure 入口網站] 中，于個人化工具資源的 [ **評估** ] 頁面上，檢查 **目前的學習行為效能**。

> [!div class="mx-imgBorder"]
> ![在 Azure 入口網站中查看新手模式學習行為評估的螢幕擷取畫面](media/settings/evaluate-apprentice-mode.png)

新手模式提供下列 **評估度量**：
* **基準–平均獎勵**：應用程式預設 (基準) 的平均獎勵。
* **個人化工具–平均**報酬：可能已達到的報酬率總計平均個人化工具。
* **最新1000事件的獎勵成就率**：基準和個人化工具獎勵的比率–標準化于最近的1000事件。

## <a name="evaluate-apprentice-mode-features"></a>評估新手模式功能

使用 [離線評估](how-to-offline-evaluation.md)來評估功能。

## <a name="switch-behavior-to-online-mode"></a>將行為切換至線上模式

當您判斷個人化工具是以平均75-85% 的累積平均進行定型時，模型就可以切換到線上模式。

在個人化工具資源的 Azure 入口網站中，在 [設定 **] 頁面的 [** **學習行為** ] 索引標籤上，選取 [傳回 **最佳動作** ]，然後選取 [ **儲存**]。

您不需要對排名和獎勵 API 呼叫進行任何變更。

## <a name="next-steps"></a>後續步驟

* [管理模型和學習設定](how-to-manage-model.md)
