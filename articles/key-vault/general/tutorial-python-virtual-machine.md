---
title: 教學課程 - 在 Python 中搭配使用 Azure Key Vault 與虛擬機器 | Microsoft Docs
description: 在本教學課程中，您會在 Python 應用程式中設定虛擬機器，以從金鑰保存庫讀取祕密。
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 07/20/2020
ms.author: mbaldwin
ms.custom: mvc, devx-track-python
ms.openlocfilehash: 8980505ac34e32a29403060a7cf3cfaec077d8af
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91336695"
---
# <a name="tutorial-use-azure-key-vault-with-a-virtual-machine-in-python"></a>教學課程：在 Python 中搭配使用 Azure Key Vault 與虛擬機器

Azure Key Vault 可協助您保護金鑰、祕密和憑證，例如 API 金鑰和資料庫連接字串。

在本教學課程中，您會設定 Python 應用程式，以使用 Azure 資源的受控識別從 Azure Key Vault 讀取資訊。 您會了解如何：

> [!div class="checklist"]
> * 建立金鑰保存庫
> * 將秘密儲存在 Key Vault 中
> * 建立 Azure Linux 虛擬機器
> * 啟用虛擬機器的[受控識別](../../active-directory/managed-identities-azure-resources/overview.md)
> * 授與主控台應用程式從 Key Vault 讀取資料所需的權限
> * 從 Key Vault 擷取祕密

在開始之前，請先閱讀 [Key Vault 基本概念](basic-concepts.md)。 

如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必要條件

若為 Windows、Mac 和 Linux：
  * [Git](https://git-scm.com/downloads)
  * 此教學課程需要您在本機執行 Azure CLI。 您必須安裝 Azure CLI 2.0.4 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級 CLI，請參閱[安裝 Azure CLI 2.0](/cli/azure/install-azure-cli)。

## <a name="log-in-to-azure"></a>登入 Azure

若要使用 Azure CLI 登入 Azure，請輸入：

```azurecli
az login
```

## <a name="create-a-resource-group-and-key-vault"></a>建立資源群組和金鑰保存庫

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

## <a name="populate-your-key-vault-with-a-secret"></a>將祕密填入金鑰保存庫

[!INCLUDE [Create a secret](../../../includes/key-vault-create-secret.md)]

## <a name="create-a-virtual-machine"></a>建立虛擬機器

使用下列其中一個方法來建立名為 **myVM** 的 VM：

| Linux | Windows |
|--|--|
| [Azure CLI](../../virtual-machines/linux/quick-create-cli.md) | [Azure CLI](../../virtual-machines/windows/quick-create-cli.md) |
| [PowerShell](../../virtual-machines/linux/quick-create-powershell.md) | [PowerShell](../../virtual-machines/windows/quick-create-powershell.md) |
| [Azure 入口網站](../../virtual-machines/linux/quick-create-portal.md) | [Azure 入口網站](../../virtual-machines/windows/quick-create-portal.md) |

若要使用 Azure CLI 建立 Linux VM，請使用 [az vm create](/cli/azure/vm) 命令。  下列範例會新增名為 azureuser 的使用者帳戶。 `--generate-ssh-keys` 參數用來自動產生 SSH 金鑰，並將它放在預設金鑰位置 (~/.ssh)。 

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

請注意輸出中的 `publicIpAddress` 值。

## <a name="assign-an-identity-to-the-vm"></a>將身分識別指派給 VM

使用 Azure CLI [az vm identity assign](/cli/azure/vm/identity?view=azure-cli-latest#az-vm-identity-assign) 命令，為虛擬機器建立系統指派的身分識別：

```azurecli
az vm identity assign --name "myVM" --resource-group "myResourceGroup"
```

請注意下列程式碼中所顯示的系統指派身分識別。 上述命令的輸出會是： 

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="assign-permissions-to-the-vm-identity"></a>對 VM 身分識別指派權限

現在，您可以執行下列命令來對金鑰保存庫指派先前所建立的身分識別權限：

```azurecli
az keyvault set-policy --name "<your-unique-keyvault-name>" --object-id "<systemAssignedIdentity>" --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>登入 VM

若要登入虛擬機器，請遵循[連線和登入執行 Linux 的 Azure 虛擬機器](../../virtual-machines/linux/login-using-aad.md)或[連線和登入執行 Windows 的 Azure 虛擬機器](../../virtual-machines/windows/connect-logon.md)中的指示。


若要登入 Linux VM，您可以使用 ssh 命令搭配[建立虛擬機器](#create-a-virtual-machine)步驟中提供的「<publicIpAddress>」：

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-python-libraries-on-the-vm"></a>在 VM 上安裝 Python 程式庫

在虛擬機器上，安裝會在 Python 指令碼中使用的兩個 Python 程式庫：`azure-keyvault-secrets` 和 `azure.identity`。  

例如，在 Linux VM 上，您可以使用 `pip3` 安裝：

```bash
pip3 install azure-keyvault-secrets

pip3 install azure.identity
```

## <a name="create-and-edit-the-sample-python-script"></a>建立和編輯 Python 指令碼範例

在虛擬機器中，建立名為 **sample.py** 的 Python 檔案。 編輯檔案以包含下列範例程式碼，並以您的金鑰保存庫名稱取代 "<your-unique-keyvault-name>"：

```python
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential

keyVaultName = "<your-unique-keyvault-name>"
KVUri = f"https://{keyVaultName}.vault.azure.net"
secretName = "mySecret"

credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)
retrieved_secret = client.get_secret(secretName)

print(f"The value of secret '{secretName}' in '{keyVaultName}' is: '{retrieved_secret.value}'")
```

## <a name="run-the-sample-python-app"></a>執行 Python 應用程式範例

最後，執行 **sample.py**。 如果一切順利，系統應該會傳回您的祕密值：

```bash
python3 sample.py

The value of secret 'mySecret' in '<your-unique-keyvault-name>' is: 'Success!'
```

## <a name="clean-up-resources"></a>清除資源

不再需要時，請刪除虛擬機器和金鑰保存庫。  您只要刪除其所屬的資源群組，就可以快速完成這項操作：

```azurecli
az group delete -g myResourceGroup
```

## <a name="next-steps"></a>後續步驟

[Azure Key Vault REST API](https://docs.microsoft.com/rest/api/keyvault/)
