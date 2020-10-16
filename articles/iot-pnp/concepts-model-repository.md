---
title: 瞭解裝置模型儲存機制的概念 |Microsoft Docs
description: 作為解決方案開發人員或 IT 專業人員，瞭解裝置模型存放庫的基本概念。
author: rido-min
ms.author: rmpablos
ms.date: 09/30/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 4e15ef5256c1552fc8ab7fb9bd84f15bb3433834
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131355"
---
# <a name="device-model-repository"></a>裝置模型存放庫

裝置模型存放庫 (DMR) 可讓裝置產生器管理和共用 IoT 隨插即用裝置型號。 裝置型號是使用 [數位 Twins 模型語言 ](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)定義的 JSON LD 檔 (DTDL) 。

DMR 會根據裝置對應項模型識別碼 (DTMI) ，定義將 DTDL 介面儲存在資料夾結構中的模式。 您可以藉由將 DTMI 轉換為相對路徑，找出 DMR 中的介面。 例如，DTMI 會轉譯 `dtmi:com:example:Thermostat;1` 為 `/dtmi/com/example/thermostat-1.json` 。

## <a name="public-device-model-repository"></a>公用裝置模型存放庫

Microsoft 裝載具有下列特性的公用 DMR：

- 策劃模型。 Microsoft 會使用 GitHub 提取要求 (PR) 驗證工作流程，來審核並核准所有可用的介面。
- 不變性.  發佈之後，即無法更新介面。
- 超大規模。 Microsoft 提供必要的基礎結構來建立安全且可擴充的端點，您可以在其中發佈和取用裝置型號。

## <a name="custom-device-model-repository"></a>自訂裝置模型存放庫

您可以在任何儲存媒體（例如本機檔案系統或自訂 HTTP 網頁伺服器）中使用相同的 DMR 模式，以建立自訂 DMR。 您可以藉由變更用來存取 DMR 的基底 URL，從自訂 DMR 取得裝置模型的方式，就像從公用 DMR。

> [!NOTE]
> Microsoft 提供工具來驗證公用 DMR 中的裝置型號。 您可以在自訂存放庫中重複使用這些工具。

## <a name="public-models"></a>公用模型

儲存在模型存放庫中的公用裝置模型可供所有人使用，並在其應用程式中整合。 公用裝置模型可為裝置產生器和解決方案開發人員提供開放的生態系統，以共用並重複使用其 IoT 隨插即用裝置模型。

如需如何在模型存放庫中發行模型以使其成為公用的指示，請參閱「 [發行模型](#publish-a-model) 」一節。

使用者可以流覽、搜尋和查看官方 [GitHub 存放庫](https://github.com/Azure/iot-plugandplay-models)中的公用介面。

您 `dtmi` 也可以從公用端點取得資料夾中的所有介面 [https://devicemodels.azure.com](https://devicemodels.azure.com)

### <a name="resolve-models"></a>解析模型

若要以程式設計方式存取這些介面，您需要將 DTMI 轉換為可用於查詢公用端點的相對路徑。 下列程式碼範例將示範如何執行這項操作：

若要將 DTMI 轉換為絕對路徑，我們會使用函式 `DtmiToPath` ，並搭配 `IsValidDtmi` ：

```cs
static string DtmiToPath(string dtmi)
{
    if (!IsValidDtmi(dtmi))
    {
        return null;
    }
    // dtmi:com:example:Thermostat;1 -> dtmi/com/example/thermostat-1.json
    return $"/{dtmi.ToLowerInvariant().Replace(":", "/").Replace(";", "-")}.json";
}

static bool IsValidDtmi(string dtmi)
{
    // Regex defined at https://github.com/Azure/digital-twin-model-identifier#validation-regular-expressions
    Regex rx = new Regex(@"^dtmi:[A-Za-z](?:[A-Za-z0-9_]*[A-Za-z0-9])?(?::[A-Za-z](?:[A-Za-z0-9_]*[A-Za-z0-9])?)*;[1-9][0-9]{0,8}$");
    return rx.IsMatch(dtmi);
}
```

使用存放庫的結果路徑和基底 URL，我們可以取得介面：

```cs
const string _repositoryEndpoint = "https://devicemodels.azure.com";

string dtmiPath = DtmiToPath(dtmi.ToString());
string fullyQualifiedPath = $"{_repositoryEndpoint}{dtmiPath}";
string modelContent = await _httpClient.GetStringAsync(fullyQualifiedPath);
```

## <a name="publish-a-model"></a>發佈模型

> [!Important]
> 您必須擁有 GitHub 帳戶，才能將模型提交至公用 DMR。

1. 派生公用 GitHub 存放庫： [https://github.com/Azure/iot-plugandplay-models](https://github.com/Azure/iot-plugandplay-models) 。
1. 複製分支存放庫。 選擇性地建立新的分支，讓您的變更與 `main` 分支隔離。
1. 使用資料夾/檔案名慣例，將新介面新增至 `dtmi` 資料夾。 請參閱 [新增模型](#add-model) 工具。
1. 使用 [腳本驗證變更](#validate-files) 區段，在本機驗證裝置模型。
1. 在本機認可變更並推送至您的分支。
1. 從您的分叉建立以分支為目標的提取要求 `main` 。 請參閱 [建立問題或提取要求](https://docs.github.com/free-pro-team@latest/desktop/contributing-and-collaborating-using-github-desktop/creating-an-issue-or-pull-request) 檔。
1. 查看 [提取要求需求](https://github.com/Azure/iot-plugandplay-models/blob/main/pr-reqs.md)。

提取要求會觸發一系列的 GitHub 動作，以驗證新提交的介面，並確定您的提取要求符合所有檢查。

Microsoft 會在三個工作天內回應含有所有檢查的提取要求。

### <a name="add-model"></a>新增模型

下列步驟說明 add-model.js 腳本如何協助您新增介面。 此腳本需要 Node.js 才能執行：

1. 從命令提示字元中，流覽至本機 git 存放庫
1. `npm install`執行
1. `npm run add-model <path-to-file-to-add>`執行

查看主控台輸出中是否有任何錯誤訊息。

### <a name="local-validation"></a>本機驗證

您可以在提交提取要求之前，先在本機執行相同的驗證檢查，以協助事先診斷問題。

#### <a name="validate-files"></a>驗證-檔案

`npm run validate-files <file1.json> <file2.json>` 檢查檔案路徑是否符合預期的資料夾和檔案名。

#### <a name="validate-ids"></a>驗證識別碼

`npm run validate-ids <file1.json> <file2.json>` 檢查檔中定義的所有識別碼是否使用與主要識別碼相同的根。

#### <a name="validate-deps"></a>驗證-d

`npm run validate-deps <file1.json> <file2.json>` 檢查資料夾中是否有可用的相依性 `dtmi` 。

#### <a name="validate-models"></a>驗證-模型

您可以執行 [DTDL 驗證範例](https://github.com/Azure-Samples/DTDL-Validator) ，在本機驗證您的裝置型號。

## <a name="next-steps"></a>後續步驟

建議的下一個步驟是複習 [IoT 隨插即用架構](concepts-architecture.md)。
