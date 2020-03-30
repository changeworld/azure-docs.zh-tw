---
title: 測試 UI 定義檔
description: 說明如何測試透過入口網站建立 Azure 受控應用程式的使用者體驗。
author: tfitzmac
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: tomfitz
ms.openlocfilehash: e2d075a58872f9337c7d1faa642a48047e2f9ddf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250188"
---
# <a name="test-your-portal-interface-for-azure-managed-applications"></a>測試 Azure 託管應用程式的門戶介面

為託管應用程式[創建 createUiDefinition.json 檔](create-uidefinition-overview.md)後，需要測試使用者體驗。 要簡化測試，請使用在門戶中載入檔的沙箱環境。 您不需要實際部署受控應用程式。 沙箱以當前全屏門戶體驗顯示您的使用者介面。 或者，您可以使用腳本來測試介面。 本文中會展示這兩種方法。 沙箱是預覽介面的推薦方式。

## <a name="prerequisites"></a>Prerequisites

* **createUiDefinition.json** 檔案。 如果沒有此檔，請複製[示例檔](https://github.com/Azure/azure-quickstart-templates/blob/master/100-marketplace-sample/createUiDefinition.json)。

* Azure 訂用帳戶。 如果沒有 Azure 訂閱，請先[創建一個免費帳戶](https://azure.microsoft.com/free/)。"

## <a name="use-sandbox"></a>使用沙箱

1. 打開["創建 UI 定義沙箱](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade)"。

   ![顯示沙箱](./media/test-createuidefinition/show-sandbox.png)

1. 將空定義替換為 createUiDefinition.json 檔的內容。 選取 [預覽]****。

   ![選擇預覽](./media/test-createuidefinition/select-preview.png)

1. 將顯示您創建的表單。 您可以逐步完成使用者體驗並填寫值。

   ![顯示表單](./media/test-createuidefinition/show-ui-form.png)

### <a name="troubleshooting"></a>疑難排解

如果表單在選擇 **"預覽"** 後未顯示，則可能有語法錯誤。 查找右側滾動欄上的紅色指示器並導航到該指示器。

![顯示語法錯誤](./media/test-createuidefinition/show-syntax-error.png)

如果表單未顯示，而您看到的是帶有淚滴的雲圖示，則表單有錯誤，例如缺少屬性。 在瀏覽器中打開 Web 開發人員工具。 [主控台]**** 會顯示關於介面的重要訊息。

![顯示錯誤](./media/test-createuidefinition/show-error.png)

## <a name="use-test-script"></a>使用測試腳本

若要在入口網站中測試您的介面，請將下列其中一個指令碼複製到本機電腦：

* [PowerShell 側載入腳本 - Az 模組](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-AzCreateUIDefinition.ps1)
* [PowerShell 側載入腳本 - Azure 模組](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-CreateUIDefinition.ps1)
* [Azure CLI 側載指令碼](https://github.com/Azure/azure-quickstart-templates/blob/master/sideload-createuidef.sh)

若要在入口網站中查看您的介面檔案，請執行您已下載的指令碼。 此指令碼會在您的 Azure 訂用帳戶中建立儲存體帳戶，並將 createUiDefinition.json 檔案上傳至儲存體帳戶。 第一次執行指令碼時或儲存體帳戶已刪除後，將會建立儲存體帳戶。 如果 Azure 訂用帳戶中已有儲存體帳戶，則指令碼會重複使用該帳戶。 指令碼會開啟入口網站，並從儲存體帳戶載入您的檔案。

請提供儲存體帳戶的位置，並指定含有 createUiDefinition.json 檔案的資料夾。

對於 PowerShell，請使用：

```powershell
.\SideLoad-AzCreateUIDefinition.ps1 `
  -StorageResourceGroupLocation southcentralus `
  -ArtifactsStagingDirectory .\100-Marketplace-Sample
```

對於 Azure CLI，請使用：

```bash
./sideload-createuidef.sh \
  -l southcentralus \
  -a .\100-Marketplace-Sample
```

如果 createUiDefinition.json 檔案位於和指令碼相同的資料夾內，而且您已建立儲存體帳戶，則不需要提供這些參數。

對於 PowerShell，請使用：

```powershell
.\SideLoad-AzCreateUIDefinition.ps1
```

對於 Azure CLI，請使用：

```bash
./sideload-createuidef.sh
```

指令碼會在瀏覽器中開啟新的索引標籤。 它會顯示入口網站，其中包含您用來建立受控應用程式的介面。

提供欄位的值。 完成後，您將看到傳遞給範本的值，這些值可在瀏覽器的開發人員工具主控台中找到。

![顯示值](./media/test-createuidefinition/show-json.png)

您可以使用這些值作為參數檔案來測試部署範本。

如果門戶掛在摘要螢幕上，則輸出部分中可能存在錯誤。 例如，您可能參考了不存在的控制項。 如果輸出中的參數為空，則該參數可能引用不存在的屬性。 例如，控制項的參考有效，但屬性參考無效。

## <a name="test-your-solution-files"></a>測試您的解決方案檔案

現在您已確認入口網站介面正常運作，接下來可以驗證您的 createUiDefinition 檔案是否與 mainTemplate.json 檔案正確整合。 您可以執行驗證指令碼測試來測試解決方案檔案的內容，包括 createUiDefinition 檔案。 指令碼會驗證 JSON 語法、檢查文字欄位上的 regex 運算式，並確定入口網站介面的輸出值符合您的範本參數。 如需執行此指令碼的相關資訊，請參閱[執行範本的靜態驗證檢查](https://github.com/Azure/azure-quickstart-templates/tree/master/test)。

## <a name="next-steps"></a>後續步驟

驗證入口網站介面後，請了解如何建立[在 Marketplace 中提供的 Azure 受控應用程式](publish-marketplace-app.md)。
