---
title: 管理 Azure 自動化中的 Python 2 封裝
description: 本文說明如何管理 Azure 自動化中的 Python 2 封裝。
services: automation
ms.subservice: process-automation
ms.date: 02/25/2019
ms.topic: conceptual
ms.openlocfilehash: 05d892edf20cda228bc566b30b0b693ea7c4a184
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75417635"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>管理 Azure 自動化中的 Python 2 封裝

Azure 自動化可讓您在 Azure 和 Linux 混合式 Runbook 背景工作角色上執行 Python 2 Runbook。 若要協助簡化 Runbook，您可以使用 Python 封裝來匯入需要的模組。 本文說明如何在 Azure 自動化中管理和使用 Python 封裝。

## <a name="import-packages"></a>匯入套件

從您的自動化帳戶中，選取 [共用資源]**** 下的 [Python 2 封裝]****。 按一下 [+ 新增 Python 2 封裝]****。

![新增 Python 封裝](media/python-packages/add-python-package.png)

在 [新增 Python 2 封裝]**** 頁面上，選取要上載的本機封裝。 此封裝可為 `.whl` 檔案或 `.tar.gz` 檔案。 選取後，按一下 [確定]**** 以上載封裝。

![新增 Python 封裝](media/python-packages/upload-package.png)

導入包後，該包將列在自動化帳戶中的**Python 2 包**頁面上。 如果您需要移除封裝，請在封裝頁面上選取封裝，然後選擇 [刪除]****。

![套件清單](media/python-packages/package-list.png)

## <a name="import-packages-with-dependencies"></a>具有依賴項的導入包

Azure 自動化不會在導入過程中解決 python 包的依賴項。 有兩種方法可以導入具有其所有依賴項的包。 只需使用以下步驟之一才能將包導入自動化帳戶。

### <a name="manually-download"></a>手動下載

在安裝了[python2.7](https://www.python.org/downloads/release/latest/python2)和[pip](https://pip.pypa.io/en/stable/)的 Windows 64 位電腦上，運行以下命令以下載包及其所有依賴項：

```cmd
C:\Python27\Scripts\pip2.7.exe download -d <output dir> <package name>
```

下載包後，您可以將其導入自動化帳戶。

### <a name="runbook"></a>Runbook

將 python runbook[導入 Python 2 包從 pypi 導入 Azure 自動化帳戶](https://gallery.technet.microsoft.com/scriptcenter/Import-Python-2-packages-57f7d509)，從庫導入自動化帳戶。 確保回合設定設置為**Azure，** 然後使用參數啟動 Runbook。 Runbook 需要"以帳戶身份運行"功能，以便自動化帳戶正常工作。 對於每個參數，請確保使用以下清單和圖像中的開關啟動它：

* -s\<訂閱 Id\>
* -g\<資源組\>
* -一\<個自動化帳戶\>
* -m\<模組包\>

![套件清單](media/python-packages/import-python-runbook.png)

Runbook 允許您指定要下載的包，例如`Azure`（第四個參數）將下載所有 Azure 模組及其所有依賴項（大約 105 個）。

運行簿完成後，可以檢查自動化帳戶中的**共用資源**下的**Python 2 包**頁面，以驗證其包是否正確導入。

## <a name="use-a-package-in-a-runbook"></a>在 Runbook 中使用封裝

導入包後，現在可以在 Runbook 中使用它。 下列範例會使用 [Azure 自動化公用程式封裝](https://github.com/azureautomation/azure_automation_utility)。 此封裝可讓您更輕鬆配合使用 Python 與 Azure 自動化。 若要使用此封裝，請遵循 GitHub 存放庫中的指示，並透過使用 `from azure_automation_utility import get_automation_runas_credential` 將其新增至 Runbook (例如，匯入函式以擷取 RunAs 帳戶)。

```python
import azure.mgmt.resource
import automationassets
from azure_automation_utility import get_automation_runas_credential

# Authenticate to Azure using the Azure Automation RunAs service principal
runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
azure_credential = get_automation_runas_credential()

# Intialize the resource management client with the RunAs credential and subscription
resource_client = azure.mgmt.resource.ResourceManagementClient(
    azure_credential,
    str(runas_connection["SubscriptionId"]))

# Get list of resource groups and print them out
groups = resource_client.resource_groups.list()
for group in groups:
    print group.name
```

## <a name="develop-and-test-runbooks-offline"></a>離線開發和測試 Runbook

若要離線開發和測試 Python 2 Runbook，您可以使用 GitHub 上的 [Azure 自動化 Python 模擬資產模組](https://github.com/azureautomation/python_emulated_assets)。 此模組可讓您參考共用的資源，例如認證、變數、連線和憑證。

## <a name="next-steps"></a>後續步驟

若要開始使用 Python 2 Runbook，請參閱[我的第一個 Python 2 Runbook](automation-first-runbook-textual-python2.md)
