---
title: 快速入門：Metrics Advisor 入口網站
titleSuffix: Azure Cognitive Services
description: 了解如何開始使用 Metrics Advisor 入口網站。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: quickstart
ms.date: 09/30/2020
ms.author: aahi
ms.openlocfilehash: 004685a50e2413c29528ad3aca08a0150843a8aa
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91631356"
---
# <a name="quickstart-monitor-your-first-metric-using-the-web-portal"></a>快速入門：使用入口網站監視您的第一個計量

佈建 Metrics Advisor 執行個體時，可以使用 API 和 Web 工作區來處理服務。 Web 工作區可讓您直接且快速地開始使用服務。 同時也提供以視覺方式進行設定、自訂模型，以及執行根本原因分析。 

* 將計量資料上線
* 檢視您的計量和視覺效果
* 微調偵測組態
* 探索診斷見解
* 建立並訂閱異常警示

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services)
* 擁有 Azure 訂用帳戶之後，在 Azure 入口網站中<a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title="建立 Metrics Advisor 資源"  target="_blank">建立 Metrics Advisor 資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>，以部署您的 Metrics Advisor 執行個體。  

    
> [!TIP]
> * 可能需要 10 到 30 分鐘，您的 Metrics Advisor 資源才會部署完成。 一旦成功部署，請按一下 [移至資源]。
> * 如果要使用 REST API 與服務互動，您需要所建立資源的金鑰和端點。 您可以在所建立資源的**金鑰和端點**索引標籤中找到相關資訊。

本文件使用 SQL Database 作為建立第一個監視器的範例。

## <a name="sign-in-to-your-workspace"></a>登入您的工作區

