---
title: 包含檔案
description: 包含檔案
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 04/27/2020
ms.author: diberry
ms.openlocfilehash: d35f11125809167c4834b33f913d9369a6402ac0
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188845"
---
[參考文件](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer?view=azure-python) | [程式庫來源程式碼](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-personalizer) | [套件 (pypi)](https://pypi.org/project/azure-cognitiveservices-personalizer/) | [範例](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py)

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="using-this-quickstart"></a>使用此快速入門


使用本快速入門有幾個步驟：

* 在 Azure 入口網站中，建立個人化工具資源
* 在 Azure 入口網站中，於個人化工具資源的 [設定]  頁面上，將模型更新頻率變更為非常短的間隔
* 在程式碼編輯器中，建立程式碼檔案並編輯程式碼檔案
* 在命令列或終端機中，從命令列安裝 SDK
* 在命令列或終端機中，執行程式碼檔案

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [Change model frequency](change-model-frequency.md)]

## <a name="install-the-python-library-for-personalizer"></a>安裝 Python 個人化工具程式庫

使用下列命令安裝適用於 Python 的個人化工具用戶端程式庫：

```console
pip install azure-cognitiveservices-personalizer
```

## <a name="object-model"></a>物件模型

個人化工具用戶端是一種 [PersonalizerClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.personalizer_client.personalizerclient?view=azure-python) 物件，會使用含有金鑰的 Microsoft.Rest.ServiceClientCredentials 向 Azure 進行驗證。

若要要求內容的單一最佳時間，請建立 [RankRequest](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.models.rankrequest?view=azure-python)，然後將其傳至 client.Rank 方法。 Rank 方法會傳回 RankResponse。

若要將獎勵分數傳送至個人化工具，請將事件識別碼和獎勵分數 (值) 設定為傳送至 EventOperations 類別上的 [Reward](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.operations.events_operations.eventsoperations?view=azure-python#reward-event-id--value--custom-headers-none--raw-false----operation-config-) 方法。

在本快速入門中，決定獎勵是很簡單的。 在生產系統中，判斷影響[獎勵分數](../concept-rewards.md)的因素及影響程度可能是複雜的程序，您可能會隨著時間做出變更決定。 這應該是個人化工具架構中的一個主要設計決策。

## <a name="code-examples"></a>程式碼範例

這些程式碼片段會說明如何使用適用於 Python 的個人化工具用戶端程式庫執行下列動作：

* [建立個人化工具用戶端](#create-a-personalizer-client)
* [排名 API](#request-the-best-action)
* [獎勵 API](#send-a-reward)

## <a name="create-a-new-python-application"></a>建立新的 Python 應用程式

在您慣用的編輯器或名為 `sample.py` 的 IDE 中，建立新的 Python 應用程式。

## <a name="add-the-dependencies"></a>新增相依性

從專案目錄，在慣用的編輯器或 IDE 中開啟 **sample.py** 檔案。 新增下列內容：

[!code-python[Add module dependencies](~/samples-personalizer/quickstarts/python/sample.py?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>新增個人化工具資源資訊

針對從環境變數中提取的資源 Azure 金鑰和端點 (名稱為 `PERSONALIZER_RESOURCE_KEY` 和 `PERSONALIZER_RESOURCE_ENDPOINT`) 建立變數。 如果您在應用程式啟動後才建立環境變數，則執行該應用程式的編輯器、IDE 或殼層必須先關閉再重新載入後，才能存取該變數。 這些方法稍後會在本快速入門中建立。

資源名稱是端點 URL 的一部分：`https://<your-resource-name>.api.cognitive.microsoft.com/`。

[!code-python[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/samples-personalizer/quickstarts/python/sample.py?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>建立個人化工具用戶端

接下來，建立可傳回個人化工具用戶端的方法。 方法的參數是 `PERSONALIZER_RESOURCE_ENDPOINT`，而 ApiKey 是 `PERSONALIZER_RESOURCE_KEY`。

[!code-python[Create the Personalizer client](~/samples-personalizer/quickstarts/python/sample.py?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>取得以動作表示的內容選擇

動作代表您要個人化工具從中選取最佳內容項目的內容選擇。 將下列方法新增至 Program 類別，以代表動作及其特性的組合。

[!code-python[Present time out day preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=getActions)]

[!code-python[Present time out day preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=createUserFeatureTimeOfDay)]

[!code-python[Present food taste preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=createUserFeatureTastePreference)]

## <a name="create-the-learning-loop"></a>建立學習迴圈

個人化工具學習迴圈是[排名](#request-the-best-action)和[獎勵](#send-a-reward)呼叫的循環。 在本快速入門中，用於個人化內容的每個排名呼叫後面都會接著獎勵呼叫，以告訴個人化工具該服務的成效為何。

下列程式碼會在命令列上進行詢問使用者喜好的循環迴圈，並將該資訊傳送至個人化工具以選取最佳動作，然後向客戶顯示選取項目，讓他們從清單中選擇，接著將獎勵傳送至個人化工具，告知服務的成效為何。

[!code-python[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/python/sample.py?name=mainLoop&highlight=9,10,29)]

新增下列方法，以在執行程式碼檔案之前，[取得內容選項](#get-content-choices-represented-as-actions)：

* `get_user_preference`
* `get_user_timeofday`
* `get_actions`

## <a name="request-the-best-action"></a>要求最佳動作


為了完成排名要求，程式會詢問使用者的喜好，以建立內容選擇的 `currentContent`。 程式可以建立從動作中排除的內容，如 `excludeActions` 所示。 排名要求需要動作及其特性、currentContext 特性、excludeActions 和唯一排名事件識別碼，才能接收回應。

本快速入門有一天時間和使用者食物喜好的簡單關係特性。 在生產系統中，判斷和[評估](../concept-feature-evaluation.md)[動作和特性](../concepts-features.md)可能不是簡單的事。

[!code-python[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/python/sample.py?name=rank)]

## <a name="send-a-reward"></a>傳送獎勵


若要取得獎勵分數以在獎勵要求中傳送，程式會從命令列取得使用者的選取項目，將數值指派給選取項目，然後將唯一的事件識別碼和獎勵分數當作數值傳送至獎勵 API。

本快速入門會指派簡單的數字作為獎勵分數，也就是零或 1。 在生產系統中，視您的特定需求而定，判斷要傳送給[獎勵](../concept-rewards.md)呼叫的時機和內容可能不是簡單的事。

[!code-python[The Personalizer learning loop sends a reward.](~/samples-personalizer/quickstarts/python/sample.py?name=reward&highlight=9)]

## <a name="run-the-program"></a>執行程式

使用 Python 從您的應用程式目錄執行應用程式。

```console
python sample.py
```

![快速入門程式會詢問幾個問題以收集使用者偏好 (稱為特性)，然後提供最佳的動作。](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)