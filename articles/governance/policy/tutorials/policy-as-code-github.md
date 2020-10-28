---
title: 教學課程：使用 GitHub 實作 Azure 原則即程式碼
description: 在本教學課程中，您會實作可搭配匯出功能、GitHub 動作和 GitHub 工作流程的「Azure 原則即程式碼」工作流程
ms.date: 10/20/2020
ms.topic: tutorial
ms.openlocfilehash: 76a46adc3fc8efab4f7a2d6e656e83c2537dd037
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92325850"
---
# <a name="tutorial-implement-azure-policy-as-code-with-github"></a>教學課程：使用 GitHub 實作 Azure 原則即程式碼

「Azure 原則即程式碼」工作流程可讓您以程式碼的形式管理您的原則定義和指派、控制更新這些定義的生命週期，以及自動驗證合規性結果。 在本教學課程中，您將了解如何搭配使用 Azure 原則功能與 GitHub 來建置生命週期流程。 這些工作包括：

> [!div class="checklist"]
> - 將原則定義和指派匯出至 GitHub
> - 將 GitHub 中已更新的原則物件推送至 Azure
> - 從 GitHub 動作觸發合規性掃描

如果您想要指派原則以識別現有資源的目前合規性狀態，快速入門文章會說明如何執行這項操作。

## <a name="prerequisites"></a>必要條件

- 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。
- 請參閱[將 Azure 原則設計為程式碼工作流程](../concepts/policy-as-code.md)，以了解本教學課程中所使用的設計模式。

### <a name="export-azure-policy-objects-from-the-azure-portal"></a>從 Azure 入口網站匯出 Azure 原則物件

若要從 Azure 入口網站匯出原則定義，請遵循下列步驟：

1. 藉由按一下 [所有服務] 然後搜尋並選取 [原則]，在 Azure 入口網站中啟動 Azure 原則服務。

1. 選取 Azure 原則分頁左側的 [定義]。

1. 使用 [匯出定義] 按鈕，或選取原則定義資料列上的省略符號，然後選取 [匯出定義]。

