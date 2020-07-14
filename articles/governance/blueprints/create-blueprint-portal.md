---
title: 快速入門：在入口網站中建立藍圖
description: 在本快速入門中，您將在 Azure 入口網站中使用 Azure 藍圖建立、定義和部署成品。
ms.date: 03/25/2020
ms.topic: quickstart
ms.openlocfilehash: dfd0aeb22801776fc9effdf8d0418a9c9b6ab802
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045207"
---
# <a name="quickstart-define-and-assign-a-blueprint-in-the-portal"></a>快速入門：在入口網站中定義和指派藍圖

在了解如何建立及指派藍圖後，您即可根據 Resource Manager 範本 (ARM 範本)、原則、安全性等要素定義常用模式，以開發可重複使用並可快速部署的組態。 在本教學課程中，您將了解如何使用 Azure 藍圖在您的組織中處理藍圖的建立、發佈和指派等常見工作。 這些工作包括：

## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free)。

## <a name="create-a-blueprint"></a>建立藍圖

定義合規性標準模式的第一個步驟，即是以可用的資源規劃藍圖。 在此範例中，請建立名為 **MyBlueprint** 的新藍圖，以設定訂用帳戶的角色和原則指派。 然後，請新增資源群組，並在新的資源群組上建立 Resource Manager 範本和角色指派。

1. 在左側窗格中選取 [所有服務]。 搜尋並選取 [藍圖]。

1. 在頁面左側選取 [藍圖定義]，然後選取頁面頂端的 [+ 建立藍圖] 按鈕。

   或者，在 [開始使用] 頁面中選取 [建立]，以直接建立藍圖。

   :::image type="content" source="./media/create-blueprint-portal/create-blueprint-button.png" alt-text="在藍圖定義頁面中建立藍圖" border="false":::

1. 從內建藍圖清單頂端的卡片中，選取 [從空白藍圖開始]。

1. 提供 [藍圖名稱]，例如 **MyBlueprint**。 (最多可使用 48 個字母和數字，但不含空格或特殊字元)。 暫時將 [藍圖描述] 保留為空白。

1. 在 [定義位置] 方塊中，選取右側的省略符號，選取您要從中儲存藍圖的[管理群組](../management-groups/overview.md)或訂用帳戶，然後選擇 [選取]。

1. 確認資訊正確無誤。 [藍圖名稱] 和 [定義位置] 欄位無法在之後變更。 然後，選取 **[下一步：成品]** 或頁面頂端的 [成品] 索引標籤。

1. 新增訂用帳戶層級的角色指派：

   1. 選取 [訂用帳戶] 下方的 [+ 新增成品] 資料列。 [新增成品] 視窗會在瀏覽器右側開啟。

   1. 選取 [角色指派] 作為 [成品類型]。

   1. 在 [角色] 底下，選取 [參與者]。 在 [新增使用者、應用程式或群組] 方塊中，勾選表示動態參數的核取方塊。

   1. 選取 [新增] 以將此成品新增至藍圖。

   :::image type="content" source="./media/create-blueprint-portal/add-role-assignment.png" alt-text="藍圖成品的角色指派" border="false":::

   > [!NOTE]
   > 大部分的成品均支援參數。 在藍圖建立期間獲得指派值的參數，是_靜態參數_。 如果是在藍圖指派期間受指派的參數，則為_動態參數_。 如需詳細資訊，請參閱[藍圖參數](./concepts/parameters.md)。

1. 新增訂用帳戶層級的原則指派：

   1. 選取角色指派成品下方的 [+ 新增成品] 資料列。

   1. 選取 [原則指派] 作為 [成品類型]。

   1. 將 [類型] 變更為 [內建]。 在 [搜尋] 中，輸入**標記**。

   1. 按一下 [搜尋] 進行篩選。 選取 [將標籤即其預設值附加至資源群組]。

   1. 選取 [新增] 以將此成品新增至藍圖。

1. 選取 [將標記及其預設值附加至資源群組] 原則指派的資料列。

