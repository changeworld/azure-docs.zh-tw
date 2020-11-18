---
title: ARM 範本的常見問題
description: 常見問題 (有關 Azure Resource Manager 範本的常見問題) 。
ms.topic: conceptual
ms.date: 09/17/2020
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 7f4238035a6fccb0addb9a481287b31732bd2662
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94744530"
---
# <a name="frequently-asked-questions-about-arm-templates"></a>ARM 範本的常見問題

本文將回答有關 Azure Resource Manager (ARM) 範本的常見問題。

## <a name="getting-started"></a>開始使用

* **什麼是 ARM 範本？為何應該使用它們？**

  ARM 範本是 JSON 檔案，您可以在其中定義要部署至 Azure 的內容。 範本可協助您為 Azure 實施基礎結構即程式碼解決方案。 您的組織可以重複且可靠地將必要的基礎結構部署至不同的環境。
  
  若要深入瞭解 ARM 範本如何協助您管理 Azure 基礎結構，請參閱 [什麼是 arm 範本？](overview.md)

* **如何? 入門範本？**

  若要簡化編寫 ARM 範本，您需要適當的工具。 建議您安裝 [Visual Studio Code](https://code.visualstudio.com/) 和 [Azure Resource Manager 工具延伸](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)模組。 如需這些工具的快速簡介，請參閱 [快速入門：使用 Visual Studio Code 建立 Azure Resource Manager 範本](quickstart-create-templates-use-visual-studio-code.md)。

  當您準備好瞭解如何建立 ARM 範本時，請 [在 ARM 範本上啟動初學者教學課程系列](template-tutorial-create-first-template.md)。 這些教學課程會帶您逐步完成建立 ARM 範本的程式。 您將瞭解範本的不同區段，以及它們如何一起運作。 此內容也可做為 [Microsoft Learn 模組](/learn/modules/authoring-arm-templates/)。

* **我應該使用 ARM 範本或 Terraform 來部署到 Azure 嗎？**

  使用您最喜歡的選項。 這兩項服務都可協助您將 Azure 的部署自動化。
  
  我們相信，透過其他基礎結構即程式碼服務使用 ARM 範本有其優點。 若要瞭解這些優點，請參閱 [為何選擇 ARM 範本？](overview.md#why-choose-arm-templates)

## <a name="build-2020"></a>組建2020

* **我在 Microsoft Build 2020 錯過了您的簡報。簡報是否可供觀看？**

  是，請 [隨時觀看](https://mybuild.microsoft.com/sessions/82984db4-37a4-4ed3-bf8b-13298841ed18?source=sessions)。

* **哪裡可以取得有關您在組建時所宣佈之新功能的詳細資訊？**

  如需我們正在運作之功能的一般資訊，請加入我們的 [Azure Advisor 部署 Yammer 群組](https://aka.ms/ARMMeet)。

  若要瞭解新的範本語言，請 [註冊以取得通知](https://aka.ms/armLangUpdates)。

  若要瞭解範本規格，請參閱 [Azure Resource Manager 範本規格 (Preview) ](template-specs.md)。

## <a name="creating-and-testing-templates"></a>建立和測試範本

* **哪裡可以瞭解 ARM 範本的最佳作法？**

  如需如何實行範本的建議，請參閱 [ARM 範本的最佳做法](template-best-practices.md)。 建立範本之後，執行 [ARM 測試控管](https://github.com/azure/arm-ttk)組。 它會檢查您的範本是否符合建議的做法。

* **我已透過入口網站設定我的環境。有什麼方法可以從現有的資源群組取得範本？**

  是，您可以從資源群組 [匯出範本](export-template-portal.md) 。 匯出的範本是學習範本的好起點，但是您可能會想要在實際執行環境中使用範本之前先加以修改。
  
  匯出範本時，您可以選取要包含在範本中的資源。

* **我可以在 ARM 範本中建立資源群組，並將資源部署到該群組嗎？**

  是的，您可以在 Azure 訂用帳戶層級部署範本時，在範本中建立資源群組。 如需建立資源群組和部署資源的範例，請參閱 [資源群組和資源](deploy-to-subscription.md#resource-groups)。

* **我可以在 ARM 範本中建立訂用帳戶嗎？**

  是，如需詳細資訊，請參閱以程式設計 [方式使用最新的 api 建立 Azure 訂用](../../cost-management-billing/manage/programmatically-create-subscription.md)帳戶。

* **如何在部署範本之前進行測試？**

  建議您在部署範本之前，先執行 [ARM 測試控管](https://github.com/azure/arm-ttk) 組和您的範本上的「 [假設](template-deploy-what-if.md) 」作業。 測試控管組會檢查您的範本是否使用最佳做法。 它會在識別出可改善您範本之執行方式的變更時提供警告。

  「假設」作業會顯示您的範本將對您的環境進行的變更。 在部署之前，您可以看到非預期的變更。 假設也會傳回可在預檢驗證期間偵測到的任何錯誤。 例如，如果您的範本包含語法錯誤，則會傳回該錯誤。 它也會傳回任何錯誤，可判斷已部署資源的最終狀態。 例如，如果您的範本使用已在使用中的名稱來部署儲存體帳戶，則會傳回該錯誤。

* **我可以在哪裡找到每個資源類型可用的屬性相關資訊？**

  VS Code 提供使用資源屬性的 intellisense。 您也可以查看 [範本參考](/azure/templates/) 中的屬性和描述。

* **我需要建立多個資源類型的實例。如何? 在我的範本中建立反覆運算器？**

  使用 copy 元素可指定多個實例。 您可以在 [資源](copy-resources.md)、 [屬性](copy-properties.md)、 [變數](copy-variables.md)和 [輸出](copy-outputs.md)上使用複製。

## <a name="template-language"></a>範本語言

* **我聽說您正在使用新的範本語言。我可以在哪裡找到更多相關資訊？**

  若要預覽新的語言，請參閱 [Project Bicep 存放庫](https://github.com/Azure/bicep)。 若要隨時掌握新的語言，請 [註冊以取得通知](https://aka.ms/armLangUpdates)。

* **是否有規劃可支援在 YAML 中建立範本？**

  目前沒有任何計畫可支援 YAML。 我們相信新的範本語言將提供比 YAML 或 JSON 更容易使用的解決方案。

* **在發行新的範本語言之後，是否仍可在 JSON 中撰寫範本？**

  是，您可以繼續使用 JSON 範本。

* **您是否提供將 JSON 範本轉換成新範本語言的工具？**

  可以。

## <a name="template-specs"></a>範本規格

* **如何開始使用範本規格的預覽版本？**

  安裝最新版本的 PowerShell 或 Azure CLI。 針對 Azure PowerShell，請使用第 [5.0.0 版或更新](/powershell/azure/install-az-ps)版本。 針對 Azure CLI，請使用 [2.14.2 版或更新版本](/cli/azure/install-azure-cli)。

* **範本規格和 Azure 藍圖如何相關？**

  Azure 藍圖將使用資源取代資源，以在其實中使用範本規格 `blueprint definition` `template spec` 。 我們會提供遷移路徑，將藍圖定義轉換成範本規格，但仍會支援藍圖定義 Api。 資源沒有任何變更 `blueprint assignment` 。 藍圖將保持使用者體驗，以在 Azure 中撰寫受管制的環境。

* **範本規格是否取代連結的範本？**

  否，但範本規格是設計來與連結的範本搭配使用。 部署父範本之前，您不需要將連結的範本移至可公開存取的端點。 相反地，在建立範本規格時，您會將父範本及其成品封裝在一起。

* **範本規格可以跨訂用帳戶共用嗎？**

  是的，只要使用者具有範本規格的讀取權限，就可以跨訂用帳戶使用。範本規格無法跨租使用者使用。

## <a name="scripts-in-templates"></a>範本中的腳本

* **我可以在範本中包含腳本，以進行範本中不可行的工作嗎？**

  是，請使用 [部署腳本](deployment-script-template.md)。 您可以在範本中包含 Azure PowerShell 或 Azure CLI 腳本。 此功能目前為預覽狀態。

* **我是否仍然可以使用自訂腳本擴充功能和 desired state configuration (DSC) ？**

  這些選項仍可使用，而且尚未變更。 部署腳本是設計來執行與 VM 來賓無關的動作。 如果您需要在 VM 中的主機作業系統上執行腳本，則自訂腳本擴充功能和（或） DSC 會是較佳的選擇。 不過，部署腳本有一些優點，例如設定超時時間。

* **Azure Government 支援部署腳本嗎？**

  是的，您可以在 US Gov 亞利桑那州和 US Gov 維吉尼亞州中使用部署腳本。

## <a name="preview-changes-before-deployment"></a>在部署前預覽變更

* **我可以在部署範本之前，預覽將會發生的變更嗎？**

  是，請使用 [假設功能](template-deploy-what-if.md)。 它會評估您環境的目前狀態，並將其與部署後將存在的狀態進行比較。 您可以檢查摘要的變更，以確定範本沒有任何未預期的結果。

* **我可以搭配增量和完整模式使用假設嗎？**

  是，同時支援這兩種 [部署模式](deployment-modes.md) 。 如需使用累加模式的範例，請參閱 [執行假設運算](template-deploy-what-if.md#run-what-if-operation)。 如需使用完整模式的範例，請參閱 [確認刪除](template-deploy-what-if.md#confirm-deletion)。

* **如何使用連結的範本？**

  是，假設會評估父範本的狀態及其連結的範本。

* **我可以在 Azure 管線中使用假設嗎？**

  是，您可以使用假設來確認管線應該繼續。

* **當我使用 if 時，我在範本的屬性中看到了變更。是否預期會出現這種「雜訊」？**

  假設處於預覽狀態。 我們正在努力減少雜訊。 您可以在以下的 GitHub 存放庫中提交問題，以協助我們改進： https://aka.ms/WhatIfIssues

## <a name="template-visualizer"></a>範本視覺化

* **有方法可以讓我將 ARM 範本及其資源視覺化嗎？**

  我們有一個由 [社區貢獻的 VS Code 延伸](https://aka.ms/ARMVisualizer) 模組，可執行將 ARM 範本視覺化的絕佳工作。 它會顯示您正在部署的資源，以及它們之間的關聯性。

* **我可以使用 VS Code 以外的範本視覺化檢視嗎？**

  在入口網站中預覽範本的視覺化。 如需詳細資訊，請 [從 Build 觀看這個簡短的課程](https://mybuild.microsoft.com/sessions/0525094b-1fd2-4f69-bfd6-6d2fff6ffe5f?source=sessions)。

## <a name="deployment-limits"></a>部署限制

* **我可以在單一部署作業中部署多少個資源群組？**

  在過去，這項限制是五個資源群組。 它最近已增加至800資源群組。 如需詳細資訊，請參閱 [在訂用帳戶層級建立資源群組和資源](deploy-to-subscription.md)。

* **我收到有關在部署歷程記錄中受限於800部署的錯誤。我該怎麼做？**

  我們正在變更如何維護資源群組的部署歷程記錄。 在過去，您必須手動刪除此歷程記錄的部署，以避免此錯誤。 從2020年6月開始，我們會在您接近限制時，自動從歷程記錄中刪除部署。 如需詳細資訊，請參閱 [從部署歷程記錄自動刪除](deployment-history-deletions.md)。

  從歷程記錄中刪除部署並不會影響已部署的資源。

## <a name="templates-and-devops"></a>範本和 DevOps

* **我可以將 ARM 範本整合到 Azure Pipelines 嗎？**

  可以。 如需如何使用範本和管線的說明，請參閱 [教學課程：搭配使用 Azure Pipelines 的 Azure Resource Manager 範本的持續整合](deployment-tutorial-pipeline.md) ，以及 [整合 ARM 範本與 Azure Pipelines](add-template-to-azure-pipelines.md)。

* **是否可以使用 GitHub 動作來部署範本？**

  是，請參閱 [使用 GitHub Actions 部署 Azure Resource Manager 範本](deploy-github-actions.md)。

## <a name="next-steps"></a>後續步驟

如需 ARM 範本的簡介，請參閱 [什麼是 arm 範本？](overview.md)。
