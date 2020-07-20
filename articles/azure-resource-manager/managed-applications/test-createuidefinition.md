---
title: 測試 UI 定義檔
description: 說明如何測試透過入口網站建立 Azure 受控應用程式的使用者體驗。
author: tfitzmac
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: tomfitz
ms.openlocfilehash: e2d075a58872f9337c7d1faa642a48047e2f9ddf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "78250188"
---
# <a name="test-your-portal-interface-for-azure-managed-applications"></a>測試 Azure 受控應用程式的入口網站介面

建立受控應用程式[的檔案 createUiDefinition.js](create-uidefinition-overview.md)之後，您必須測試使用者經驗。 若要簡化測試，請使用在入口網站中載入檔案的沙箱環境。 您不需要實際部署受控應用程式。 沙箱會在目前的全螢幕入口網站體驗中呈現您的使用者介面。 或者，您可以使用腳本來測試介面。 本文中會展示這兩種方法。 沙箱是預覽介面的建議方式。

## <a name="prerequisites"></a>必要條件

* **createUiDefinition.json** 檔案。 如果您沒有此檔案，請複製[範例](https://github.com/Azure/azure-quickstart-templates/blob/master/100-marketplace-sample/createUiDefinition.json)檔案。

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="use-sandbox"></a>使用沙箱

1. 開啟 [[建立 UI 定義沙箱](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade)]。

   ![顯示沙箱](./media/test-createuidefinition/show-sandbox.png)

1. 將空白定義取代為檔案上 createUiDefinition.js的內容。 選取 [預覽]****。

   ![選取預覽](./media/test-createuidefinition/select-preview.png)

1. 您所建立的表單隨即顯示。 您可以逐步執行使用者體驗，並填入值。

   ![顯示表單](./media/test-createuidefinition/show-ui-form.png)

### <a name="troubleshooting"></a>疑難排解

如果您的表單在選取**預覽**之後不會顯示，您可能會發生語法錯誤。 在右側的捲軸上尋找紅色指示器，然後流覽至該指標。

![顯示語法錯誤](./media/test-createuidefinition/show-syntax-error.png)

如果您的表單未顯示，而您看到有卸載的雲端圖示，您的表單就會發生錯誤，例如遺漏的屬性。 在您的瀏覽器中開啟 Web 開發人員工具。 [主控台]**** 會顯示關於介面的重要訊息。

![顯示錯誤](./media/test-createuidefinition/show-error.png)

## <a name="use-test-script"></a>使用測試腳本

若要在入口網站中測試您的介面，請將下列其中一個指令碼複製到本機電腦：

* [PowerShell 側邊載入腳本-Az 模組](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-AzCreateUIDefinition.ps1)
* [PowerShell 側邊載入腳本-Azure 模組](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-CreateUIDefinition.ps1)
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

提供欄位的值。 完成後，您會看到傳遞至範本的值，您可以在瀏覽器的開發人員工具主控台中找到此範本。

![顯示值](./media/test-createuidefinition/show-json.png)

您可以使用這些值作為參數檔案來測試部署範本。

如果入口網站在 [摘要] 畫面上停止回應，輸出區段中可能會有錯誤。 例如，您可能參考了不存在的控制項。 如果輸出中的參數是空的，則參數可能會參考不存在的屬性。 例如，控制項的參考有效，但屬性參考無效。

## <a name="test-your-solution-files"></a>測試您的解決方案檔案

現在您已確認入口網站介面正常運作，接下來可以驗證您的 createUiDefinition 檔案是否與 mainTemplate.json 檔案正確整合。 您可以執行驗證指令碼測試來測試解決方案檔案的內容，包括 createUiDefinition 檔案。 指令碼會驗證 JSON 語法、檢查文字欄位上的 regex 運算式，並確定入口網站介面的輸出值符合您的範本參數。 如需執行此指令碼的相關資訊，請參閱[執行範本的靜態驗證檢查](https://github.com/Azure/azure-quickstart-templates/tree/master/test)。

## <a name="next-steps"></a>後續步驟

驗證入口網站介面後，請了解如何建立[在 Marketplace 中提供的 Azure 受控應用程式](publish-marketplace-app.md)。
