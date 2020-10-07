---
title: 包含檔案
description: 包含檔案
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: cog-serv-seo-aug-2020
ms.date: 08/25/2020
ms.openlocfilehash: 18fa74562b50ac832c7512351065bf8fedeba74f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "89055363"
---
[參考文件](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer?view=azure-python) | [程式庫來源程式碼](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-personalizer) | [套件 (pypi)](https://pypi.org/project/azure-cognitiveservices-personalizer/) | [範例](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/Personalizer)

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
* 擁有 Azure 訂用帳戶之後，在 Azure 入口網站中<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer"  title="建立個人化工具資源"  target="_blank">建立個人化工具資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>，以取得您的金鑰和端點。 在其部署後，按一下 [前往資源]。
    * 您需要來自所建立資源的金鑰和端點，以將應用程式連線至個人化工具 API。 您稍後會在快速入門中將金鑰和端點貼到下列程式碼中。
    * 您可以使用免費定價層 (`F0`) 來試用服務，之後可升級至付費層以用於實際執行環境。

## <a name="setting-up"></a>設定

[!INCLUDE [Change model frequency](change-model-frequency.md)]

### <a name="install-the-client-library"></a>安裝用戶端程式庫

安裝 Python 之後，您可以透過以下項目安裝用戶端程式庫：

```console
pip install azure-cognitiveservices-personalizer
```

### <a name="create-a-new-python-application"></a>建立新的 Python 應用程式

建立新的 Python 檔案，並為資源的端點和訂用帳戶金鑰建立變數。

[!INCLUDE [Personalizer find resource info](find-azure-resource-info.md)]

```python
from azure.cognitiveservices.personalizer import PersonalizerClient
from azure.cognitiveservices.personalizer.models import RankableAction, RewardRequest, RankRequest
from msrest.authentication import CognitiveServicesCredentials

import datetime, json, os, time, uuid

key = "<paste-your-personalizer-key-here>"
endpoint = "<paste-your-personalizer-endpoint-here>"
```

## <a name="object-model"></a>物件模型

個人化工具用戶端是一種 [PersonalizerClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.personalizer_client.personalizerclient?view=azure-python) 物件，會使用含有金鑰的 Microsoft.Rest.ServiceClientCredentials 向 Azure 進行驗證。

若要要求內容的單一最佳時間，請建立 [RankRequest](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.models.rankrequest?view=azure-python)，然後將其傳至 client.Rank 方法。 Rank 方法會傳回 RankResponse。

若要將獎勵分數傳送至個人化工具，請將事件識別碼和獎勵分數 (值) 設定為傳送至 EventOperations 類別上的 [Reward](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.operations.events_operations.eventsoperations?view=azure-python#reward-event-id--value--custom-headers-none--raw-false----operation-config-) 方法。

在本快速入門中，決定獎勵是很簡單的。 在生產系統中，判斷影響[獎勵分數](../concept-rewards.md)的因素及影響程度可能是複雜的程序，您可能會隨著時間做出變更決定。 這應該是個人化工具架構中的一個主要設計決策。

## <a name="code-examples"></a>程式碼範例

這些程式碼片段會說明如何使用適用於 Python 的個人化工具用戶端程式庫執行下列動作：

* [驗證用戶端](#authenticate-the-client)
* [排名 API](#request-the-best-action)
* [獎勵 API](#send-a-reward)

## <a name="authenticate-the-client"></a>驗證用戶端

使用您稍早建立的 `key` 和 `endpoint` 來具現化 `PersonalizerClient`。

```python
# Instantiate a Personalizer client
client = PersonalizerClient(endpoint, CognitiveServicesCredentials(key))
```

## <a name="get-content-choices-represented-as-actions"></a>取得以動作表示的內容選擇

動作代表您要個人化工具從中選取最佳內容項目的內容選擇。 將下列方法新增至 Program 類別，以代表動作及其特性的組合。

```python
def get_actions():
    action1 = RankableAction(id='pasta', features=[{"taste":"salty", "spice_level":"medium"},{"nutrition_level":5,"cuisine":"italian"}])
    action2 = RankableAction(id='ice cream', features=[{"taste":"sweet", "spice_level":"none"}, { "nutritional_level": 2 }])
    action3 = RankableAction(id='juice', features=[{"taste":"sweet", 'spice_level':'none'}, {'nutritional_level': 5}, {'drink':True}])
    action4 = RankableAction(id='salad', features=[{'taste':'salty', 'spice_level':'none'},{'nutritional_level': 2}])
    return [action1, action2, action3, action4]
```

```python
def get_user_timeofday():
    res={}
    time_features = ["morning", "afternoon", "evening", "night"]
    time = input("What time of day is it (enter number)? 1. morning 2. afternoon 3. evening 4. night\n")
    try:
        ptime = int(time)
        if(ptime<=0 or ptime>len(time_features)):
            raise IndexError
        res['time_of_day'] = time_features[ptime-1]
    except (ValueError, IndexError):
        print("Entered value is invalid. Setting feature value to", time_features[0] + ".")
        res['time_of_day'] = time_features[0]
    return res
```

```python
def get_user_preference():
    res = {}
    taste_features = ['salty','sweet']
    pref = input("What type of food would you prefer? Enter number 1.salty 2.sweet\n")
    
    try:
        ppref = int(pref)
        if(ppref<=0 or ppref>len(taste_features)):
            raise IndexError
        res['taste_preference'] = taste_features[ppref-1]
    except (ValueError, IndexError):
        print("Entered value is invalid. Setting feature value to", taste_features[0]+ ".")
        res['taste_preference'] = taste_features[0]
    return res
```

## <a name="create-the-learning-loop"></a>建立學習迴圈

個人化工具學習迴圈是[排名](#request-the-best-action)和[獎勵](#send-a-reward)呼叫的循環。 在本快速入門中，用於個人化內容的每個排名呼叫後面都會接著獎勵呼叫，以告訴個人化工具該服務的成效為何。

下列程式碼會在命令列上進行詢問使用者喜好的循環迴圈，並將該資訊傳送至個人化工具以選取最佳動作，然後向客戶顯示選取項目，讓他們從清單中選擇，接著將獎勵傳送至個人化工具，告知服務的成效為何。

```python
keep_going = True
while keep_going:

    eventid = str(uuid.uuid4())

    context = [get_user_preference(), get_user_timeofday()]
    actions = get_actions()

    rank_request = RankRequest( actions=actions, context_features=context, excluded_actions=['juice'], event_id=eventid)
    response = client.rank(rank_request=rank_request)
    
    print("Personalizer service ranked the actions with the probabilities listed below:")
    
    rankedList = response.ranking
    for ranked in rankedList:
        print(ranked.id, ':',ranked.probability)

    print("Personalizer thinks you would like to have", response.reward_action_id+".")
    answer = input("Is this correct?(y/n)\n")[0]

    reward_val = "0.0"
    if(answer.lower()=='y'):
        reward_val = "1.0"
    elif(answer.lower()=='n'):
        reward_val = "0.0"
    else:
        print("Entered choice is invalid. Service assumes that you didn't like the recommended food choice.")

    client.events.reward(event_id=eventid, value=reward_val)

    br = input("Press Q to exit, any other key to continue: ")
    if(br.lower()=='q'):
        keep_going = False
```

新增下列方法，以在執行程式碼檔案之前，[取得內容選項](#get-content-choices-represented-as-actions)：

* `get_user_preference`
* `get_user_timeofday`
* `get_actions`

## <a name="request-the-best-action"></a>要求最佳動作

為了完成排名要求，程式會詢問使用者的喜好，以建立內容選擇的 `currentContent`。 程式可以建立從動作中排除的內容，如 `excludeActions` 所示。 排名要求需要動作及其特性、currentContext 特性、excludeActions 和唯一排名事件識別碼，才能接收回應。

本快速入門有一天時間和使用者食物喜好的簡單關係特性。 在生產系統中，判斷和[評估](../concept-feature-evaluation.md)[動作和特性](../concepts-features.md)可能不是簡單的事。

```python
rank_request = RankRequest( actions=actions, context_features=context, excluded_actions=['juice'], event_id=eventid)
response = client.rank(rank_request=rank_request)
```

## <a name="send-a-reward"></a>傳送獎勵

若要取得獎勵分數以在獎勵要求中傳送，程式會從命令列取得使用者的選取項目，將數值指派給選取項目，然後將唯一的事件識別碼和獎勵分數當作數值傳送至獎勵 API。

本快速入門會指派簡單的數字作為獎勵分數，也就是零或 1。 在生產系統中，視您的特定需求而定，判斷要傳送給[獎勵](../concept-rewards.md)呼叫的時機和內容可能不是簡單的事。

```python
reward_val = "0.0"
if(answer.lower()=='y'):
    reward_val = "1.0"
elif(answer.lower()=='n'):
    reward_val = "0.0"
else:
    print("Entered choice is invalid. Service assumes that you didn't like the recommended food choice.")

client.events.reward(event_id=eventid, value=reward_val)
```

## <a name="run-the-program"></a>執行程式

使用 Python 從您的應用程式目錄執行應用程式。

```console
python sample.py
```

![快速入門程式會詢問幾個問題以收集使用者偏好 (稱為特性)，然後提供最佳的動作。](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)