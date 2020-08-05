---
title: 針對 Azure Functions 中的 Python 函式應用程式進行疑難排解
description: 瞭解如何針對 Python 功能進行疑難排解。
author: Hazhzeng
ms.topic: article
ms.date: 07/29/2020
ms.author: hazeng
ms.custom: tracking-python
ms.openlocfilehash: e5155d426a57a306b00860285be18c2f8fc9b18d
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/04/2020
ms.locfileid: "87567888"
---
# <a name="troubleshoot-python-errors-in-azure-functions"></a>針對 Azure Functions 中的 Python 錯誤進行疑難排解

以下是 Python 功能常見問題的疑難排解指南清單：

* [ModuleNotFoundError 和 ImportError](#troubleshoot-modulenotfounderror)
* [無法匯入 ' cygrpc '](#troubleshoot-cannot-import-cygrpc)

## <a name="troubleshoot-modulenotfounderror"></a>針對 ModuleNotFoundError 進行疑難排解

本節可協助您針對 Python 函式應用程式中的模組相關錯誤進行疑難排解。 這些錯誤通常會導致下列 Azure Functions 錯誤訊息：

> `Exception: ModuleNotFoundError: No module named 'module_name'.`

當 Python 函式應用程式無法載入 Python 模組時，就會發生此錯誤問題。 此錯誤的根本原因是下列其中一個問題：

- [找不到套件](#the-package-cant-be-found)
- [未使用適當的 Linux Wheel 來解析套件](#the-package-isnt-resolved-with-proper-linux-wheel)
- [套件與 Python 解譯器版本不相容](#the-package-is-incompatible-with-the-python-interpreter-version)
- [套件與其他套件發生衝突](#the-package-conflicts-with-other-packages)
- [套件僅支援 Windows 或 macOS 平台](#the-package-only-supports-windows-or-macos-platforms)

### <a name="view-project-files"></a>檢視專案檔

若要識別出問題的實際原因，您需要取得在函式應用程式上執行的 Python 專案檔。 如果您的本機電腦上沒有專案檔，可以使用下列其中一種方式來取得：

- 如果函式應用程式具有 `WEBSITE_RUN_FROM_PACKAGE` 應用程式設定且其值為 URL，則可將 URL 複製並貼到您的瀏覽器，以下載檔案。
- 如果函式應用程式具有 `WEBSITE_RUN_FROM_PACKAGE` 且已將其設定為 `1`，請瀏覽至 `https://<app-name>.scm.azurewebsites.net/api/vfs/data/SitePackages`，並從最新的 `href` URL 下載檔案。
- 如果函式應用程式不具上述應用程式設定，請瀏覽至 `https://<app-name>.scm.azurewebsites.net/api/settings`，並在 `SCM_RUN_FROM_PACKAGE` 底下尋找 URL。 藉由將 URL 複製並貼到瀏覽器來下載檔案。
- 如果這些都不適用，請瀏覽至 `https://<app-name>.scm.azurewebsites.net/DebugConsole`，並顯示 `/home/site/wwwroot` 底下的內容。

此文章的其餘部分可協助您藉由檢查函式應用程式的內容、識別根本原因並解決特定問題，以針對此錯誤的可能原因進行疑難排解。

### <a name="diagnose-modulenotfounderror"></a>診斷 ModuleNotFoundError

此節將詳細說明模組相關錯誤的可能根本原因。 當您識別出可能的根本原因之後，即可移至相關的緩和措施。

#### <a name="the-package-cant-be-found"></a>找不到套件

瀏覽至 `.python_packages/lib/python3.6/site-packages/<package-name>` 或 `.python_packages/lib/site-packages/<package-name>`。 如果檔案路徑不存在，則這個遺失路徑可能就是根本原因。

部署期間使用協力廠商或過時的工具可能會導致此問題。

如需緩和措施，請參閱[啟用遠端組建](#enable-remote-build)或[建置原生相依性](#build-native-dependencies)。

#### <a name="the-package-isnt-resolved-with-proper-linux-wheel"></a>未使用適當的 Linux Wheel 來解析套件

移至 `.python_packages/lib/python3.6/site-packages/<package-name>-<version>-dist-info` 或 `.python_packages/lib/site-packages/<package-name>-<version>-dist-info`。 使用您慣用的文字編輯器來開啟 **Wheel** 檔案，然後檢查 **Tag:** 區段。 如果標記的值未包含 **linux**，這可能就是問題。

Python 函式只會在 Azure 中的 Linux 上執行：函式執行階段 v2.x 會在 Debian Stretch 上及 Debian Buster 上的 v3.x 執行階段執行。 成品預期會包含正確的 Linux 二進位檔。 在核心工具、協力廠商或過時的工具中使用 `--build local` 旗標，可能導致使用較舊的二進位檔。

如需緩和措施，請參閱[啟用遠端組建](#enable-remote-build)或[建置原生相依性](#build-native-dependencies)。

#### <a name="the-package-is-incompatible-with-the-python-interpreter-version"></a>套件與 Python 解譯器版本不相容

移至 `.python_packages/lib/python3.6/site-packages/<package-name>-<version>-dist-info` 或 `.python_packages/lib/site-packages/<package-name>-<version>-dist-info`。 使用文字編輯器，開啟中繼資料檔案，並檢查 **Classifiers:** 區段。 如果該區段未包含 `Python :: 3`、`Python :: 3.6`、`Python :: 3.7` 或 `Python :: 3.8`，這表示套件版本可能太舊，或者很可能該套件已不在維護中。

您可以從 [Azure 入口網站](https://portal.azure.com)檢查函式應用程式的 Python 版本。 瀏覽至您的函式應用程式、選擇 [資源總管]，然後選取 [執行]。

:::image type="content" source="media/recover-module-not-found/resource-explorer.png" alt-text="在入口網站中針對函式應用程式開啟資源總管":::

在資源總管載入之後，搜尋 **LinuxFxVersion**，其會顯示 Python 版本。

如需緩和措施，請參閱[將套件更新為最新版本](#update-your-package-to-the-latest-version)或[將套件取代為對等項目](#replace-the-package-with-equivalents)。

#### <a name="the-package-conflicts-with-other-packages"></a>套件與其他套件發生衝突

如果您確認已正確使用適當的 Linux Wheel 來解析套件，則可能會與其他套件發生衝突。 在某些套件中，PyPi 文件可能會釐清不相容的模組。 例如，在 [`azure 4.0.0`](https://pypi.org/project/azure/4.0.0/) 中，有一則如下的敘述：

<pre>This package isn't compatible with azure-storage.
If you installed azure-storage, or if you installed azure 1.x/2.x and didn’t uninstall azure-storage,
you must uninstall azure-storage first.</pre>

您可以在 `https://pypi.org/project/<package-name>/<package-version>` 中找到套件版本的文件。

如需緩和措施，請參閱[將套件更新為最新版本](#update-your-package-to-the-latest-version)或[將套件取代為對等項目](#replace-the-package-with-equivalents)。

#### <a name="the-package-only-supports-windows-or-macos-platforms"></a>套件僅支援 Windows 或 macOS 平台

使用文字編輯器開啟 `requirements.txt`，並檢查 `https://pypi.org/project/<package-name>` 中的套件。 某些套件只能在 Windows 或 macOS 平台上執行。 例如，pywin32 只能在 Windows 上執行。

當您使用 Windows 或 macOS 進行本機開發時，可能不會發生 `Module Not Found` 錯誤。 不過，無法在執行階段使用 Linux 的 Azure Functions 上匯入套件。 這可能是因為在專案初始化期間，使用 `pip freeze` 來將虛擬環境匯出至 Windows 或 macOS 機器的 requirements.txt 所導致。

如需緩和措施，請參閱[將套件取代為對等項目](#replace-the-package-with-equivalents)或[製作 requirements.txt](#handcraft-requirementstxt)。

### <a name="mitigate-modulenotfounderror"></a>緩和 ModuleNotFoundError

以下是模組相關問題的可能緩和措施。 使用[上述診斷](#diagnose-modulenotfounderror)來判斷要嘗試使用哪一個緩和措施。

#### <a name="enable-remote-build"></a>啟用遠端組建

確定已啟用遠端組建。 執行此動作的方式取決於您的部署方法。

## <a name="visual-studio-code"></a>[Visual Studio Code](#tab/vscode)
確定已安裝[適用於 Visual Studio Code 的 Azure Functions 延伸模組](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)的最新版本。 確認 `.vscode/settings.json` 存在，且其包含 `"azureFunctions.scmDoBuildDuringDeployment": true` 設定。 如果沒有，請在啟用 `azureFunctions.scmDoBuildDuringDeployment` 設定的情況下建立此檔案，然後重新部署專案。

## <a name="azure-functions-core-tools"></a>[Azure Functions Core Tools](#tab/coretools)

確定已安裝最新版的 [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools/releases)。 移至您的區域函式專案資料夾，然後使用 `func azure functionapp publish <app-name>` 進行部署。

## <a name="manual-publishing"></a>[手動發佈](#tab/manual)

如果您要手動將套件發佈到 `https://<app-name>.scm.azurewebsites.net/api/zipdeploy` 端點，確定已將 **SCM_DO_BUILD_DURING_DEPLOYMENT** 和 **ENABLE_ORYX_BUILD** 設定為 **True**。 若要深入了解，請參閱[如何使用應用程式設定](functions-how-to-use-azure-function-app-settings.md#settings)。

---

#### <a name="build-native-dependencies"></a>建置原生相依性

確定已安裝最新版的 **Docker** 和 [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools/releases)。 移至您的區域函式專案資料夾，然後使用 `func azure functionapp publish <app-name> --build-native-deps` 進行部署。

#### <a name="update-your-package-to-the-latest-version"></a>將套件更新為最新版本

瀏覽 `https://pypi.org/project/<package-name>` 中的最新套件版本，並檢查 **Classifiers:** 區段。 套件應該是 `OS Independent`，或與**作業系統**中的 `POSIX` 或 `POSIX :: Linux` 相容。 此外，程式設計語言應該包含 `Python :: 3`、`Python :: 3.6`、`Python :: 3.7` 或 `Python :: 3.8`。

如果這些正確，您就能透過變更 requirements.txt 中的 `<package-name>~=<latest-version>` 行，來將套件更新為最新版本。

#### <a name="handcraft-requirementstxt"></a>製作 requirements.txt

有些開發人員會使用 `pip freeze > requirements.txt`，來產生可供其開發環境使用的 Python 套件清單。 雖然這在大部分案例中都應該可行，但可能會在跨平台部署案例中發生問題，例如，在 Windows 或 macOS 上本機開發函式，但要發佈到在 Linux 上執行的函式應用程式。 在此案例中，`pip freeze` 可能會為您的本機開發環境引進非預期的作業系統特定相依性或相依性。 這些相依性可能會在 Linux 上執行時中斷 Python 函式應用程式。

最佳做法是在專案原始程式碼中檢查每個 .py 檔案的匯入陳述式，並且只在 requirements.txt 檔案中簽入那些模組。 這保證能夠在不同作業系統上正確處理套件的解析。

#### <a name="replace-the-package-with-equivalents"></a>將套件取代為對等項目

首先，我們應該在 `https://pypi.org/project/<package-name>` 中查看套件的最新版本。 此套件通常會有自己的 GitHub 頁面，請移至 GitHub 上的**問題**一節，並搜尋是否已修正您的問題。 如已修正，則將套件更新為最新版本。

有時，可能已將套件整合到 [Python 標準程式庫](https://docs.python.org/3/library/) \(英文\) (例如 pathlib)。 若是如此，由於我們會在 Azure Functions 中提供特定的 Python 發行版本 (Python 3.6、Python 3.7 和 Python 3.8)，因此應移除您 requirements.txt 中的套件。

不過，如果您遇到尚未修正的問題，而且正面臨截止期限。 建議您進行一些研究，並為您的專案尋找類似的套件。 Python 社群通常將為您提供可使用的各種類似程式庫。

---

## <a name="troubleshoot-cannot-import-cygrpc"></a>無法匯入 ' cygrpc ' 的疑難排解

本節可協助您針對 Python 函數應用程式中的「cygrpc」相關錯誤進行疑難排解。 這些錯誤通常會導致下列 Azure Functions 錯誤訊息：

> `Cannot import name 'cygrpc' from 'grpc._cython'`

當 Python 函式應用程式無法以適當的 Python 解譯器啟動時，就會發生此錯誤問題。 此錯誤的根本原因是下列其中一個問題：

- [Python 解譯器不相符的 OS 架構](#the-python-interpreter-mismatches-os-architecture)
- [Azure Functions Python 背景工作角色不支援 Python 解譯器](#the-python-interpreter-is-not-supported-by-azure-functions-python-worker)

### <a name="diagnose-cygrpc-reference-error"></a>診斷 ' cygrpc ' 參考錯誤

#### <a name="the-python-interpreter-mismatches-os-architecture"></a>Python 解譯器不相符的 OS 架構

這很可能是因為32位的 Python 解譯器安裝在您的64位作業系統上。

如果您是在 x64 作業系統上執行，請確定您的 Python 3.6、3.7 或3.8 解譯器也是在64位版本上。

您可以使用下列命令來檢查您的 Python 解譯器位：

在 PowerShell 中的 Windows 上：`py -c 'import platform; print(platform.architecture()[0])'`

在類似 Unix 的 shell 上：`python3 -c 'import platform; print(platform.architecture()[0])'`

如果 Python 解譯器位與作業系統架構不相符，請從[Python Software Foundation](https://python.org/downloads/release)下載適當的 python 解譯器。

#### <a name="the-python-interpreter-is-not-supported-by-azure-functions-python-worker"></a>Azure Functions Python 背景工作角色不支援 Python 解譯器

Azure Functions Python 背景工作角色僅支援 Python 3.6、3.7 和3.8。
請檢查您的 Python 解譯器是否符合我們 `py --version` 在 Windows 或 `python3 --version` Unix 之類的系統中的預期版本。 確定傳回結果為 Python 3.6. x、Python 3.7. x 或 Python 3.8. x。

如果您的 Python 解譯器版本不符合預期，請從[Python Software Foundation](https://python.org/downloads/release)下載 python 3.6、3.7 或3.8 解譯器。

## <a name="next-steps"></a>後續步驟

如果您無法解決問題，請向函數小組回報：

> [!div class="nextstepaction"]
> [回報未解決的問題](https://github.com/Azure/azure-functions-python-worker/issues)
