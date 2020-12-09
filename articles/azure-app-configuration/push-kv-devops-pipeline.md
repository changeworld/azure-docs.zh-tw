---
title: 使用 Azure Pipelines 將設定推送到應用程式設定
description: 瞭解如何使用 Azure Pipelines 將索引鍵/值推送到應用程式設定存放區
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.topic: how-to
ms.date: 07/27/2020
ms.author: alkemper
ms.openlocfilehash: fd3f7dbfd824360dcba9f8a166c579e3b55527ba
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932110"
---
# <a name="push-settings-to-app-configuration-with-azure-pipelines"></a>使用 Azure Pipelines 將設定推送到應用程式設定

[Azure 應用程式組態 Push](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push)工作會將設定檔中的索引鍵/值推送到您的應用程式設定存放區。 這項工作會啟用管線內的完整圓形功能，因為您現在可以從應用程式設定存放區提取設定，以及將設定推送到應用程式設定存放區。

## <a name="prerequisites"></a>必要條件

- Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)
- 應用程式設定資源-在 [Azure 入口網站](https://portal.azure.com)中免費建立一個。
- Azure DevOps 專案- [免費建立一個](https://go.microsoft.com/fwlink/?LinkId=2014881)
- Azure 應用程式組態推送工作- [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push)免費下載。

## <a name="create-a-service-connection"></a>建立服務連線

[服務連接](/azure/devops/pipelines/library/service-endpoints)可讓您從 Azure DevOps 專案存取 Azure 訂用帳戶中的資源。

1. 在 Azure DevOps 中，移至包含目標管線的專案，然後開啟左下方的 **專案設定** 。
1. 在 [ **管線** ] 下，選取 [ **服務連接** ]，然後選取右上方的 [ **新增服務連接** ]。
1. 選取 **Azure Resource Manager**。
1. 選取 **服務主體 (自動)**。
1. 填寫您的訂用帳戶和資源。 為您的服務連接提供名稱。

現在您已建立服務連線，請尋找指派給它的服務主體名稱。 在下一個步驟中，您將會在此服務主體中新增角色指派。

1. 移至 [**專案設定**  >  **服務連接**]。
1. 選取您在上一節中建立的服務連接。
1. 選取 [ **管理服務主體**]。
1. 請注意列出的 **顯示名稱** 。

## <a name="add-role-assignment"></a>新增角色指派

將適當的應用程式設定角色指派指派給在工作中使用的認證，讓工作可以存取應用程式設定存放區。

1. 流覽至您的目標應用程式設定存放區。 
1. 選取左側的 [ **存取控制] (IAM)**。
1. 在頂端，選取 [ **+ 新增** ]，然後選擇 [ **新增角色指派**]。
1. 在 [ **角色**] 底下，選取 [ **應用程式設定資料擁有** 者]。 此角色可讓工作讀取和寫入應用程式設定存放區。 
1. 選取與您在上一節中建立的服務連接相關聯的服務主體。
  
## <a name="use-in-builds"></a>在組建中使用

本節將說明如何在 Azure DevOps 組建管線中使用 Azure 應用程式組態 Push 工作。

1. 按一下 [**管線** 管線]，流覽至組建管線頁面  >  ****。 您可以在 [這裡](/azure/devops/pipelines/create-first-pipeline?tabs=tfs-2018-2&view=azure-devops)找到組建管線的檔。
      - 如果您要建立新的組建管線，請選取管線右側的 [ **顯示** 小幫手]，然後搜尋 **Azure 應用程式組態推送** 工作。
      - 如果您要使用現有的組建管線， **請在編輯** 管線時流覽至 [工作] 索引標籤，然後搜尋 Azure 應用程式組態的 **推送** 工作。
2. 設定工作的必要參數，以將索引鍵/值從設定檔推送到應用程式設定存放區。 **設定檔路徑** 參數是從檔案存放庫的根目錄開始。
3. 儲存組建並將其排到佇列。 組建記錄檔會顯示在工作執行期間發生的任何失敗。

## <a name="use-in-releases"></a>在發行中使用

本節將說明如何在 Azure DevOps 發行管線中使用 Azure 應用程式組態 Push 工作。

1. 選取 **管線** 版本，以流覽至發行管線頁面  >  ****。 您可以在 [這裡](/azure/devops/pipelines/release?view=azure-devops)找到發行管線的檔。
1. 選擇現有的發行管線。 如果您沒有帳戶，請選取 [ **+ 新增** ] 來建立一個新的。
1. 選取右上角的 [ **編輯** ] 按鈕，以編輯發行管線。
1. 選擇要加入工作的 **階段** 。 您可以在 [這裡](/azure/devops/pipelines/release/environments?view=azure-devops)找到更多有關階段的資訊。
1. **+** 針對該工作選取，然後在 [**部署**] 索引標籤下新增 **Azure 應用程式組態推送** 工作。
1. 在工作中設定必要的參數，以將您的金鑰值從設定檔推送到您的應用程式設定存放區。 參數的說明可在下方的 [ **參數** ] 區段中取得，並在每個參數旁的工具提示中取得。
1. 儲存並將發行排在佇列中。 發行記錄檔將會顯示工作執行期間發生的任何失敗。

## <a name="parameters"></a>參數

應用程式設定推播工作會使用下列參數：

- **Azure 訂** 用帳戶：一個下拉式清單，其中包含可用的 Azure 服務連線。 若要更新並重新整理可用的 Azure 服務連線清單，請按文字方塊右邊的 [重新整理 **Azure 訂** 用帳戶] 按鈕。
- **應用程式設定名稱**：在選取的訂用帳戶下載入可用設定存放區的下拉式清單。 若要更新並重新整理可用設定存放區的清單，請按文字方塊右邊的 [重新整理 **應用程式設定名稱** ] 按鈕。
- **設定檔案路徑**：設定檔的路徑。 您可以流覽您的組建成品，以選取設定檔。 `...`文字方塊右邊的 (按鈕) 。
- **分隔符號**：用來壓平合併 yml 檔案的分隔符號。
- **深度**：即將將 json 和. yml 檔案簡維至其中的深度。
- **前置** 詞：在推送至應用程式設定存放區的每個金鑰開頭附加的字串。
- **標籤**：新增至每個索引鍵/值的字串，做為應用程式設定存放區中的標籤。
- **內容類型**：加入至每個索引鍵/值的字串，做為應用程式設定存放區中的內容類型。
- **標記**：格式為的 JSON 物件 `{"tag1":"val1", "tag2":"val2"}` ，其會定義每個索引鍵/值（推送至您的應用程式設定存放區）所新增的標記。
- 以 **指定的前置詞和標籤刪除存放區中的所有其他 Key-Values**：預設 **值為未** 核取。
  - **Checked**：從設定檔推入新的索引鍵/值之前，會先從應用程式設定存放區中移除所有符合指定前置詞和標籤的索引鍵/值。
  - **未選取**：將設定檔中的所有索引鍵/值推送到應用程式設定存放區，並讓應用程式設定存放區中的所有其他專案保持不變。

填妥必要的參數之後，請執行管線。 指定設定檔中的所有索引鍵/值都會上傳至應用程式設定。

## <a name="troubleshooting"></a>疑難排解

如果發生未預期的錯誤，可以藉由將管線變數設定為來啟用偵錯工具記錄 `system.debug` `true` 。

## <a name="faq"></a>常見問題集

**如何上傳多個設定檔？**

在相同的管線內建立 Azure 應用程式組態 Push 工作的多個實例，以將多個設定檔推送到應用程式設定存放區。

**當我嘗試將索引鍵/值推送至我的設定存放區時，為什麼會收到409錯誤？**

如果工作嘗試移除或覆寫應用程式設定存放區中鎖定的索引鍵/值，就會發生409衝突錯誤訊息。
