---
title: 範本概觀
description: 描述使用 Azure 資源管理器範本部署資源的好處。
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 02602b4d12ae4333c88b352e4c13923d67f2c591
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885730"
---
# <a name="what-are-arm-templates"></a>什麼是 ARM 範本？

隨著遷移到雲,許多團隊採用了敏捷開發方法。 這些團隊會迅速反覆運算。 他們需要反覆將解決方案部署到雲中,並知道他們的基礎架構處於可靠的狀態。 隨著基礎結構成為反覆運算過程的一部分,操作和發展之間的劃分已經消失。 團隊需要通過統一流程管理基礎結構和應用程式代碼。

為了應對這些挑戰,您可以自動部署並使用基礎結構作為代碼的做法。 在代碼中,定義需要部署的基礎結構。 基礎結構代碼將成為專案的一部分。 與應用程式代碼一樣,您將基礎結構代碼存儲在原始程式碼儲存庫中並對其進行版本控制。 團隊中的任何人都可以運行代碼並部署類似的環境。

要將基礎結構作為 Azure 解決方案的代碼來實現,請使用 Azure 資源管理員 (ARM) 範本。 該範本是一個 JavaScript 物件表示法 (JSON) 檔,用於定義專案的基礎結構和配置。 範本會使用宣告式語法，可讓您陳述您要部署的項目，而不需要撰寫一連串程式設計命令來加以建立。 在範本中,指定要部署的資源和這些資源的屬性。

## <a name="why-choose-arm-templates"></a>為什麼選擇 ARM 範本?

如果您試著在將 ARM 樣本與其他基礎結構之一作為代碼服務之間確定,請考慮使用樣本的以下優點:

* **聲明性語法**:ARM 範本允許您創建和部署整個 Azure 基礎結構聲明性。 例如,您不僅可以部署虛擬機器,還可以部署網路基礎結構、儲存系統和您可能需要的任何其他資源。

* **可重複的結果**:在整個開發生命週期中反覆部署您的基礎架構,並確信您的資源以一致的方式部署。 範本是冪等的,這意味著您可以多次部署同一範本,並獲取相同的資源類型,處於相同的狀態。 您可以開發一個表示所需狀態的範本,而不是開發大量單獨的範本來表示更新。

* **業務流程**:您不必擔心排序操作的複雜性。 資源管理員協調相互依賴的資源的部署,以便按正確的順序創建這些資源。 如果可能,資源管理器並行部署資源,以便部署完成比串列部署更快。 通過一個命令而不是通過多個命令命令部署範本。

   ![樣本部署比較](./media/overview/template-processing.png)

* **模組化檔**:您可以將範本分解為更小、可重用的元件,並在部署時將它們連結在一起。 您還可以將一個範本嵌套在另一個範本中。

* **創建任何 Azure 資源**:可以立即在範本中使用新的 Azure 服務和功能。 資源提供程式引入新資源后,即可通過範本部署這些資源。 在使用新服務之前,您不必等待工具或模組的更新。

* **可擴充性**:使用[部署文稿](deployment-script-template.md),您可以將 PowerShell 或 Bash 文稿添加到範本中。 部署腳本擴展了在部署期間設置資源的能力。 腳本可以包含在範本中,也可以存儲在外部源中並在範本中引用。 部署腳稿使您能夠在單個 ARM 範本中完成端到端環境設置。

