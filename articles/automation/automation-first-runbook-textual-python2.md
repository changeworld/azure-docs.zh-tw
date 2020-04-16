---
title: 我在 Azure 自動化中的第一個 Python Runbook
description: 教學課程將逐步引導您建立、測試和發佈簡單的 Python Runbook。
services: automation
ms.subservice: process-automation
ms.date: 03/19/2019
ms.topic: conceptual
ms.openlocfilehash: b788e5bda65702305ed6f6b001b57c28939aa875
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405903"
---
# <a name="my-first-python-runbook"></a>我的第一個 Python Runbook

> [!div class="op_single_selector"]
> - [圖形化](automation-first-runbook-graphical.md)
> - [PowerShell](automation-first-runbook-textual-powershell.md)
> - [PowerShell 工作流程](automation-first-runbook-textual.md)
> - [Python](automation-first-runbook-textual-python2.md)

本教學課程將逐步引導您在「Azure 自動化」中建立 [Python Runbook](automation-runbook-types.md#python-runbooks)。 從您測試及發佈的簡單 Runbook 開始。 接著您會修改 Runbook 以實際管理 Azure 資源，在此情況下會啟動 Azure 虛擬機器。 最後您藉由新增 Runbook 參數，讓 Runbook 更穩固。

> [!NOTE]
> 不支援使用 Webhook 啟動 Python 執行簿。

## <a name="prerequisites"></a>Prerequisites

若要完成此教學課程，您需要下列項目：

- Azure 訂用帳戶。 如果您沒有這類帳戶，可以[啟用自己的 MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或註冊[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [自動化帳戶](automation-offering-get-started.md) ，用來保存 Runbook 以及向 Azure 資源驗證。 此帳戶必須擁有啟動和停止虛擬機器的權限。
- Azure 虛擬機器。 您會停止並啟動這部電腦，因此它不該是生產 VM。

## <a name="create-a-new-runbook"></a>建立新的 Runbook

您會由建立一個可輸出 Hello World** 文字的簡單 Runbook 開始。

1. 在 Azure 入口網站中，開啟您的自動化帳戶。

    [自動化帳戶] 頁面提供這個帳戶中資源的快速檢視。 您應該已經有一些資產。 其中大部分資產是自動包含在新自動化帳戶的模組。 您應該也擁有 [必要條件](#prerequisites)中所述的認證資產。<br>

1. 在 **「流程自動化**」下選擇**Runbook**以打開 Runbook 清單。
1. 選擇 **「新增執行簿**」 以建立新的 Runbook。
1. 將 Runbook 命名為「MyFirstRunbook-Python」****。
1. 為**Runbook 類型**選擇**Python 2。**
1. 按一下 [建立] **** 來建立 Runbook 並開啟文字式編輯器。

## <a name="add-code-to-the-runbook"></a>將程式碼加入 Runbook

現在新增簡單的指令來列印文字`Hello World`。

```python
print("Hello World!")
```

按一下 [儲存]**** 以儲存 Runbook。

## <a name="test-the-runbook"></a>測試 Runbook

在您發佈 Runbook 之前，為了使其可用於生產環境，您想要測試以確定它可以正常運作。 測試 Runbook 時，您會執行其 草稿 版本，並以互動方式檢視其輸出。

1. 按一下 [測試窗格] **** 來開啟 [測試] 窗格。
1. 按一下 [開始] **** 以開始測試。 這應該是唯一啟用的選項。
1. 隨即會建立 [Runbook 工作](automation-runbook-execution.md) ，並顯示其狀態。
   作業狀態以「已排隊」開頭,表示正在等待雲中的 Runbook 工作人員可用。 當背景工作角色宣告該作業時，狀態會變更為 [正在開始]，然後 Runbook 真正開始執行時再變更為 [執行中]。
1. Runbook 工作完成時，會顯示其輸出。 在這種情況下,您應該看到`Hello World`。
1. 關閉 [測試] 窗格以返回畫布。

## <a name="publish-and-start-the-runbook"></a>發佈和啟動 Runbook

您建立的 Runbook 仍處於 [草稿] 模式。 您需要將它發佈，才能在生產環境中執行它。
發佈 Runbook 時,使用草稿版本覆蓋現有已發佈版本。
在此情況下，因為您剛剛建立 Runbook，所以還沒有已發佈的版本。

1. 按一下 [發佈]**** 來發佈 Runbook，然後出現提示時按一下 [是]****。
1. 如果向左捲動以檢視 Runbook 頁上的**Runbook,** 則應看到**已發布****的創作狀態**。
1. 捲動回到右側，檢視 [MyFirstRunbook-Python]**** 的窗格。
   頂部的選項允許您啟動 Runbook、查看 Runbook 或計劃它在未來某個時間啟動。
2. 按下 **「開始」** 然後在「開始執行簿」 邊欄選項卡打開時按一下**確定**。
3. 為您建立的 Runbook 作業打開工作窗格。 您可以關閉此窗格,但讓我們將其保持打開狀態,以便可以監視作業的進度。
1. [作業摘要]**** 中會顯示作業狀態，且符合當您測試 Runbook 時看到的狀態。
2. 執行簿狀態顯示"已完成"後,按一下「**輸出**」。 將開啟輸出表單,您可以在其中檢視`Hello World`。
3. 關閉 [輸出] 窗格。
4. 按一下 [所有記錄] **** 以開啟 Runbook 作業的 [資料流] 窗格。 應僅在輸出流`Hello World`中看到。 但是,如果 Runbook 寫入 Runbook 作業,則此窗格可以顯示 Runbook 作業的其他流,如"詳細"和"錯誤"。
5. 關閉 [資料流] 窗格和 [工作] 窗格，以返回 [MyFirstRunbook-Python] 窗格。
6. 按下 **「作業**」可打開此 Runbook 的作業頁面。 此頁列出此 Runbook 創建的所有作業。 由於您只執行一次作業，因此應該只會看到列出一項作業。
7. 您可以按一下此作業，以開啟您啟動 Runbook 時所檢視的相同 [作業] 窗格。 此窗格允許您返回時間並查看為特定 Runbook 創建的任何作業的詳細資訊。

## <a name="add-authentication-to-manage-azure-resources"></a>加入驗證來管理 Azure 資源

您已測試並發行您的 Runbook，但是到目前為止，它似乎並不實用。 您想要讓它管理 Azure 資源。
為此,腳本必須使用來自自動化帳戶的憑據進行身份驗證。 為了協助您，您可以使用 [Azure 自動化公用程式套件](https://github.com/azureautomation/azure_automation_utility)更輕鬆地進行驗證並與 Azure 資源互動。

> [!NOTE]
> 自動化帳戶必須使用服務主體功能創建,才能有"運行為證書"。
> 如果自動化帳戶不是使用服務主體創建的,則可以按照 Python 的 Azure[管理庫進行身份驗證](/azure/python/python-sdk-azure-authenticate)中所述進行身份驗證。

1. 按一下 [MyFirstRunbook-Python] 窗格上的 [編輯]**** 以開啟文字編輯器。

2. 加入下列程式碼，對 Azure 進行驗證：

   ```python
   import os
   from azure.mgmt.compute import ComputeManagementClient
   import azure.mgmt.resource
   import automationassets

   def get_automation_runas_credential(runas_connection):
       from OpenSSL import crypto
       import binascii
       from msrestazure import azure_active_directory
       import adal

       # Get the Azure Automation RunAs service principal certificate
       cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
       pks12_cert = crypto.load_pkcs12(cert)
       pem_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM,pks12_cert.get_privatekey())

       # Get run as connection information for the Azure Automation service principal
       application_id = runas_connection["ApplicationId"]
       thumbprint = runas_connection["CertificateThumbprint"]
       tenant_id = runas_connection["TenantId"]

       # Authenticate with service principal certificate
       resource ="https://management.core.windows.net/"
       authority_url = ("https://login.microsoftonline.com/"+tenant_id)
       context = adal.AuthenticationContext(authority_url)
       return azure_active_directory.AdalAuthentication(
       lambda: context.acquire_token_with_client_certificate(
               resource,
               application_id,
               pem_pkey,
               thumbprint)
       )

   # Authenticate to Azure using the Azure Automation RunAs service principal
   runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
   azure_credential = get_automation_runas_credential(runas_connection)
   ```

## <a name="add-code-to-create-python-compute-client-and-start-the-vm"></a>加入程式碼以建立 Python 計算用戶端，並啟動虛擬機器

若要使用 Azure 虛擬機器，請建立 [適用於 Python 的 Azure 計算用戶端](https://docs.microsoft.com/python/api/azure-mgmt-compute/azure.mgmt.compute.computemanagementclient)的執行個體。

使用計算用戶端啟動 VM。 將下列程式碼加入 Runbook：

```python
# Initialize the compute management client with the RunAs credential and specify the subscription to work against.
compute_client = ComputeManagementClient(
    azure_credential,
    str(runas_connection["SubscriptionId"])
)


print('\nStart VM')
async_vm_start = compute_client.virtual_machines.start(
    "MyResourceGroup", "TestVM")
async_vm_start.wait()
```

包含`MyResourceGroup`VM 的資源組的名稱`TestVM`在哪裡, 並且是要啟動的 VM 的名稱。

測試並再次執行 Runbook，看看它是否啟動虛擬機器。

## <a name="use-input-parameters"></a>使用輸入參數

Runbook 目前對資源組和 VM 的名稱使用硬編碼值。
現在讓我們來新增程式碼，以從輸入參數獲取這些值。

您使用 `sys.argv` 變數以取得參數值。
在其他 `import` 陳述式之後，立即將下列程式碼加入 Runbook：

```python
import sys

resource_group_name = str(sys.argv[1])
vm_name = str(sys.argv[2])
```

這樣會匯入 `sys` 模組，並建立兩個變數來保留資源群組和虛擬機器名稱。
請注意，引數清單的元素 `sys.argv[0]` 是指令碼名稱，而不是由使用者輸入。

現在您可以修改 Runbook 的最後兩行以使用輸入參數值，而不是使用硬式編碼的值：

```python
async_vm_start = compute_client.virtual_machines.start(
    resource_group_name, vm_name)
async_vm_start.wait()
```

啟動 Python Runbook 時(在「測試」窗格上或作為已發布的 Runbook),可以在 **「參數**」下的「開始執行簿」頁中輸入參數的值。

在第一個框中開始輸入值后,將出現第二個值,等等,以便根據需要輸入盡可能多的參數值。

這些值可用於陣列中的腳本,`sys.argv`就像您剛剛添加的代碼一樣。

輸入您的資源群組名稱做為第一個參數值，並輸入要啟動之虛擬機器的名稱做為第二個參數值。

![輸入參數值](media/automation-first-runbook-textual-python/runbook-python-params.png)

按一下 [確定]**** 啟動 Runbook。 隨即執行 Runbook，並開始您所指定的虛擬機器。

## <a name="error-handling-in-python"></a>Python 的錯誤處理

您還可以使用以下約定從 Python Runbook 檢索各種流,包括警告、錯誤和 DEBUG 流。

```python
print("Hello World output") 
print("ERROR: - Hello world error")
print("WARNING: - Hello world warning")
print("DEBUG: - Hello world debug")
print("VERBOSE: - Hello world verbose")
```

下面的示例顯示了塊中使用的此`try...except`約定。

```python
try:
    raise Exception('one', 'two')
except Exception as detail:
    print 'ERROR: Handling run-time error:', detail
```

> [!NOTE]
> Azure 自動化不`sys.stderr`支援 。

## <a name="next-steps"></a>後續步驟

- 要開始使用 PowerShell 執行簿,請參閱[我的第一個 PowerShell 執行簿](automation-first-runbook-textual-powershell.md)。
- 要開始使用圖形 Runbook,請參考[我的第一個圖形執行簿](automation-first-runbook-graphical.md)。
- 要開始使用 PowerShell 工作流執行簿,請參閱[我的第一個 PowerShell 工作流執行簿](automation-first-runbook-textual.md)。
- 要瞭解有關 Runbook 類型及其優點和限制的更多資訊,請參閱[Azure 自動化 Runbook 類型](automation-runbook-types.md)。
- 若要深入了解 Azure with Python 的開發，請參閱 [Azure with Python 開發人員](/azure/python/)。
- 若要檢視範例 Python 2 Runbook，請參閱 [Azure 自動化 GitHub](https://github.com/azureautomation/runbooks/tree/master/Utility/Python)。
