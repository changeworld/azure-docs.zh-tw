---
title: 使用 Azure 監視器建立 Azure Cosmos DB 的警示
description: 瞭解如何使用 Azure 監視器設定 Azure Cosmos DB 的警示。
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: how-to
ms.date: 07/16/2020
ms.openlocfilehash: 1042638dc622e6675c997bc6db8df1d072824816
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93099907"
---
# <a name="create-alerts-for-azure-cosmos-db-using-azure-monitor"></a>使用 Azure 監視器建立 Azure Cosmos DB 的警示
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

警示是用來設定週期性測試，以監視 Azure Cosmos DB 資源的可用性和回應性。 警示可透過電子郵件的形式傳送通知給您，或在您的其中一個計量達到閾值或在活動記錄中記錄特定事件時執行 Azure 函數。

您可以根據計量或 Azure Cosmos 帳戶上的活動記錄事件來接收警示：

* **計量** ：當指定的計量值超出您指派的閾值時，就會觸發警示。 例如，當取用的要求單位總數超過 1000 RU/秒。 當第一次符合條件時，以及之後當該條件不再符合時，就會觸發此警示。 請參閱 Azure Cosmos DB 中可用之不同度量的 [監視資料參考](monitor-cosmos-db-reference.md#metrics) 文章。

* **活動記錄事件** –此警示會在發生特定事件時觸發。 例如，當您的 Azure Cosmos 帳戶的金鑰被存取或重新整理時。

您可以從 [Azure Cosmos DB] 窗格或 Azure 入口網站中的 Azure 監視器服務來設定警示。 這兩個介面都提供相同的選項。 本文說明如何使用 Azure 監視器設定 Azure Cosmos DB 的警示。

## <a name="create-an-alert-rule"></a>建立警示規則

本節說明當您收到 HTTP 狀態碼429時，如何建立警示，當要求速率受到限制時，就會收到此警示。 例如，您可能會想要在有100或更多的速率限制要求時收到警示。 本文說明如何使用 HTTP 狀態碼來設定這類案例的警示。 您也可以使用類似的步驟來設定其他類型的警示，只需要根據您的需求選擇不同的條件即可。

1. 登入 [Azure 入口網站。](https://portal.azure.com/)

1. 從左側導覽列中選取 [ **監視** ]，然後選取 [ **警示** ]。

1. 選取 [新增警示規則] 按鈕，以開啟 [建立警示規則] 窗格。  

1. 填寫 [ **領域** ] 區段：

   * 開啟 [ **選取資源** ] 窗格，並設定下列各項：

   * 選擇您的 **訂** 用帳戶名稱。

   * 選取 **資源類型** **Azure Cosmos DB 帳戶** 。

   * 您 Azure Cosmos 帳戶的 **位置** 。

   * 填入詳細資料之後，會顯示所選範圍中的 Azure Cosmos 帳戶清單。 選擇您要設定警示的帳戶，然後選取 [ **完成** ]。

1. 填寫 [ **條件** ] 區段：

   * 開啟 [ **選取條件** ] 窗格以開啟 [ **設定信號邏輯** ] 頁面，並設定下列各項：

   * 選取一個訊號。 **信號類型** 可以是 **度量** 或 **活動記錄** 。 選擇此案例的 **度量** 。 因為您想要在要求單位計量總計有速率限制問題時收到警示。

   * 全 **選****監視服務**

   * 選擇 **信號名稱** 。 若要取得 HTTP 狀態碼的警示，請選擇 [ **要求單位總數** ] 信號。

   * 在下一個索引標籤中，您可以定義觸發警示的邏輯，並使用圖表來查看 Azure Cosmos 帳戶的趨勢。 **要求單位度量總計** 支援維度。 這些維度可讓您篩選度量。 如果您未選取任何維度，則會忽略此值。

   * 選擇 [ **StatusCode** ] 作為 **維度名稱** 。 選取 [ **新增自訂值** ]，並將狀態碼設定為429。

   * 在 **警示邏輯** 中，將 **閾值** 設定為 **靜態** 。 靜態閾值會使用使用者定義的臨界值來評估規則，而動態閾值則使用內建的機器學習演算法來持續學習計量行為模式，並自動計算閾值。

   * 將 **運算子** 設定為 [ **大於** ]，將 [匯總 **類型** ] 設定為 [ **總計** ]，將 [ **閾值** ] 設定為 [ **100** ]。 使用此邏輯，如果您的用戶端看到超過100個具有429狀態碼的要求，就會觸發警示。 您也可以根據您的需求，設定匯總類型、匯總細微性和評估頻率。

   * 填滿表單之後，請選取 [ **完成** ]。 下列螢幕擷取畫面顯示警示邏輯的詳細資料：

     :::image type="content" source="./media/create-alerts/configure-alert-logic.png" alt-text="設定邏輯以接收速率有限/429 要求的警示":::

1. 填寫 [ **動作群組** ] 區段：

   * 在 [ **建立規則** ] 窗格中，選取現有的動作群組，或建立新的動作群組。 動作群組可讓您定義警示條件發生時所要採取的動作。 針對此範例，請建立新的動作群組，以在觸發警示時收到電子郵件通知。 開啟 [ **新增動作群組** ] 窗格，並填寫下列詳細資料：

   * **動作組名** -動作組名在資源群組內必須是唯一的。

   * **簡短名稱** -動作群組的簡短名稱，此值會包含在電子郵件和 SMS 通知中，以識別哪個動作群組是通知的來源。

   * 選擇將在其中建立此動作群組的訂用帳戶和資源群組。  

   * 提供您的動作名稱，並選取 [ **電子郵件/SMS 訊息/推播/語音** ] 作為 **動作類型** 。 下列螢幕擷取畫面顯示動作類型的詳細資料：

     :::image type="content" source="./media/create-alerts/configure-alert-action-type.png" alt-text="設定邏輯以接收速率有限/429 要求的警示":::

1. 填寫 [ **警示規則詳細資料** ] 區段：

   * 定義規則的名稱、提供選擇性的描述、警示的嚴重性層級、選擇是否要在建立規則時啟用規則，然後選取 [ **建立規則警示** ] 以建立計量規則警示。

建立警示之後，就會在10分鐘內啟用。

## <a name="common-alerting-scenarios"></a>常見的警示案例

以下是您可以使用警示的一些案例：

* 當 Azure Cosmos 帳戶的金鑰更新時。
* 當容器、資料庫或區域的資料或索引使用量超過特定位元組數目時。
* 當正規化的 RU/秒耗用量超過特定百分比時。 正規化的 RU 耗用量度量可提供複本集內的最大輸送量使用率。 若要瞭解，請參閱 [如何監視正規化的 RU/s](monitor-normalized-request-units.md) 文章。  
* 新增、移除區域或離線時。
* 建立、刪除或更新資料庫或容器時。
* 當資料庫或容器的輸送量變更時。

## <a name="next-steps"></a>下一步

* 如何監視 Azure Cosmos 容器中的 [正規化 RU/s 度量](monitor-normalized-request-units.md) 。
* 如何在 Azure Cosmos DB 中監視作業的 [輸送量或要求單位使用量](monitor-request-unit-usage.md) 。