1. 在藍圖定義中提供成品參數的視窗隨即開啟，可用來根據此藍圖設定所有指派的參數 (靜態參數)，而不需要在指派期間設定 (動態參數)。 此範例會在藍圖指派期間使用動態參數，因此請保留預設值，並選取 [取消]。

1. 新增訂用帳戶層級的資源群組：

   1. 選取 [訂用帳戶] 下方的 [+ 新增成品] 資料列。

   1. 選取 [資源群組] 作為 [成品類型]。

   1. 將 [成品顯示名稱]、[資源群組名稱] 和 [位置] 方塊保留為空白，但請務必勾選每個參數屬性的核取方塊，將其設為動態參數。

   1. 選取 [新增] 以將此成品新增至藍圖。

1. 在資源群組下新增範本：

   1. 選取 [資源群組] 項目下方的 [+ 新增成品] 資料列。

   1. 選取 [Azure Resource Manager 範本] 作為 [成品類型]，並將 [成品顯示名稱] 設為 [儲存體帳戶]，將 [描述] 保留為空白。

   1. 在編輯器方塊的 [範本] 索引標籤中，貼上下列 ARM 範本。 貼上範本後，請選取 [參數] 索引標籤，並請留意已偵測到範本參數 **storageAccountType** 和**位置**。 系統會自動偵測並填入每個參數，但將其設定為動態參數。

      > [!IMPORTANT]
      > 如果要匯入範本，請確定檔案僅有 JSON，不含 HTML。 指向 GitHub 上的 URL 時，請確定您是選取 [RAW] 以取得純 JSON 檔案，而不是以 HTML 包裝以顯示於 GitHub 上的檔案。 如果匯入的範本並非單純的 JSON，則會發生錯誤。

      ```json
      {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
              "storageAccountType": {
                  "type": "string",
                  "defaultValue": "Standard_LRS",
                  "allowedValues": [
                      "Standard_LRS",
                      "Standard_GRS",
                      "Standard_ZRS",
                      "Premium_LRS"
                  ],
                  "metadata": {
                      "description": "Storage Account type"
                  }
              },
              "location": {
                  "type": "string",
                  "defaultValue": "[resourceGroup().location]",
                  "metadata": {
                      "description": "Location for all resources."
                  }
              }
          },
          "variables": {
              "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
          },
          "resources": [{
              "type": "Microsoft.Storage/storageAccounts",
              "name": "[variables('storageAccountName')]",
              "location": "[parameters('location')]",
              "apiVersion": "2018-07-01",
              "sku": {
                  "name": "[parameters('storageAccountType')]"
              },
              "kind": "StorageV2",
              "properties": {}
          }],
          "outputs": {
              "storageAccountName": {
                  "type": "string",
                  "value": "[variables('storageAccountName')]"
              }
          }
      }
      ```

   1. 清除 **storageAccountType** 核取方塊，並留意下拉式清單中僅包含 ARM 範本的 **allowedValues** 下方所含的值。 選取方塊，將其設回動態參數。

   1. 選取 [新增] 以將此成品新增至藍圖。

   :::image type="content" source="./media/create-blueprint-portal/add-resource-manager-template.png" alt-text="藍圖成品的 Resource Manager 範本" border="false":::

1. 完成的藍圖應該如下所示： 請注意，每個成品的 [參數] 資料行中都會出現**已填入 _x_ 個參數，共 _y_ 個**。 在每次指派藍圖時都會設定動態參數。

   :::image type="content" source="./media/create-blueprint-portal/completed-blueprint.png" alt-text="已完成的藍圖定義 #2" border="false":::

1. 新增所有規劃的成品之後，請選取頁面底部的 [儲存草稿]。

## <a name="edit-a-blueprint"></a>編輯藍圖

