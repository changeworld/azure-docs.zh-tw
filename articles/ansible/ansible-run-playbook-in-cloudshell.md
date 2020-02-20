---
title: 快速入門 - 在 Azure Cloud Shell 中透過 Bash 執行 Ansible 劇本
description: 在本教學課程中，您將了解如何在 Azure Cloud Shell 中使用 Bash 完成各種 Ansible 工作
keywords: ansible、azure、devops、bash、cloudshell、腳本、bash
ms.topic: quickstart
ms.date: 04/30/2019
ms.openlocfilehash: 0eb89bcacae1629bdb1f6dcda8f9a25efdb6eedf
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77193645"
---
# <a name="quickstart-run-ansible-playbooks-via-bash-in-azure-cloud-shell"></a>快速入門：在 Azure Cloud Shell 中透過 Bash 執行 Ansible 劇本

Azure Cloud Shell 是可經由瀏覽器存取的互動式殼層，應用在 Azure 資源管理上。 Cloud Shell 可讓您使用 Bash 或 Powershell 命令列。 在本文中，您可以在 Azure Cloud Shell 中使用 Bash 執行 Ansible 劇本。

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- **設定 Azure Cloud Shell** - 如果您還不熟悉 Azure Cloud Shell，請參閱 [Azure Cloud Shell 中 Bash 的快速入門](https://docs.microsoft.com/azure/cloud-shell/quickstart)。
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>自動認證設定

登入 Cloud Shell 時，Ansible 會向 Azure 進行驗證以管理結礎結構，而不必進行任何額外設定。 

使用多個訂用帳戶時，可藉由匯出 `AZURE_SUBSCRIPTION_ID` 環境變數來指定 Ansible 使用的訂用帳戶。 

若要列出所有 Azure 訂用帳戶，請執行下列命令：

```azurecli-interactive
az account list
```

使用您的 Azure 訂用帳戶識別碼設定 `AZURE_SUBSCRIPTION_ID`，如下所示：

```azurecli-interactive
export AZURE_SUBSCRIPTION_ID=<your-subscription-id>
```

## <a name="verify-the-configuration"></a>驗證組態
若要確認設定成功，請使用 Ansible 建立 Azure 資源群組。

[!INCLUDE [create-resource-group-with-ansible.md](../../includes/ansible-snippet-create-resource-group.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"] 
> [快速入門：使用 Ansible 在 Azure 中設定虛擬機器](./ansible-create-vm.md)