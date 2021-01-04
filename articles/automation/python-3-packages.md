---
title: 在 Azure 自動化中管理 Python 3 套件
description: 本文說明如何在 Azure 自動化中管理 (預覽版) 的 Python 3 套件。
services: automation
ms.subservice: process-automation
ms.date: 12/22/2020
ms.topic: conceptual
ms.openlocfilehash: 3f39f49ff47b935da7ffc777ee45bd219f5740b5
ms.sourcegitcommit: f7084d3d80c4bc8e69b9eb05dfd30e8e195994d8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/22/2020
ms.locfileid: "97734296"
---
# <a name="manage-python-3-packages-preview-in-azure-automation"></a>在 Azure 自動化中管理 (預覽版) 的 Python 3 套件

Azure 自動化可讓您在 Azure 和 Linux 混合式 Runbook 背景工作角色上執行 Python 3 runbook (preview) 。 若要協助簡化 Runbook，您可以使用 Python 封裝來匯入需要的模組。 本文說明如何在 Azure 自動化中管理和使用 Python 3 套件 (預覽) 。

## <a name="import-packages"></a>匯入套件

在您的自動化帳戶中，選取 [**共用資源**] 底下的 [ **Python 套件**]。 選取 [ **+ 新增 Python 套件**]。

:::image type="content" source="media/python-3-packages/add-python-3-package.png" alt-text="[Python 3 套件] 頁面的螢幕擷取畫面會在左側功能表中顯示 Python 3 套件，並將 Python 2 套件醒目提示。":::

在 [ **新增 Python 套件** ] 頁面上，選取 [python 3] 作為 [ **版本**]，然後選取要上傳的本機套件。 此套件可以是 **.whl** 或 **.tar.gz** 檔案。 選取套件之後，請選取 **[確定]** 以上傳。

:::image type="content" source="media/python-3-packages/upload-package.png" alt-text="螢幕擷取畫面顯示已選取上傳的 gz 檔案的 [新增 Python 3 套件] 頁面。":::

匯入套件之後，它會列在您自動化帳戶的 [Python 套件] 頁面上，[ **python 3 套件 (預覽])** 索引標籤底下。如果您需要移除封裝，請選取封裝，然後按一下 [ **刪除**]。

:::image type="content" source="media/python-3-packages/python-3-packages-list.png" alt-text="螢幕擷取畫面會顯示匯入套件之後的 [Python 3 套件] 頁面。":::

## <a name="import-packages-with-dependencies"></a>匯入具有相依性的套件

Azure 自動化不會在匯入過程中解析 Python 套件的相依性。 不過，有一種方式可以匯入封裝及其所有相依性。

### <a name="manually-download"></a>手動下載

在已安裝 [Python 3.8](https://www.python.org/downloads/release/python-380/) 和 [pip](https://pip.pypa.io/en/stable/) 的 Windows 64 位電腦上，執行下列命令以下載套件及其所有相依性：

```cmd
C:\Python38\Scripts\pip3.8.exe download -d <output dir> <package name>
```

下載套件之後，您就可以將它們匯入您的自動化帳戶中。

## <a name="use-a-package-in-a-runbook"></a>在 Runbook 中使用封裝

匯入套件之後，您就可以在 runbook 中使用它。 新增下列程式碼，以列出 Azure 訂用帳戶中的所有資源群組。

```python
import os  
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

# Intialize the resource management client with the RunAs credential and subscription  
resource_client = azure.mgmt.resource.ResourceManagementClient(  
    azure_credential,  
    str(runas_connection["SubscriptionId"]))  

# Get list of resource groups and print them out  
groups = resource_client.resource_groups.list()  
for group in groups:  
    print(group.name) 
```

## <a name="next-steps"></a>後續步驟

若要準備 Python Runbook，請參閱[建立 Python Runbook](learn/automation-tutorial-runbook-textual-python-3.md)。