在[建立藍圖](#create-a-blueprint)時，您並未提供描述或將角色指派新增至新的資源群組。 您可以依照下列步驟加以修正：

1. 在頁面左側選取 [藍圖定義]。

1. 在藍圖清單中，以滑鼠右鍵按一下您先前建立的藍圖，然後選取 [編輯藍圖]。

1. 在 [藍圖描述] 中，提供藍圖及其構成成品的相關資訊。 在此案例中，請輸入類似如下的內容：**此藍圖設定的是訂用帳戶的標記原則和角色指派，且會建立資源群組，並將資源範本和角色指派部署到該資源群組。**

1. 選取 [下一步：**成品]** 或頁面頂端的 [成品] 索引標籤。

1. 在資源群組下新增角色指派：

   1. 直接在 [資源群組] 項目下方選取 [+ 新增成品] 資料列。

   1. 選取 [角色指派] 作為 [成品類型]。

   1. 在 [角色] 下方選取 [擁有者]，並清除 [新增使用者、應用程式或群組] 方塊下方的核取方塊。

   1. 搜尋並選取要新增的使用者、應用程式或群組。 此成品會在每次指派此藍圖時使用設定相同的靜態參數。

   1. 選取 [新增] 以將此成品新增至藍圖。

   :::image type="content" source="./media/create-blueprint-portal/add-role-assignment-2.png" alt-text="藍圖成品的第二個角色指派" border="false":::

1. 完成的藍圖應該如下所示： 請注意，新增的角色指派會顯示**已填入 1 個參數，共 1 個**。 這表示它是靜態參數。

   :::image type="content" source="./media/create-blueprint-portal/completed-blueprint-2.png" alt-text="已完成藍圖的第二個定義" border="false":::

1. 完成更新後，請選取 [儲存草稿]。

## <a name="publish-a-blueprint"></a>發佈藍圖

將所有規劃好的成品新增到藍圖之後，即可發佈藍圖。
發佈之後，藍圖即可指派給訂用帳戶。

1. 在頁面左側選取 [藍圖定義]。

1. 在藍圖清單中，以滑鼠右鍵按一下您先前建立的藍圖，然後選取 [發佈藍圖]。

1. 在開啟的窗格中，提供 [版本] (長度上限為 20 個字元的字母、數字和連字號)，例如 **v1**。 選擇性地在 [變更附註] 中輸入文字，例如**首次發佈**。

1. 選取頁面底部的 [發佈]。

## <a name="assign-a-blueprint"></a>指派藍圖

藍圖在發佈後，即可指派給訂用帳戶。 將您建立的藍圖指派給管理群組階層下的其中一個訂用帳戶。 如果將藍圖儲存到某訂用帳戶，則只能將其指派給該訂用帳戶。

1. 在頁面左側選取 [藍圖定義]。

1. 在藍圖清單中，以滑鼠右鍵按一下您先前建立的藍圖 (或選取省略符號)，然後選取 [指派藍圖]。

1. 在 [指派藍圖] 頁面的 [訂用帳戶] 下拉式清單中，選取要部署此藍圖的訂用帳戶。

   如果 [Azure 計費](../../cost-management-billing/index.yml)中有支援的企業供應項目，則會在 [訂用帳戶] 方塊下方啟動 [新建] 連結。 請遵循下列步驟：

   1. 選取 [新建] 以建立新的訂用帳戶，而不選取現有的訂用帳戶。

   1. 提供新訂用帳戶的**顯示名稱**。

   1. 從下拉式清單中選取可用的**供應項目**。

   1. 使用省略符號選取訂用帳戶將成為其子系的[管理群組](../management-groups/overview.md)。

   1. 選取頁面底部的 [建立]。

      :::image type="content" source="./media/create-blueprint-portal/assignment-create-subscription.png" alt-text="建立訂用帳戶作為藍圖指派訂用帳戶" border="false":::

      > [!IMPORTANT]
      > 選取 [建立] 後，會立即建立新的訂用帳戶。

   > [!NOTE]
   > 您選取的每個訂用帳戶均會建立指派。 您可在稍後變更單一訂用帳戶的指派，而不需強制變更其餘選取的訂用帳戶。

1. 提供此指派的唯一名稱，作為 [指派名稱]。

1. 在 [位置] 中，選取將建立受控識別的區域和訂用帳戶部署物件。 Azure 藍圖會使用此受控識別來部署指派的藍圖之中的所有成品。 若要深入了解，請參閱[適用於 Azure 資源的受控識別](../../active-directory/managed-identities-azure-resources/overview.md)。

1. 請將 **v1** 項目上**已發佈**版本的 [藍圖定義版本] 下拉式清單選項保留原樣。 (預設值是最新發佈的版本)。

1. [鎖定指派] 請保留 [不要鎖定] 的預設值。 如需詳細資訊，請參閱[藍圖資源鎖定](./concepts/resource-locking.md)。

   :::image type="content" source="./media/create-blueprint-portal/assignment-locking-mi.png" alt-text="指派的鎖定和受控識別" border="false":::

1. 在 [受控識別] 下，[系統指派]保留預設值。

1. 對於訂用帳戶層級角色指派 **[使用者群組或應用程式名稱]: 參與者**，請搜尋並選取使用者、應用程式或群組。

1. 對於訂用帳戶層級的原則指派，請將 [標記名稱] 設為 **CostCenter**，並將 [標記值] 設為 **ContosoIT**。

1. 對於 **ResourceGroup**，請從下拉式清單中提供 **StorageAccount** 的 [名稱] 和 [美國東部 2] 的 [位置]。

   > [!NOTE]
   > 您在藍圖定義期間新增於資源群組下的每個成品，都應與其部署所需的資源群組或物件相符。
   > 不接受參數的成品，或是在指派時並未定義任何參數的成品，僅會列出內容資訊。

1. 在 ARM 範本 **StorageAccount** 中，選取 **Standard_GRS** 作為 **storageAccountType** 參數。

1. 閱讀頁面底部的 [資訊] 方塊，然後選取 [指派]。

## <a name="track-deployment-of-a-blueprint"></a>追蹤藍圖的部署情況

將藍圖指派到一個或多個訂用帳戶時，會發生兩件事：

- 藍圖會新增至每個訂用帳戶的 [已指派的藍圖] 頁面。
- 藍圖定義的所有成品開始進行部署。

在藍圖被指派到訂用帳戶之後，請確認部署的進度：

1. 選取頁面左側的 [指派的藍圖]。

1. 在藍圖清單中，以滑鼠右鍵按一下您先前指派的藍圖，然後選取 [檢視指派詳細資料]。

   :::image type="content" source="./media/create-blueprint-portal/view-assignment-details.png" alt-text="從 [已指派的藍圖] 頁面中檢視指派詳細資料" border="false":::

1. 在 [藍圖指派] 頁面上，驗證已成功部署所有成品，且部署期間並未出現任何錯誤。 如果發生錯誤，請參閱[疑難排解藍圖](./troubleshoot/general.md)的步驟，判斷發生錯誤的原因。

## <a name="clean-up-resources"></a>清除資源

### <a name="unassign-a-blueprint"></a>取消指派藍圖

如果不再需要某個藍圖指派，請將其從訂用帳戶中移除。 較新的藍圖可以更新後的模式、原則和設計取代此藍圖。 移除藍圖時，會將指派為該藍圖一部份的成品保留下來。 若要移除藍圖指派，請按照下列步驟操作：

1. 選取頁面左側的 [指派的藍圖]。

1. 在藍圖清單中，選取您要取消指派的藍圖。 然後，選取頁面頂端的 [取消指派藍圖] 按鈕。

1. 閱讀確認訊息，然後選取 [確定]。

### <a name="delete-a-blueprint"></a>刪除藍圖

1. 在頁面左側選取 [藍圖定義]。

1. 以滑鼠右鍵按一下您要刪除的藍圖，然後選取 [刪除藍圖]。 然後，選取確認對話方塊中的 [是]。

> [!NOTE]
> 以此方法刪除藍圖，也會將所選藍圖的已發佈版本全數刪除。
> 若要刪除單一版本，請開啟藍圖，選取 [已發佈的版本] 索引標籤，選取要刪除的版本，然後選取 [刪除此版本]。 同樣地，您必須先刪除藍圖定義的所有藍圖指派，才能刪除該藍圖。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已使用 Azure 入口網站建立、指派及移除藍圖。 若要深入了解 Azure 藍圖，請繼續閱讀藍圖生命週期文章。

> [!div class="nextstepaction"]
> [了解藍圖生命週期](./concepts/lifecycle.md)
