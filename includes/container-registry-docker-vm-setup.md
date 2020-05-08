---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/07/2020
ms.author: danlep
ms.openlocfilehash: be170144fddeb1a69592f1714ec745d559665832
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982412"
---
## <a name="create-a-docker-enabled-virtual-machine"></a>建立具備 Docker 功能的虛擬機器

基於測試目的，請使用已啟用 Docker 的 Ubuntu VM 來存取 Azure container registry。 若要對登錄使用 Azure Active Directory 驗證，請同時在 VM 上安裝[Azure CLI][azure-cli] 。 如果您已經有 Azure 虛擬機器，請略過此建立步驟。

您可以將相同的資源群組用於虛擬機器和容器登錄。 此安裝程式會在結束時簡化清理，但不需要。 如果您選擇為虛擬機器和虛擬網路建立個別的資源群組，請執行[az group create][az-group-create]。 下列範例假設您已為資源組名和登錄位置設定環境變數：

```azurecli
az group create --name $RESOURCE_GROUP --location $REGISTRY_LOCATION
```

現在，使用[az vm create][az-vm-create]部署預設的 Ubuntu Azure 虛擬機器。 下列範例會建立名為*myDockerVM*的 VM。

```azurecli
VM_NAME=myDockerVM

az vm create \
  --resource-group $RESOURCE_GROUP \
  --name $VM_NAME \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

系統需要花幾分鐘的時間來建立 VM。 命令完成之後，請記下 Azure CLI 所顯示的 `publicIpAddress`。 請使用此位址與 VM 建立 SSH 連線。

### <a name="install-docker-on-the-vm"></a>在 VM 上安裝 Docker

在 VM 執行之後，與 VM 建立 SSH 連線。 以您 VM 的公用 IP 位址取代 *publicIpAddress*。

```bash
ssh azureuser@publicIpAddress
```

執行下列命令以在 Ubuntu VM 上安裝 Docker：

```bash
sudo apt-get update
sudo apt install docker.io -y
```

安裝之後，執行下列命令確認 Docker 在 VM 上正常執行：

```bash
sudo docker run -it hello-world
```

輸出：

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>安裝 Azure CLI

請遵循[使用 apt 安裝 Azure CLI](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) 中的步驟在您的 Ubuntu 虛擬機器上安裝 Azure CLI。 例如：

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

結束 SSH 連線。

[azure-cli]: /cli/azure/install-azure-cli
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-group-create]: /cli/azure/group