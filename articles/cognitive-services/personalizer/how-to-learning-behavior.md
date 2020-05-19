---
title: 設定學習行為
description: 新手模式可讓您安心使用個人化工具服務及其機器學習功能，並提供計量，讓您可以從中學習服務所傳送的資訊，而不會造成線上流量的風險。
ms.topic: how-to
ms.date: 05/01/2020
ms.openlocfilehash: af38500fc439964f9928cdd08aae2380ee0d0a8a
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599589"
---
# <a name="configure-the-personalizer-learning-behavior"></a>設定個人化工具學習行為

[新手模式](concept-apprentice-mode.md)可讓您對個人化工具服務及其機器學習功能有信任和信心，並保證服務會傳送可從學習的資訊，而不會有風險的線上流量。

[!INCLUDE [Important Blue Box - Apprentice mode pricing tier](./includes/important-apprentice-mode.md)]

## <a name="configure-apprentice-mode"></a>設定新手模式

1. 登入個人化工具資源的[Azure 入口網站](https://portal.azure.com)。

1. 在 [設定 **] 頁面的**[**學習行為**] 索引標籤上，選取 [傳回**基準動作]、[學習為新手]，** 然後選取 [**儲存**]。

> [!div class="mx-imgBorder"]
> ![在 Azure 入口網站中設定新手模式學習行為的螢幕擷取畫面](media/settings/configure-learning-behavior-azure-portal.png)

## <a name="changes-to-the-existing-application"></a>現有應用程式的變更

您現有的應用程式不應變更其目前選取動作以顯示的方式，或應用程式如何判斷該**動作的值**。 應用程式的唯一變更可能是傳送至個人化工具之排名 API 的動作順序。 您的應用程式目前顯示的動作會當做動作清單中的_第一個動作_來傳送。 [排名 API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank)會使用這個第一個動作來定型您的個人化工具模型。

### <a name="configure-your-application-to-call-the-rank-api"></a>設定應用程式以呼叫排名 API

若要將個人化工具新增至您的應用程式，您必須呼叫排名和獎勵 Api。

1. 在您現有的應用程式邏輯中，將[排名 API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank)呼叫新增至您判斷動作及其功能清單的位置之後。 動作清單中的第一個動作必須是您現有邏輯所選取的動作。

1. 設定您的程式碼，以顯示與排名 API 回應的報酬**動作識別碼**相關聯的動作。

### <a name="configure-your-application-to-call-reward-api"></a>設定應用程式以呼叫獎勵 API

1. 使用您現有的商務邏輯來計算**顯示之動作的報酬**。 值必須在0到1的範圍內。 使用[獎勵 API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward)將此報酬傳送至個人化工具。 獎勵值不會立即出現，而且可以在一段時間內延遲，視您的商務邏輯而定。

1. 如果您未在設定的報酬**等待時間**內傳回報酬，則會改用預設報酬。

## <a name="evaluate-apprentice-mode"></a>評估新手模式

在 Azure 入口網站的個人化工具資源的 [**評估**] 頁面上，查看目前的**學習行為效能**。

> [!div class="mx-imgBorder"]
> ![在 Azure 入口網站中查看評估新手模式學習行為的螢幕擷取畫面](media/settings/evaluate-apprentice-mode.png)

新手模式提供下列**評估計量**：
* **基準–平均**報酬：應用程式預設值的平均報酬（基準）。
* **個人化工具–平均**報酬：個人化工具可能達到的總報酬數平均值。
* **最新1000事件的報酬率比率**：基準和個人化工具報酬的比率–正規化于最近的1000事件。

## <a name="evaluate-apprentice-mode-features"></a>評估新手模式功能

使用[離線評估](how-to-offline-evaluation.md)來評估功能。

## <a name="switch-behavior-to-online-mode"></a>將行為切換為線上模式

當您判斷個人化工具是以平均75-85% 的滾動平均來定型時，此模型就已準備好切換為線上模式。

在個人化工具資源的**Azure 入口網站的 [設定] 頁面上**，于 [**學習行為**] 索引標籤上，選取 [傳回**最佳動作**]，然後選取 [**儲存**]。

您不需要對排名和報酬 API 呼叫進行任何變更。

## <a name="next-steps"></a>後續步驟

* [管理模型和學習設定](how-to-manage-model.md)
