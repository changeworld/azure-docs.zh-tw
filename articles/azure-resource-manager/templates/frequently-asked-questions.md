---
title: ARM 範本的常見問題
description: 關於 Azure Resource Manager 範本的常見問題（FAQ）。
ms.topic: conceptual
ms.date: 07/27/2020
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: e23334daf0f4f6d5a6917af49c786d47c4557375
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87327254"
---
# <a name="frequently-asked-questions-about-arm-templates"></a>ARM 範本的相關常見問題

本文會回答有關 Azure Resource Manager （ARM）範本的常見問題。

## <a name="getting-started"></a>開始使用

* **什麼是 ARM 範本，為什麼要使用它們？**

  ARM 範本是 JSON 檔案，您可以在其中定義要部署至 Azure 的內容。 範本可協助您為 Azure 執行基礎結構即程式碼解決方案。 您的組織可以重複且可靠地將所需的基礎結構部署到不同的環境。
  
  若要深入瞭解 ARM 範本如何協助您管理 Azure 基礎結構，請參閱[什麼是 arm 範本？](overview.md)

* **如何? 開始使用範本嗎？**

  若要簡化編寫 ARM 範本，您需要適當的工具。 建議您安裝[Visual Studio Code](https://code.visualstudio.com/)和[Azure Resource Manager 工具延伸](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)模組。 如需這些工具的快速簡介，請參閱[快速入門：使用 Visual Studio Code 建立 Azure Resource Manager 範本](quickstart-create-templates-use-visual-studio-code.md)。

  當您準備好瞭解如何建立 ARM 範本時，請[在 ARM 範本上啟動初學者教學課程系列](template-tutorial-create-first-template.md)。 這些教學課程會帶您逐步完成建立 ARM 範本的程式。 您會瞭解範本的不同區段，以及如何搭配使用它們。 此內容也會以[Microsoft Learn 模組](/learn/modules/authoring-arm-templates/)的形式提供。

* **我應該使用 ARM 範本或 Terraform 來部署至 Azure 嗎？**

  使用您喜歡最佳的選項。 這兩項服務都可協助您自動部署至 Azure。
  
  我們相信透過其他基礎結構即程式碼服務使用 ARM 範本有其優點。 若要瞭解這些優點，請參閱[為何選擇 ARM 範本？](overview.md#why-choose-arm-templates)

## <a name="build-2020"></a>組建2020

* **我在 Microsoft Build 2020 錯過了簡報。簡報是否可以用來進行觀看？**

  是，請[隨時觀看](https://mybuild.microsoft.com/sessions/82984db4-37a4-4ed3-bf8b-13298841ed18?source=sessions)。

* **哪裡可以取得您在組建中宣佈的新功能的詳細資訊？**

  如需我們所要使用之功能的一般資訊，請加入我們的[Azure 顧問部署 Yammer 群組](https://aka.ms/ARMMeet)。

  若要瞭解新的範本語言，請[註冊通知](https://aka.ms/armLangUpdates)。

  若要瞭解範本規格，請參閱[Azure Resource Manager 範本規格（預覽）](template-specs.md)。

## <a name="creating-and-testing-templates"></a>建立和測試範本

* **我可以在哪裡瞭解 ARM 範本的最佳做法？**

  如需如何執行範本的建議，請參閱[ARM 範本的最佳做法](template-best-practices.md)。 建立範本之後，請執行[ARM 測試控管](https://github.com/azure/arm-ttk)組。 它會檢查您的範本是否符合建議的作法。

* **我已透過入口網站設定我的環境。是否有從現有的資源群組取得範本的方法？**

  是，您可以從資源群組[匯出範本](export-template-portal.md)。 匯出的範本是學習範本的好起點，但您可能會想要在生產環境中使用它之前先修改它。
  
  匯出範本時，您可以選取要包含在範本中的資源。

* **我可以在 ARM 範本中建立資源群組，並對其部署資源嗎？**

  是，當您在 Azure 訂用帳戶層級部署範本時，可以在範本中建立資源群組。 如需建立資源群組和部署資源的範例，請參閱[資源群組和資源](deploy-to-subscription.md#resource-groups)。

* **我可以在 ARM 範本中建立訂用帳戶嗎？**

  尚未完成，但我們正在處理。

* **如何在部署範本之前先進行測試？**

  我們建議您在部署之前，先在您的範本上執行[ARM 測試控管](https://github.com/azure/arm-ttk)組和「[假設](template-deploy-what-if.md)」作業。 測試控管組會檢查您的範本是否使用最佳作法。 當它識別出可改善您的範本執行方式的變更時，它會提供警告。

  「假設」作業會顯示您的範本將對您的環境進行的變更。 部署之前，您可以看到非預期的變更。 假設也會傳回任何可在預檢驗證期間偵測到的錯誤。 例如，如果您的範本包含語法錯誤，則會傳回該錯誤。 它也會傳回任何可判斷已部署資源最終狀態的錯誤。 例如，如果您的範本部署了名稱已在使用中的儲存體帳戶，則會傳回該錯誤。

* **哪裡可以找到每個資源類型可用屬性的相關資訊？**

  VS Code 提供 intellisense 以使用資源屬性。 您也可以查看屬性和描述的[範本參考](/azure/templates/)。

* **我需要建立一個資源類型的多個實例。如何? 在我的範本中建立反覆運算器嗎？**

  使用 copy 元素可指定多個實例。 您可以在[資源](copy-resources.md)、[屬性](copy-properties.md)、[變數](copy-variables.md)和[輸出](copy-outputs.md)上使用 [複製]。

## <a name="template-language"></a>範本語言

* **我聽說您正在使用新的範本語言。哪裡可以找到更多相關資訊？**

  若要瞭解新的範本語言，請[註冊通知](https://aka.ms/armLangUpdates)。

* **是否有計劃要支援在 YAML 中建立範本？**

  目前沒有計劃可支援 YAML。 我們相信新的範本語言會提供比 YAML 或 JSON 更容易使用的解決方案。

* **發行新的範本語言之後，仍然可以在 JSON 中撰寫範本嗎？**

  是，您可以繼續使用 JSON 範本。

* **您會提供工具，將我的 JSON 範本轉換成新的範本語言嗎？**

  是。

## <a name="template-specs"></a>範本規格

* **如何參與範本規格的預覽版本？**

  [加入範本規格的等候清單](https://aka.ms/templateSpecsWaitlist)。

* **如何與範本規格和 Azure 藍圖相關聯？**

  Azure 藍圖會藉由將資源取代為資源，在其實現中使用範本規格 `blueprint definition` `template spec` 。 我們將提供遷移路徑，將藍圖定義轉換成範本規格，但仍會支援藍圖定義 Api。 資源沒有任何變更 `blueprint assignment` 。 藍圖會保持使用者體驗，以在 Azure 中撰寫受管理的環境。

* **範本規格是否取代連結的範本？**

  否，但範本規格是設計來與連結的範本搭配運作。 您不需要在部署父範本之前，將連結的範本移至可公開存取的端點。 相反地，您會在建立範本規格時，將父範本及其成品封裝在一起。

* **可以在訂用帳戶之間共用範本規格嗎？**

  是的，只要使用者擁有範本規格的讀取權限，就可以在訂用帳戶之間使用。範本規格無法跨租使用者使用。

## <a name="scripts-in-templates"></a>範本中的腳本

* **我可以在範本中加入腳本來執行範本中不可行的工作嗎？**

  是，請使用[部署腳本](deployment-script-template.md)。 您可以在範本中包含 Azure PowerShell 或 Azure CLI 腳本。 此功能目前為預覽狀態。

* **我仍然可以使用自訂腳本擴充功能和期望狀態設定（DSC）嗎？**

  這些選項仍然可用而且尚未變更。 部署腳本是設計用來執行與 VM 來賓不相關的動作。 如果您需要在 VM 的主機作業系統上執行腳本，則自訂腳本擴充功能和/或 DSC 會是較佳的選擇。 不過，部署腳本有其優點，例如設定超時時間。

* **Azure Government 支援部署腳本嗎？**

  是，您可以使用 US Gov 亞利桑那州和 US Gov 維吉尼亞州中的部署腳本。

## <a name="preview-changes-before-deployment"></a>在部署前預覽變更

* **我可以在部署範本之前，預覽將發生的變更嗎？**

  是，請使用「[假設」功能](template-deploy-what-if.md)。 它會評估您環境的目前狀態，並將其與部署後存在的狀態進行比較。 您可以檢查摘要的變更，以確定範本沒有任何非預期的結果。

* **是否可以搭配增量和完整模式使用假設？**

  是，支援這兩種[部署模式](deployment-modes.md)。 如需使用增量模式的範例，請參閱[執行假設](template-deploy-what-if.md#run-what-if-operation)作業。 如需使用完整模式的範例，請參閱[確認刪除](template-deploy-what-if.md#confirm-deletion)。

* **假設使用的是連結的範本嗎？**

  是，假設會評估父範本的狀態及其連結的範本。

* **我可以在 Azure 管線中使用假設嗎？**

  是，您可以使用 [假設] 來確認管線應該繼續。

* **當我使用 [假設] 時，我會看到不在我的範本中的屬性變更。是否預期會出現這種「雜訊」？**

  假設處於預覽狀態。 我們正努力減少雜訊。 您可以在這裡的 GitHub 存放庫中提交問題來協助我們改善：https://aka.ms/WhatIfIssues

## <a name="template-visualizer"></a>範本視覺化

* **是否有方法可以將我的 ARM 範本和其資源視覺化？**

  我們有一個由[社區貢獻的 VS Code 延伸](https://aka.ms/ARMVisualizer)模組，可執行視覺化 ARM 範本的絕佳作業。 它會顯示您正在部署的資源，以及它們之間的關聯性。

* **我可以在 VS Code 之外使用範本視覺化檢視嗎？**

  正在入口網站中預覽範本視覺化。 如需詳細資訊，請觀賞 Build 的這個[簡短的課程](https://mybuild.microsoft.com/sessions/0525094b-1fd2-4f69-bfd6-6d2fff6ffe5f?source=sessions)。

## <a name="deployment-limits"></a>部署限制

* **在單一部署作業中，我可以部署多少個資源群組？**

  在過去，這項限制是五個資源群組。 其最近已增加到800的資源群組。 如需詳細資訊，請參閱在訂用帳戶[層級建立資源群組和資源](deploy-to-subscription.md)。

* **我收到有關在部署歷程記錄中限制為800部署的錯誤。我該怎麼做？**

  我們正在變更如何維護資源群組的部署歷程記錄。 在過去，您必須手動刪除此歷程記錄中的部署，以避免發生此錯誤。 自2020年6月起，我們將會在您接近限制時，自動從歷程記錄中刪除部署。 如需詳細資訊，請參閱[從部署歷程記錄自動刪除](deployment-history-deletions.md)。

  從歷程記錄中刪除部署並不會影響已部署的資源。

## <a name="templates-and-devops"></a>範本和 DevOps

* **我可以將 ARM 範本整合到 Azure Pipelines 嗎？**

  是。 如需如何使用範本和管線的說明，請參閱[教學課程： Azure Resource Manager 範本與 Azure Pipelines 的持續整合](deployment-tutorial-pipeline.md)和使用[Azure Pipelines 整合 ARM 範本](add-template-to-azure-pipelines.md)。

* **我可以使用 GitHub 動作來部署範本嗎？**

  是，請參閱[使用 GitHub 動作部署 Azure Resource Manager 範本](deploy-github-actions.md)。

## <a name="next-steps"></a>後續步驟

如需 ARM 範本的簡介，請參閱[什麼是 arm 範本？](overview.md)。