部署和建立您的資源之後，請登入 [Metrics Advisor 入口網站](https://go.microsoft.com/fwlink/?linkid=2143774)。 選取您的工作區以開始監視您的計量。 
 
目前，您可以在每個可用的區域建立一個 Metrics Advisor 資源。 您可以隨時在 Metrics Advisor 入口網站中切換工作區。


## <a name="onboard-time-series-data"></a>將時間序列資料上線

Metrics Advisor 會針對不同的資料來源 (例如 SQL Database、Azure 資料總管和 Azure 表格儲存體) 提供連接器。 不同連接器的連線資料步驟很類似，雖然某些組態參數可能不同。 如需特定資料來源的必要參數，請參閱[從不同的來源連線資料](../data-feeds-from-different-sources.md)。

本快速入門會使用 SQL Database 作為範例。 您也可以依照相同的步驟內嵌自己的資料。

若要開始，請使用您的 Active Directory 帳戶登入 Metrics Advisor 工作區。 從登陸頁面中，選取您剛建立的**目錄**、**訂用帳戶**和**工作區**，然後按一下 [開始使用]。 載入工作負載的主頁面後，從左側功能表中選取 [新增資料摘要]。

### <a name="data-schema-requirements-and-configuration"></a>資料結構描述需求和組態

[!INCLUDE [data schema requirements](../includes/data-schema-requirements.md)]

### <a name="configure-connection-settings"></a>設定連線設定

> [!TIP]
> 如需可用參數的詳細資訊，請參閱[如何新增資料摘要](../how-tos/onboard-your-data.md)。

藉由連線到您的時間序列資料來源來新增資料摘要。 從選取下列參數開始：

* **來源類型**：儲存時間序列資料的資料來源類型。
* **細微性**：時間序列資料中連續資料點之間的間隔，例如每年、每月、每天。 自訂的最低間隔支援為 60 秒。
* **內嵌資料起算時間 (UTC)** ：要內嵌之第一個時間戳記的開始時間。 


接下來，使用您資料來源的認證指定**連接字串**，以及自訂**查詢**。 查詢是用來指定要內嵌的資料，並轉換成所需的結構描述。

[!INCLUDE [query requirements](../includes/query-requirements.md)]

:::image type="content" source="../media/connection-settings.png" alt-text="連線設定" lightbox="../media/connection-settings.png":::


### <a name="verify-the-connection-and-load-the-data-schema"></a>確認連線並載入資料結構描述

建立連接字串和查詢字串後，請選取 [驗證並取得結構描述] 以確認連線，並執行查詢以從資料來源取得您的資料結構描述。 此作業通常需要幾秒鐘，視您的資料來源連線而定。 如果此步驟發生錯誤，請確認：

1. 您的連接字串和查詢是正確的。
2. 如果有防火牆設定，您的 Metrics Advisor 執行個體就能夠連線到資料來源。

### <a name="schema-configuration"></a>結構描述組態

載入資料結構描述 (如下所示) 後，選取適當的欄位。


|選取項目  |描述  |注意  |
|---------|---------|---------|
|**Timestamp**     | 資料點的時間戳記。 如果省略，則在內嵌資料點時，Metrics Advisor 會使用時間戳記。 針對每個資料摘要，您最多可以指定一個資料行作為時間戳記。        | 選擇性。 最多只能指定一個資料行。       |
|**Measure**     |  資料摘要中的數值。 針對每個資料摘要，您可以指定多個量值，但至少應選取一個資料行作為量值。        | 應指定至少一個資料行。        |
|**維度**     | 類別值。 不同值的組合會識別特定的單一維度時間序列，例如：國家/地區、語言、租用戶。 您可以選取無或任意數目的資料行作為維度。 注意：如果您選取非字串資料行作為維度，請謹慎使用維度分解。 | 選擇性。        |
|**忽略**     | 略過選取的資料行。        |         |


:::image type="content" source="../media/schema-configuration.png" alt-text="連線設定" lightbox="../media/schema-configuration.png":::

### <a name="automatic-roll-up-settings"></a>自動積存設定

> [!IMPORTANT]
> 如果要啟用**根本原因分析**和其他診斷功能，則必須設定「自動積存設定」。 啟用之後，就無法變更自動積存設定。

Metrics Advisor 可以在內嵌期間，自動在每個維度上執行彙總 (SUM/MAX/MIN...)，然後建置將用於根本原因分析和其他診斷功能的階層。 如需詳細資訊，請參閱[自動積存設定](../how-tos/onboard-your-data.md#automatic-roll-up-settings)。

為資料摘要提供自訂名稱，這會顯示在您的工作區中。 按一下 [提交]。 

## <a name="tune-detection-configuration"></a>調整偵測組態

新增資料摘要之後，Metrics Advisor 會嘗試從指定的開始日期內嵌計量資料。 資料可能需要一些時間才能完全內嵌，您可以按一下資料摘要頁面頂端的 [內嵌進度] 檢視內嵌狀態。 如果資料已內嵌，Metrics Advisor 會套用偵測，並繼續監視新資料的來源。

套用偵測時，請按一下資料摘要中所列的其中一個計量，以尋找**計量詳細資料頁面**，並： 
- 檢視此計量下所有時間序列配量的視覺效果
- 更新偵測組態以符合預期的結果
- 為偵測到的異常設定通知

:::image type="content" source="../media/metric-details.png" alt-text="連線設定" lightbox="../media/metric-details.png":::

## <a name="view-the-diagnostic-insights"></a>檢視診斷見解

微調偵測組態後，發現的異常應該反映出資料中的實際異常狀況。 Metrics Advisor 會執行多維度計量的分析，例如異常叢集、事件相互關聯和根本原因分析。 使用這些功能來分析和診斷資料中的事件。

若要檢視診斷深入解析，請按一下時間序列視覺效果上的紅色點，這代表偵測到的異常。 隨即會出現一個視窗，其中包含事件分析頁面的連結。 

:::image type="content" source="../media/incident-link.png" alt-text="連線設定" lightbox="../media/incident-link.png":::

按一下連結後，您將會切換到事件分析頁面，系統會使用一些診斷深入解析來分析對應的異常。 頁面頂端會顯示事件的相關統計資料，例如**嚴重性**、**相關的異常**，以及受影響的**開始時間**和**結束時間**。 

接下來，您會看到事件的上階異常，以及自動化的根本原因建議。 這項自動化的根本原因建議是藉由分析所有相關異常的事件樹狀結構所產生，包括對父系異常的偏差、散佈和比重。 

:::image type="content" source="../media/incident-diagnostic.png" alt-text="連線設定" lightbox="../media/incident-diagnostic.png":::

根據這些功能，您已經可以直接看到發生的狀況以及事件帶來的影響，還有最可能的根本原因。 因此您可以採取立即動作儘快解決事件。 

但是，您也可以透過更多的診斷深入解析，利用額外的功能向下切入以深入了解異常、檢視類似的異常，並在計量之間比較。 請在[如何診斷事件](../how-tos/diagnose-incident.md)中尋找更多資訊。 

## <a name="get-notified-when-new-anomalies-are-found"></a>在發現新的異常時收到通知

如果要在資料中偵測到異常時收到警示，您可以為一或多個計量建立訂用帳戶。 Metrics Advisor 會使用勾點來傳送警示。 支援的勾點類型有三種：電子郵件勾點、網路勾點和 Azure DevOps。 我們會使用網頁勾點作為範例。 

### <a name="create-a-web-hook"></a>建立 Webhook

Webhook 是一種進入點，可讓您以程式設計方式從 Metrics Advisor 服務得知異常狀況；當異常狀況觸發警示時，會呼叫使用者提供的 API。如需有關如何建立勾點的詳細資訊，請參閱**建立勾點**一節，位於：[操作說明：使用勾點來設定警示並取得通知](../how-tos/alerts.md#create-a-hook)。 

### <a name="configure-alert-settings"></a>設定警示設定

建立勾點之後，警示設定會決定應該如何傳送警示通知，以及應該傳送哪個警示通知。 您可以為每個計量設定多個警示設定。 有兩個重要的設定分別為**警示納入範圍** (指定要包含的異常)，以及**篩選異常選項** (以定義要包含在警示中的異常)。 請參閱**新增或編輯警示設定**一節，位於[操作說明：使用掛勾來設定警示並取得通知](../how-tos/alerts.md#add-or-edit-alert-settings)以取得詳細資料。


## <a name="next-steps"></a>後續步驟

- [將資料摘要上線](../how-tos/onboard-your-data.md)
    - [管理資料摘要](../how-tos/manage-data-feeds.md)
    - [不同資料來源的組態](../data-feeds-from-different-sources.md)
- [使用 REST API 或用戶端程式庫](rest-api.md)
- [設定計量和微調偵測組態](../how-tos/configure-metrics.md)
