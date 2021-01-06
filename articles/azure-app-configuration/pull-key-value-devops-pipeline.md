---
title: 使用 Azure Pipelines 提取 settingsfromo 應用程式設定
description: 瞭解如何使用 Azure Pipelines 將索引鍵/值提取至應用程式設定存放區
services: azure-app-configuration
author: drewbatgit
ms.service: azure-app-configuration
ms.topic: how-to
ms.date: 11/17/2020
ms.author: drewbat
ms.openlocfilehash: fbe517c766b3835bf4265a1309b8737a25925b7c
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97914955"
---
# <a name="pull-settings-to-app-configuration-with-azure-pipelines"></a>使用 Azure Pipelines 將設定提取至應用程式設定

[Azure 應用程式組態](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task)工作會從您的應用程式設定存放區提取索引鍵/值，並將其設定為 Azure 管線變數，以供後續工作取用。 這項工作可補充將從設定檔中的索引鍵/值推送到應用程式設定存放區的 [Azure 應用程式組態推送](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push) 工作。 如需詳細資訊，請參閱 [使用 Azure Pipelines 將設定推送至應用程式設定](push-kv-devops-pipeline.md)。

## <a name="prerequisites"></a>必要條件

- Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)
- 應用程式設定存放區-在 [Azure 入口網站](https://portal.azure.com)中免費建立一個。
- Azure DevOps 專案- [免費建立一個](https://go.microsoft.com/fwlink/?LinkId=2014881)
- Azure 應用程式組態工作-從 [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task#:~:text=Navigate%20to%20the%20Tasks%20tab,the%20Azure%20App%20Configuration%20instance.)免費下載。  

## <a name="create-a-service-connection"></a>建立服務連線

[服務連接](/azure/devops/pipelines/library/service-endpoints)可讓您從 Azure DevOps 專案存取 Azure 訂用帳戶中的資源。

1. 在 Azure DevOps 中，移至包含目標管線的專案，然後開啟左下方的 **專案設定** 。
1. 在 [ **管線** ] 下，選取 [ **服務連接**]。
1. 如果您沒有任何現有的服務連接，請按一下畫面中間的 [ **建立服務連接** ] 按鈕。 否則，請按一下頁面右上方的 [ **新增服務連接** ]。
1. 選取 **Azure Resource Manager**。
1. 選取 **服務主體 (自動)**。
1. 填寫您的訂用帳戶和資源。 為您的服務連接提供名稱。

現在您已建立服務連線，請尋找指派給它的服務主體名稱。 在下一個步驟中，您將會在此服務主體中新增角色指派。

1. 移至 [**專案設定**  >  **服務連接**]。
1. 選取您在上一節中建立的服務連接。
1. 選取 [ **管理服務主體**]。
1. 請注意列出的 **顯示名稱** 。

## <a name="add-role-assignment"></a>新增角色指派

將適當的應用程式設定角色指派給在工作中使用的服務連線，讓工作可以存取應用程式設定存放區。

1. 流覽至您的目標應用程式設定存放區。 如需設定應用程式設定存放區的逐步解說，請參閱其中一個 Azure 應用程式組態快速入門中的 [建立應用程式設定存放區](/azure/azure-app-configuration/quickstart-dotnet-core-app#create-an-app-configuration-store) 。
1. 選取左側的 [ **存取控制] (IAM)**。
1. 在頂端，選取 [ **+ 新增** ]，然後選擇 [ **新增角色指派**]。
1. 在 [ **角色**] 底下，選取 [ **應用程式設定資料讀取器**]。 此角色可讓工作從應用程式設定存放區讀取。 
1. 選取與您在上一節中建立的服務連接相關聯的服務主體。

> [!NOTE]
> 若要解析應用程式設定內的 Azure Key Vault 參考，服務連線也必須被授與讀取所參考 Azure 金鑰保存庫中秘密的許可權。
  
## <a name="use-in-builds"></a>在組建中使用

本節將討論如何在 Azure DevOps 組建管線中使用 Azure 應用程式組態工作。

1. 按一下 [**管線** 管線]，流覽至組建管線頁面  >  ****。 如需建立管線檔，請參閱  [建立您的第一個管線](/azure/devops/pipelines/create-first-pipeline?view=azure-devops&tabs=net%2Ctfs-2018-2%2Cbrowser)。
      - 如果您要建立新的組建管線，請按一下 [ **新增管線**]，選取管線的儲存機制。 選取管線右邊的 [ **顯示助理** ]，並搜尋 **Azure 應用程式組態** 工作。
      - 如果您是使用現有的組建管線，請選取 [ **編輯** ] 以編輯管線。 **在 [工作] 索引** 標籤中，搜尋 **Azure 應用程式組態** 工作。
1. 設定工作的必要參數，以從應用程式設定存放區提取索引鍵/值。 參數的描述可在下面的 **parameters** 區段和每個參數旁的工具提示中取得。
      - 將 **Azure 訂** 用帳戶參數設定為您在上一個步驟中建立的服務連線名稱。
      - 將 **應用程式設定名稱** 設定為應用程式設定存放區的資源名稱。
      - 保留其餘參數的預設值。
1. 儲存組建並將其排到佇列。 組建記錄檔會顯示在工作執行期間發生的任何失敗。

## <a name="use-in-releases"></a>在發行中使用

本節將討論如何在 Azure DevOps 發行管線中使用 Azure 應用程式組態工作。

1. 選取 **管線** 版本，以流覽至發行管線頁面  >  ****。 如需發行管線檔，請參閱 [發行管線](/azure/devops/pipelines/release?view=azure-devops)。
1. 選擇現有的發行管線。 如果您沒有帳戶，請按一下 [ **新增管線** ] 建立新的管線。
1. 選取右上角的 [ **編輯** ] 按鈕，以編輯發行管線。
1. 選擇要加入工作的 **階段** 。 如需階段的詳細資訊，請參閱 [新增階段、相依性、& 條件](/azure/devops/pipelines/release/environments?view=azure-devops)。
1. 按一下 [在 **+** 代理程式上執行]，然後在 [**新增** 工作] 索引標籤下新增 **Azure 應用程式組態** 工作。
1. 在工作中設定必要的參數，以從您的應用程式設定存放區提取您的索引鍵/值。 參數的描述可在下面的 **parameters** 區段和每個參數旁的工具提示中取得。
      - 將 **Azure 訂** 用帳戶參數設定為您在上一個步驟中建立的服務連線名稱。
      - 將 **應用程式設定名稱** 設定為應用程式設定存放區的資源名稱。
      - 保留其餘參數的預設值。
1. 儲存並將發行排在佇列中。 發行記錄檔將會顯示工作執行期間發生的任何失敗。

## <a name="parameters"></a>參數

Azure 應用程式組態工作會使用下列參數：

- **Azure 訂** 用帳戶：一個下拉式清單，其中包含可用的 Azure 服務連線。 若要更新並重新整理可用的 Azure 服務連線清單，請按文字方塊右邊的 [重新整理 **Azure 訂** 用帳戶] 按鈕。
- **應用程式設定名稱**：在選取的訂用帳戶下載入可用設定存放區的下拉式清單。 若要更新並重新整理可用設定存放區的清單，請按文字方塊右邊的 [重新整理 **應用程式設定名稱** ] 按鈕。
- 索引 **鍵篩選**：篩選準則可用來選取 Azure 應用程式組態要求的索引鍵/值。 * 的值會選取所有索引鍵/值。 如需的詳細資訊，請參閱 [查詢索引鍵值](concept-key-value.md#query-key-values)。
- **標籤**：指定從應用程式設定存放區選取索引鍵/值時，應使用的標籤。 如果未提供任何標籤，則會抓取沒有標籤的索引鍵/值。 不允許使用下列字元：、*。
- **Trim Key Prefix**：指定一或多個前置詞，以在將其設定為變數之前，先從應用程式設定金鑰修剪。 多個前置詞可以用換行字元分隔。

## <a name="use-key-values-in-subsequent-tasks"></a>在後續工作中使用索引鍵/值

從應用程式設定提取的索引鍵/值會設定為管線變數，而這些變數可作為環境變數存取。 環境變數的索引鍵是索引鍵-值的索引鍵，在修剪前置詞之後（如有指定），它會從應用程式設定中取出。

例如，如果後續的工作執行 PowerShell 腳本，它可能會使用索引鍵 ' myBuildSetting ' 的索引鍵/值，如下所示：
```powershell
echo "$env:myBuildSetting"
```
值將會列印到主控台。

> [!NOTE]
> 應用程式設定內的 Azure Key Vault 參考將會解析並設定為 [秘密變數](/azure/devops/pipelines/process/variables#secret-variables)。 在 Azure 管線中，秘密變數會從記錄遮罩。 它們不會以環境變數的形式傳遞至工作，而必須改為傳遞做為輸入。 

## <a name="troubleshooting"></a>疑難排解

如果發生未預期的錯誤，可以藉由將管線變數設定為來啟用偵錯工具記錄 `system.debug` `true` 。

## <a name="faq"></a>常見問題集

**如何? 從多個金鑰和標籤撰寫我的設定？**

有時候可能需要從多個標籤組成設定，例如，預設和開發。 一個管線中可使用多個應用程式設定工作來執行此案例。 工作在後續步驟中所提取的索引鍵/值將會取代先前步驟中的任何值。 在上述範例中，工作可以用來選取具有預設標籤的索引鍵/值，而第二個工作則可以選取具有 dev 標籤的索引鍵/值。 具有 dev 標籤的金鑰會使用預設標籤來覆寫相同的索引鍵。
