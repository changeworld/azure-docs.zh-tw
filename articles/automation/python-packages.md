---
title: 管理 Azure 自動化中的 Python 2 封裝
description: 本文說明如何管理 Azure 自動化中的 Python 2 套件。
services: automation
ms.subservice: process-automation
ms.date: 02/25/2019
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 0b0fab40eb95fabd4b74e809c9e6cd6f6573f513
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87852900"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>管理 Azure 自動化中的 Python 2 封裝

Azure 自動化可讓您在 Azure 和 Linux 混合式 Runbook 背景工作角色上執行 Python 2 Runbook。 若要協助簡化 Runbook，您可以使用 Python 封裝來匯入需要的模組。 本文說明如何在 Azure 自動化中管理和使用 Python 套件。

## <a name="import-packages"></a>匯入套件

從您的自動化帳戶中，選取 [共用資源] 下的 [Python 2 套件]。 按一下 [+ 新增 Python 2 封裝]。

![新增 Python 封裝](media/python-packages/add-python-package.png)

在 [新增 Python 2 套件] 頁面上，選取要上載的本機套件。 此套件可以是 **.whl** 或 **.tar.gz** 檔案。 選取套件後，按一下 [確定] 將其上傳。

![新增 Python 封裝](media/python-packages/upload-package.png)

套件匯入後，會列在您自動化帳戶中的 [Python 2 套件] 頁面上。 如果您需要移除套件，請選取套件，然後按一下 [刪除]。

![套件清單](media/python-packages/package-list.png)

## <a name="import-packages-with-dependencies"></a>匯入具有相依性的套件

在匯入過程中，Azure 自動化不會解析 Python 套件的相依性。 有兩種方式可匯入套件及其所有的相依性。 將套件匯入至您的自動化帳戶時，只需執行下列其中一個步驟。

### <a name="manually-download"></a>手動下載

在已安裝 [Python2.7](https://www.python.org/downloads/release/latest/python2) 和 [pip](https://pip.pypa.io/en/stable/) 的 Windows 64 位元電腦上，請執行下列命令以下載套件及其所有的相依性：

```cmd
C:\Python27\Scripts\pip2.7.exe download -d <output dir> <package name>
```

下載套件後，您可以將其匯入至您的自動化帳戶。

### <a name="runbook"></a>Runbook

 若要取得 Runbook，請從資源庫[將 Python 2 套件從 pypi 匯入至您的 Azure 自動化自動化帳戶](https://gallery.technet.microsoft.com/scriptcenter/Import-Python-2-packages-57f7d509)。 請確定 [回合設定] 已設定為 **Azure**，並使用參數啟動 Runbook。 使用 Runbook 時，必須要有執行身分帳戶，自動化帳戶才能運作。 對於每個參數，請確實使用參數加以啟動，如下列清單和影像所示：

* -s\<subscriptionId\>
* -g\<resourceGroup\>
* -a\<automationAccount\>
* -m\<modulePackage\>

![套件清單](media/python-packages/import-python-runbook.png)

Runbook 可讓您指定要下載的套件。 例如，使用 `Azure` 參數會下載所有的 Azure 模組和所有相依性 (大約 105 個)。

Runbook 完成後，您可以在自動化帳戶的 [共用資源] 底下查看 [Python 2 套件]，以確認套件已正確匯入。

## <a name="use-a-package-in-a-runbook"></a>在 Runbook 中使用封裝

套件匯入後，您就可以在 Runbook 中加以使用。 下列範例會使用 [Azure 自動化公用程式套件](https://github.com/azureautomation/azure_automation_utility)。 此封裝可讓您更輕鬆配合使用 Python 與 Azure 自動化。 若要使用此套件，請依照 GitHub 存放庫中的指示操作，並將其新增至 Runbook。 例如，您可以使用 `from azure_automation_utility import get_automation_runas_credential` 匯入用來擷取執行身分帳戶的函式。

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

若要準備 Python Runbook，請參閱[建立 Python Runbook](learn/automation-tutorial-runbook-textual-python2.md)。
