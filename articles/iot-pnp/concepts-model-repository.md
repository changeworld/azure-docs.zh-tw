---
title: 瞭解裝置模型儲存機制的概念 |Microsoft Docs
description: 作為解決方案開發人員或 IT 專業人員，瞭解裝置模型存放庫的基本概念。
author: rido-min
ms.author: rmpablos
ms.date: 11/17/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: b567efe2541bb33c905def73bb78398799b4ed69
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/19/2020
ms.locfileid: "94920537"
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

使用相同的 DMR 模式，在任何儲存媒體中建立自訂 DMR，例如本機檔案系統或自訂 HTTP 網頁伺服器。 您可以藉由變更用來存取 DMR 的基底 URL，從自訂 DMR 取得裝置模型，方法與從公用 DMR 的方式相同。

> [!NOTE]
> Microsoft 提供工具來驗證公用 DMR 中的裝置型號。 您可以在自訂存放庫中重複使用這些工具。

## <a name="public-models"></a>公用模型

儲存在模型存放庫中的公用裝置模型可供所有人使用，並在其應用程式中整合。 公用裝置模型可為裝置產生器和解決方案開發人員提供開放的生態系統，以共用並重複使用其 IoT 隨插即用裝置模型。

如需如何在模型存放庫中發行模型以使其成為公用的指示，請參閱「 [發行模型](#publish-a-model) 」一節。

使用者可以流覽、搜尋和查看官方 [GitHub 存放庫](https://github.com/Azure/iot-plugandplay-models)中的公用介面。

您 `dtmi` 也可以從公用端點取得資料夾中的所有介面 [https://devicemodels.azure.com](https://devicemodels.azure.com)

### <a name="resolve-models"></a>解析模型

若要以程式設計方式存取這些介面，您需要將 DTMI 轉換為可用於查詢公用端點的相對路徑。

若要將 DTMI 轉換為絕對路徑，請使用 `DtmiToPath` 函數搭配 `IsValidDtmi` ：

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
1. 使用資料夾/檔案名慣例，將新介面新增至 `dtmi` 資料夾。 若要深入瞭解，請參閱將 [模型匯入到 `dtmi/` 資料夾](#import-a-model-to-the-dtmi-folder)。
1. 使用工具在本機驗證模型 `dmr-client` 。 若要深入瞭解，請參閱 [驗證模型](#validate-models)。
1. 在本機認可變更並推送至您的分支。
1. 從您的分叉建立以分支為目標的提取要求 `main` 。 請參閱 [建立問題或提取要求](https://docs.github.com/free-pro-team@latest/desktop/contributing-and-collaborating-using-github-desktop/creating-an-issue-or-pull-request) 檔。
1. 查看 [提取要求需求](https://github.com/Azure/iot-plugandplay-models/blob/main/pr-reqs.md)。

提取要求會觸發一組 GitHub 動作來驗證提交的介面，並確定您的提取要求符合所有需求。

Microsoft 會在三個工作天內回應含有所有檢查的提取要求。

### <a name="dmr-client-tools"></a>`dmr-client` 工具

在 PR 檢查期間用來驗證模型的工具，也可以用來在本機新增和驗證 DTDL 介面。

> [!NOTE]
> 此工具需要 [.NET SDK](https://dotnet.microsoft.com/download) 3.1 版或更新版本。

### <a name="install-dmr-client"></a>安裝 `dmr-client`

```bash
curl -L https://aka.ms/install-dmr-client-linux | bash
```

```powershell
iwr https://aka.ms/install-dmr-client-windows -UseBasicParsing | iex
```

### <a name="import-a-model-to-the-dtmi-folder"></a>將模型匯入到 `dtmi/` 資料夾

如果您已將模型儲存在 json 檔案中，您可以使用 `dmr-client import` 命令將它們新增至 `dtmi/` 具有正確檔案名的資料夾：

```bash
# from the local repo root folder
dmr-client import --model-file "MyThermostat.json"
```

> [!TIP]
> 您可以使用 `--local-repo` 引數來指定本機儲存機制的根資料夾。

### <a name="validate-models"></a>驗證模型

您可以使用下列命令來驗證您的模型 `dmr-client validate` ：

```bash
dmr-client validate --model-file ./my/model/file.json
```

> [!NOTE]
> 驗證會使用最新的 DTDL 剖析器版本，以確保所有介面都與 DTDL 語言規格相容。

若要驗證外部相依性，它們必須存在於本機儲存機制中。 若要驗證模型，請使用 `--repo` 選項來指定 `local` 或 `remote` 資料夾來解析相依性：

```bash
# from the repo root folder
dmr-client validate --model-file ./my/model/file.json --repo .
```

### <a name="strict-validation"></a>嚴格驗證

DMR 包含其他 [需求](https://github.com/Azure/iot-plugandplay-models/blob/main/pr-reqs.md)，請使用 `stict` 旗標針對它們驗證您的模型：

```bash
dmr-client validate --model-file ./my/model/file.json --repo . --strict true
```

檢查主控台輸出是否有任何錯誤訊息。

### <a name="export-models"></a>匯出模型

您可以使用 JSON 陣列，從指定的存放庫 (本機或遠端) 將模型匯出至單一檔案：

```bash
dmr-client export --dtmi "dtmi:com:example:TemperatureController;1" -o TemperatureController.expanded.json
```

## <a name="next-steps"></a>後續步驟

建議的下一個步驟是複習 [IoT 隨插即用架構](concepts-architecture.md)。