1. 選取 [以 GitHub 登入] 按鈕。 如果您尚未向 GitHub 進行驗證以授權 Azure 原則來匯出資源，請在開啟的新視窗中，檢查 [GitHub 動作](https://github.com/features/actions)需要的存取權，然後選取 [授權 AzureGitHubActions] 以繼續匯出程序。 完成之後，新視窗會自行關閉。

1. 在 [基本資料] 索引標籤上設定下列選項，然後選取 [原則] 索引標籤或 [下一步：原則] 按鈕 (位於頁面底部)。

   - **存放庫篩選器** ：設定為 [我的存放庫] 來僅查看您擁有的存放庫，或設定為 [所有存放庫] 來查看您授與 GitHub 動作存取權的所有存放庫。
   - **存放庫：** 將設定為您想要匯出 Azure 原則資源的存放庫。
   - **分支** ：設定存放庫中的分支。 使用預設以外的分支，是再進一步合併至原始程式碼之前，用來驗證更新的好方法。
   - **目錄** ：將 Azure 原則資源匯出到其中的「根層級資料夾」。 此目錄下的子資料夾會根據匯出的資源來建立。

1. 在 [原則] 索引標籤上，選取省略符號並挑選管理群組、訂用帳戶或資源群組的組合，以設定要搜尋的範圍。
   
1. 使用 [新增原則定義] 按鈕，搜尋要匯出的物件範圍。 在開啟的側邊視窗中，選取要匯出的每個物件。 依據搜尋方塊或類型來篩選選取範圍。 選取要匯出的所有物件之後，請使用頁面底部的 [新增] 按鈕。

1. 針對每個選取的物件，選取原則定義所需的匯出選項，例如 [僅定義] 或 [定義和指派]。 然後選取 [檢閱 + 匯出] 索引標籤或 [下一步：檢閱 + 匯出] 按鈕 (位於頁面底部)。

   > [!NOTE]
   > 如果選擇 [定義和指派] 選項，則只會匯出在新增原則定義時，篩選準則所設範圍內的原則指派。

1. 在 [檢閱 + 匯出] 索引標籤上，檢查詳細資料是否相符，然後使用頁面底部的 [匯出] 按鈕。

1. 檢查您的 GitHub 存放庫、分支和「根層級資料夾」，您會看到選取的資源現在已匯出至您的原始檔控制。

Azure 原則資源會匯出到所選 GitHub 存放庫和「根層級資料夾」內的下列結構：

```text
|
|- <root level folder>/  ________________ # Root level folder set by Directory property
|  |- policies/  ________________________ # Subfolder for policy objects
|     |- <displayName>_<name>____________ # Subfolder based on policy displayName and name properties
|        |- policy.json _________________ # Policy definition
|        |- assign.<displayName>_<name>__ # Each assignment (if selected) based on displayName and name properties
|
```

### <a name="push-policy-objects-updated-in-github-to-azure"></a>將 GitHub 中已更新的原則物件推送至 Azure

1. 匯出原則物件時，名為 `.github/workflows/manage-azure-policy-<randomLetters>.yml` 的 [GitHub 工作流程](https://docs.github.com/en/actions/configuring-and-managing-workflows/configuring-a-workflow#about-workflows)檔案也會隨之建立，以便您開始使用。

   > [!NOTE]
   > 每次使用匯出時，都會建立 GitHub 工作流程檔案。 檔案的每個執行個體都會專屬於該匯出動作期間的選項。

1. 此工作流程檔案會使用[管理 Azure 原則](https://github.com/marketplace/actions/manage-azure-policy)動作，將 GitHub 存放庫中對匯出原則物件所做的變更推送回 Azure 原則。 根據預設，該動作只會考慮並同步與 Azure 中現有檔案不同的檔案。 您也可以在動作中使用 `assignments` 參數，只同步處理對特定指派檔案所做的變更。 此參數只能用來對特定環境套用原則指派。 如需詳細資訊，請參閱[管理 Azure 原則存放庫的讀我檔案](https://github.com/Azure/manage-azure-policy)。

1. 根據預設，工作流程必須手動觸發。 若要這麼做，請使用 GitHub 中的 [動作]，選取 `manage-azure-policy-<randomLetters>` 工作流程、選取 [執行工作流程]，然後再次選取 [執行工作流程]。

   :::image type="content" source="../media/policy-as-code-github/manually-trigger-workflow.png" alt-text="GitHub Web 介面中 [動作] 索引標籤、[工作流程] 和 [執行工作流程] 按鈕的螢幕擷取畫面。":::

   > [!NOTE]
   > 工作流程檔案必須位於預設分支中，才能加以偵測並以手動方式執行。

1. 工作流程會將原則物件的變更與 Azure 同步，並提供記錄中的狀態。

   :::image type="content" source="../media/policy-as-code-github/workflow-logging.png" alt-text="GitHub Web 介面中 [動作] 索引標籤、[工作流程] 和 [執行工作流程] 按鈕的螢幕擷取畫面。":::

1. 工作流程也會在 Azure 原則物件 (`properties.metadata`) 中新增詳細資料，以便您追蹤。

   :::image type="content" source="../media/policy-as-code-github/updated-definition-metadata.png" alt-text="GitHub Web 介面中 [動作] 索引標籤、[工作流程] 和 [執行工作流程] 按鈕的螢幕擷取畫面。":::

### <a name="trigger-compliance-scans-using-github-action"></a>使用 GitHub 動作觸發合規性掃描

透過使用 [Azure 原則合規性掃描動作](https://github.com/marketplace/actions/azure-policy-compliance-scan)，您可以在一或多個資源、資源群組或訂用帳戶上，從 [GitHub 工作流程](https://docs.github.com/en/actions/configuring-and-managing-workflows/configuring-a-workflow#about-workflows)觸發隨選合規性評估掃描，並根據這些資源的合規性狀態來修改工作流程路徑。 您也可以將工作流程設定為在排定的時間執行，以在方便的時間取得最新的合規性狀態。 (選擇性) 此 GitHub 動作也可以針對掃描資源的合規性狀態產生報告，以供進一步分析或封存。

下列範例會執行訂用帳戶的合規性掃描。 

```yaml

on:
  schedule:    
    - cron:  '0 8 * * *'  # runs every morning 8am
jobs:
  assess-policy-compliance:    
    runs-on: ubuntu-latest
    steps:         
    - name: Login to Azure
      uses: azure/login@v1
      with:
        creds: ${{secrets.AZURE_CREDENTIALS}} 

    
    - name: Check for resource compliance
      uses: azure/policy-compliance-scan@v0
      with:
        scopes: |
          /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

```

## <a name="review"></a>檢閱

在本教學課程中，您已成功完成下列工作：

> [!div class="checklist"]
> - 已將原則定義和指派匯出至 GitHub
> - 已將 GitHub 中更新的原則物件推送至 Azure
> - 已從 GitHub 動作觸發合規性掃描

## <a name="next-steps"></a>後續步驟

若要深入了解原則定義的結構，請閱讀這篇文章：

> [!div class="nextstepaction"]
> [Azure 原則定義結構](../concepts/definition-structure.md)