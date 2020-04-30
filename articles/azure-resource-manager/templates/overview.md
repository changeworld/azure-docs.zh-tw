---
title: 範本概觀
description: 說明使用 Azure Resource Manager 範本來部署資源的優點。
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: b3b5fb383ac89d0968a437f35aab656afa1913f0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82086329"
---
# <a name="what-are-arm-templates"></a>什麼是 ARM 範本？

隨著移至雲端，許多小組都採用 agile 開發方法。 這些小組會快速地反復查看。 他們需要將其解決方案重複部署至雲端，並知道其基礎結構處於可靠的狀態。 由於基礎結構已成為反復程式的一部分，因此作業與開發之間的劃分已消失。 小組必須透過統一的程式來管理基礎結構和應用程式程式碼。

若要解決這些挑戰，您可以將部署自動化，並使用基礎結構即程式碼的做法。 在程式碼中，您可以定義需要部署的基礎結構。 基礎結構程式碼會成為專案的一部分。 就像應用程式程式碼一樣，您可以將基礎結構程式碼儲存在來源存放庫中，並將其版本。 小組中的任何人都可以執行程式碼，並部署類似的環境。

若要將基礎結構當做 Azure 解決方案的程式碼來執行，請使用 Azure Resource Manager （ARM）範本。 範本是 JavaScript 物件標記法（JSON）檔案，可定義專案的基礎結構和設定。 範本會使用宣告式語法，可讓您陳述您要部署的項目，而不需要撰寫一連串程式設計命令來加以建立。 在範本中，您可以指定要部署的資源，以及這些資源的屬性。

## <a name="why-choose-arm-templates"></a>為何選擇 ARM 範本？

如果您想要決定使用 ARM 範本和其中一個其他基礎結構作為程式碼服務，請考慮使用範本的下列優點：

* **宣告式語法**： ARM 範本可讓您以宣告方式建立及部署整個 Azure 基礎結構。 例如，您不僅可以部署虛擬機器，也不能部署網路基礎結構、儲存系統，以及您可能需要的其他任何資源。

* 可**重複的結果**：在整個開發生命週期中重複部署您的基礎結構，並讓您確信以一致的方式部署您的資源。 範本具有等冪性，這表示您可以多次部署相同的範本，並取得相同狀態的相同資源類型。 您可以開發一個代表所需狀態的範本，而不是開發許多不同的範本來代表更新。

* **協調流程**：您不需要擔心排序作業的複雜性。 Resource Manager 會協調相互相依資源的部署，使其以正確的順序建立。 可能的話，Resource Manager 以平行方式部署資源，讓您的部署比序列部署更快完成。 您可以透過一個命令來部署範本，而不是透過多個命令式命令。

   ![範本部署比較](./media/overview/template-processing.png)

* **模組化**檔案：您可以將範本分成較小且可重複使用的元件，並在部署期間將它們連結在一起。 您也可以在另一個範本內嵌套一個範本。

* **建立任何 azure 資源**：您可以立即在範本中使用新的 azure 服務和功能。 一旦資源提供者引進新資源，您就可以透過範本來部署這些資源。 您不需要等到工具或模組更新後，再使用新的服務。

* 擴充**性：您**可以使用[部署腳本](deployment-script-template.md)，將 PowerShell 或 Bash 腳本新增至您的範本。 部署腳本會擴充您在部署期間設定資源的能力。 腳本可以包含在範本中，或儲存在外部來源中，並在範本中參考。 部署腳本讓您能夠在單一 ARM 範本中完成端對端環境設定。