* **測試**:您可以通過使用 ARM 範本工具套件(arm-ttk)測試範本,確保範本遵循建議的準則。 此測試套件是一個 PowerShell 腳本,可以從[GitHub](https://github.com/Azure/arm-ttk)下載。 通過該工具組,您可以更輕鬆地使用範本語言開發專業知識。

* **預覽更改**:在部署範本之前,可以使用[「如果」操作](template-deploy-what-if.md)獲取更改的預覽。 使用"假設",您將看到將創建、更新或刪除哪些資源,以及任何將更改的資源屬性。 What-if 操作檢查環境的當前狀態,並無需管理狀態。

* **內置驗證**:您的範本僅在通過驗證後部署。 資源管理器在開始部署之前檢查範本,以確保部署成功。 部署不太可能以半成品狀態停止。

* **追蹤的部署**:在 Azure 門戶中,您可以查看部署歷史記錄並獲取有關範本部署的資訊。 您可以看到已部署的範本、傳入的參數值以及任何輸出值。 其他基礎結構作為代碼服務不會通過門戶進行跟蹤。

   ![部署歷程記錄](./media/overview/deployment-history.png)

* **策略作為代碼**[:Azure 策略](../../governance/policy/overview.md)是一個策略,作為代碼框架來自動執行治理。 如果使用 Azure 策略,則透過樣本部署時對不合規的資源執行策略修正。

* **部署藍圖**:您可以利用 Microsoft 提供的[藍圖](../../governance/blueprints/overview.md)來滿足法規和合規性標準。 這些藍圖包括各種體繫結構的預建構範本。

* **CI/CD 整合**:您可以將範本整合到持續整合和持續部署 (CI/CD) 工具中,這些工具可以自動執行發佈管道,實現快速可靠的應用程式和基礎結構更新。 通過使用 Azure DevOps 和資源管理器範本任務,可以使用 Azure 管道持續生成和部署 ARM 範本專案。 要瞭解更多資訊,請參閱[具有導管](add-template-to-azure-pipelines.md)的 VS 專案[,以及與 Azure 管道的持續整合](template-tutorial-use-azure-pipelines.md)。

* **可匯出代碼**:您可以通過匯出資源組的當前狀態或查看用於特定部署的範本來獲取現有資源組的範本。 查看[導出的範本](export-template-portal.md)是瞭解範本語法的有用方法。

* **創作工具**:您可以使用[Visual Studio 代碼](use-vs-code-to-create-template.md)和範本工具擴展來創作範本。 您可以獲得感知、語法突出顯示、在線幫助和許多其他語言函數。 除了視覺工作室代碼,您還可以使用[視覺工作室](create-visual-studio-deployment-project.md)。

## <a name="template-file"></a>樣本檔案

在樣本中,可以編寫擴展 JSON 功能的[樣本表示式](template-expressions.md)。 這些表示式使用資源管理員提供的[函數](template-functions.md)。

該樣本具有以下部分:

* [參數](template-parameters.md)- 在部署期間提供允許將同一範本用於不同環境的值。

* [變數](template-variables.md)─ 定義在樣本中重複使用的值。 它們可以從參數值構造。

* [使用者定義的函數](template-user-defined-functions.md)- 創建簡化範本的自訂函數。

* [資源](template-syntax.md#resources)- 指定要部署的資源。

* [輸出](template-outputs.md)- 從已部署的資源返回值。

## <a name="template-deployment-process"></a>樣本部署程序

部署範本時,資源管理器會將範本轉換為 REST API 操作。 例如，當 Resource Manager 收到具有下列資源定義的範本︰

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2016-01-01",
    "name": "mystorageaccount",
    "location": "westus",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
  }
]
```

它會將定義轉換成下列 REST API 作業，該作業會再傳送給 Microsoft.Storage 資源提供者︰

```HTTP
PUT
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/mystorageaccount?api-version=2016-01-01
REQUEST BODY
{
  "location": "westus",
  "sku": {
    "name": "Standard_LRS"
  },
  "kind": "Storage",
  "properties": {}
}
```

## <a name="template-design"></a>樣本設計

範本和資源群組的定義方式全由您決定，方案的管理方式也是如此。 比方說，您可以透過單一範本在單一資源群組中部署三層式應用程式。

![三層式範本](./media/overview/3-tier-template.png)

但您不需要在單一的範本中定義整個基礎結構。 通常的合理作法是將您的部署需求分成一組有目標及特定目的的範本。 您可以輕鬆地將這些範本重複使用於不同的方案。   若要部署特定的方案，您會建立連結所有必要範本的主版範本。 下圖顯示如何透過包含三個巢狀範本的父範本部署三層式方案。

![巢狀階層範本](./media/overview/nested-tiers-template.png)

如果您想像的階層有不同的生命週期，您可以將這三個階層部署到不同的資源群組。 請注意，資源仍可連結至其他資源群組中的資源。

![階層範本](./media/overview/tier-templates.png)

如需巢狀範本的相關資訊，請參閱[透過 Azure Resource Manager 使用連結的範本](linked-templates.md)。

## <a name="next-steps"></a>後續步驟

* 有關指導您完成範本建立過程的分步教程,請參閱[教學:建立和部署第一個 ARM 樣本](template-tutorial-create-first-template.md)。
* 有關樣本檔中的屬性的資訊,請參閱[瞭解 ARM 樣本的結構和語法](template-syntax.md)。
* 要瞭解如何匯出樣本,請參閱[快速入門:使用 Azure 門戶建立和部署 ARM 範本](quickstart-create-templates-use-the-portal.md)。