* **測試**：您可以使用 arm 範本工具套件（arm-ttk）進行測試，以確定您的範本遵循建議的指導方針。 此測試套件是您可以從[GitHub](https://github.com/Azure/arm-ttk)下載的 PowerShell 腳本。 此工具套件可讓您更輕鬆地使用範本語言開發專業知識。

* **預覽變更**：您可以在部署範本之前，使用「[假設](template-deploy-what-if.md)」作業來取得變更的預覽。 使用 [假設] 時，您會看到將會建立、更新或刪除哪些資源，以及任何將變更的資源屬性。 「假設」作業會檢查您環境的目前狀態，並不需要管理狀態。

* **內建驗證**：您的範本只有在通過驗證之後才會部署。 Resource Manager 在開始部署之前檢查範本，以確定部署將會成功。 您的部署較不可能以半完成狀態停止。

* **追蹤的部署**：在 Azure 入口網站中，您可以查看部署歷程記錄，並取得範本部署的相關資訊。 您可以看到已部署的範本、傳入的參數值，以及任何輸出值。 其他基礎結構即程式碼服務不會透過入口網站進行追蹤。

   ![部署歷程記錄](./media/overview/deployment-history.png)

* **原則即程式碼**： [Azure 原則](../../governance/policy/overview.md)是用來將治理自動化的原則，這是程式碼架構。 如果您使用 Azure 原則，則會在透過範本部署時，在不符合規範的資源上進行原則補救。

* **部署藍圖**：您可以利用 Microsoft 提供的[藍圖](../../governance/blueprints/overview.md)來符合法規和合規性標準。 這些藍圖包括適用于各種架構的預先建立範本。

* **CI/cd 整合**：您可以將範本整合到持續整合和持續部署（CI/cd）工具中，這可以自動化您的發行管線，以進行快速且可靠的應用程式和基礎結構更新。 藉由使用 Azure DevOps 和 Resource Manager 範本工作，您可以使用 Azure Pipelines 來持續建立及部署 ARM 範本專案。 若要深入瞭解，請參閱[VS project 與管線](add-template-to-azure-pipelines.md)和[教學課程：使用 Azure Pipelines 持續整合 Azure Resource Manager 範本](./deployment-tutorial-pipeline.md)。

* 可匯出的程式**代碼**.. 您可以藉由匯出資源群組的目前狀態，或查看用於特定部署的範本，來取得現有資源群組的範本。 查看[匯出的範本](export-template-portal.md)是瞭解範本語法的實用方式。

* **撰寫工具**：您可以使用[Visual Studio Code](use-vs-code-to-create-template.md)和範本工具擴充功能來撰寫範本。 您可以取得 intellisense、語法反白顯示、內嵌說明，以及許多其他語言功能。 除了 Visual Studio 的程式碼之外，您也可以使用[Visual Studio](create-visual-studio-deployment-project.md)。

## <a name="template-file"></a>範本檔案

在您的範本內，您可以撰寫可延伸 JSON 功能的[範本運算式](template-expressions.md)。 這些運算式會利用 Resource Manager 所[提供的](template-functions.md)函式。

此範本具有下列區段：

* [參數](template-parameters.md)-在部署期間提供值，可讓相同的範本用於不同的環境。

* [變數](template-variables.md)-定義在您的範本中重複使用的值。 可以從參數值來進行。

* [使用者定義函數](template-user-defined-functions.md)-建立可簡化範本的自訂函式。

* [資源](template-syntax.md#resources)-指定要部署的資源。

* [輸出](template-outputs.md)-來自已部署資源的傳回值。

## <a name="template-deployment-process"></a>範本部署進程

當您部署範本時，Resource Manager 會將範本轉換成 REST API 作業。 例如，當 Resource Manager 收到具有下列資源定義的範本︰

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

## <a name="template-design"></a>範本設計

範本和資源群組的定義方式全由您決定，方案的管理方式也是如此。 比方說，您可以透過單一範本在單一資源群組中部署三層式應用程式。

![三層式範本](./media/overview/3-tier-template.png)

但您不需要在單一的範本中定義整個基礎結構。 通常的合理作法是將您的部署需求分成一組有目標及特定目的的範本。 您可以輕鬆地將這些範本重複使用於不同的方案。   若要部署特定的方案，您會建立連結所有必要範本的主版範本。 下圖顯示如何透過包含三個巢狀範本的父範本部署三層式方案。

![巢狀階層範本](./media/overview/nested-tiers-template.png)

如果您想像的階層有不同的生命週期，您可以將這三個階層部署到不同的資源群組。 請注意，資源仍可連結至其他資源群組中的資源。

![階層範本](./media/overview/tier-templates.png)

如需巢狀範本的相關資訊，請參閱[透過 Azure Resource Manager 使用連結的範本](linked-templates.md)。

## <a name="next-steps"></a>後續步驟

* 如需逐步教學課程，引導您完成建立範本的程式，請參閱[教學課程：建立及部署您的第一個 ARM 範本](template-tutorial-create-first-template.md)。
* 如需範本檔案中屬性的相關資訊，請參閱[瞭解 ARM 範本的結構和語法](template-syntax.md)。
* 若要瞭解如何匯出範本，請參閱[快速入門：使用 Azure 入口網站建立及部署 ARM 範本](quickstart-create-templates-use-the-portal.md)。
