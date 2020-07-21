---
title: 如何使用採用 Azure Cosmos DB 的 Azure Kubernetes
description: 了解如何在使用 Azure Cosmos DB 的 Azure 上執行 Kubernetes 叢集的啟動程序 (預覽)
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/06/2019
ms.author: sngun
ms.openlocfilehash: 31c3c02f2b32594e5b20450d0bb519f4cdf82807
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86497755"
---
# <a name="how-to-use-azure-kubernetes-with-azure-cosmos-db-preview"></a>如何使用採用 Azure Cosmos DB 的 Azure Kubernetes (預覽)

Azure Cosmos DB 中的 etcd API 可讓您使用 Azure Cosmos DB 作為 Azure Kubernetes 的後端存放區。 Azure Cosmos DB 會實作 etcd 有線通訊協定，讓主要節點的 API 伺服器使用 Azure Cosmos DB 就如同存取本機安裝的 etcd 一樣。 Azure Cosmos DB 中的 etcd API 目前為預覽狀態。 使用 Azure Cosmos etcd API 作為 Kubernetes 的備份存放區有下列優點： 

* 不需要手動設定及管理 etcd。
* 由 Cosmos 保證的 etcd 高可用性 (單一區域中有 99.99%，多區域中有 99.999%)。
* etcd 的彈性延展性。
* 預設保護且適合企業使用。
* 領先業界的全方位 SLA。

若要深入了解 Azure Cosmos DB 中的 etcd API，請參閱[概觀](etcd-api-introduction.md)一文。 本文會示範如何使用 [Azure Kubernetes 引擎](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md) (aks-engine) 在採用 [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/) 的 Azure 上執行 Kubernetes 叢集的啟動程序，而不是使用本機安裝和設定的 etcd。 

## <a name="prerequisites"></a>先決條件

1. 安裝最新版的 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。 您可以下載專屬於您作業系統的 Azure CLI，並加以安裝。

1. 安裝[最新版的](https://github.com/Azure/aks-engine/releases) Azure Kubernetes 引擎。 您可以從 [Azure Kubernetes 引擎](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md#install-aks-engine)頁面取得不同作業系統的安裝指示。 您只需要已連結檔之**安裝 AKS 引擎**一節中的步驟。下載之後，請將 zip 檔案解壓縮。

   Azure Kubernetes 引擎 (**aks-engine**) 會針對 Azure 上的 Kubernetes 叢集產生 Azure Resource Manager 範本。 aks-engine 的輸入是叢集定義檔，其中描述所需的叢集，包括協調器、功能和代理程式。 輸入檔案的結構類似於 Azure Kubernetes Service 的公用 API。

1. Azure Cosmos DB 中的 etcd API 目前為預覽狀態。 若要使用預覽版本，請在此註冊：https://aka.ms/cosmosetcdapi-signup。 提交表單之後，您的訂用帳戶將允許使用 Azure Cosmos etcd API。 

## <a name="deploy-the-cluster-with-azure-cosmos-db"></a>使用 Azure Cosmos DB 部署叢集

1. 開啟命令提示字元視窗，並以下列命令登入 Azure：

   ```azurecli-interactive
   az login 
   ```

1. 如果您有多個訂用帳戶，請切換至已允許 Azure Cosmos DB etcd API 的訂用帳戶。 您可以使用下列命令切換至所需的訂用帳戶：

   ```azurecli-interactive
   az account set --subscription "<Name of your subscription>"
   ```
1. 接下來，建立新的資源群組，您將在其中部署 Azure Kubernetes 叢集所需的資源。 請務必將資源群組建立在 "centralus" 區域中。 資源群組並非一定要位於 "centralus" 區域中，但 Azure Cosmos etcd API 目前僅能部署在 "centralus" 區域中。 因此，最好讓 Kubernetes 叢集與 Cosmos etcd 執行個體位在同一個區域：

   ```azurecli-interactive
   az group create --name <Name> --location "centralus"
   ```

1. 接下來，建立 Azure Kubernetes 叢集的服務主體，以便與屬於相同資源群組的資源通訊。 您可以使用 Azure CLI、PowerShell 或 Azure 入口網站建立服務主體，在此範例中，您將使用 CLI 來建立。

   ```azurecli-interactive
   az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<Your_Azure_subscription_ID>/resourceGroups/<Your_resource_group_name>"
   ```
   此命令會輸出服務主體的詳細資料，例如：
   
   ```cmd
   Retrying role assignment creation: 1/36
   {
     "appId": "8415a4e9-4f83-46ca-a704-107457b2e3ab",
     "displayName": "azure-cli-2019-04-19-19-01-46",
     "name": "http://azure-cli-2019-04-19-19-01-46",
     "password": "102aecd3-5e37-4f3d-8738-2ac348c2e6a7",
     "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
   }
   ```
   
   請記下 **appId** 和 **password** 欄位，因為您會在下一個步驟中用到這些參數。 

1. 從命令提示字元中，瀏覽至 Azure Kubernetes 引擎可執行檔所在的資料夾。 例如，在命令提示字元中，您可以瀏覽至如下所示的資料夾：

   ```cmd
   cd "\aks-engine-v0.36.3-windows-amd64\aks-engine-v0.36.3-windows-amd64"
   ```

1. 開啟您選擇的文字編輯器，並定義用來以 Azure Cosmos DB etcd API 部署 Azure Kubernetes 叢集的 Resource Manager 範本。 將下列 JSON 定義複製到文字編輯器，並將檔案儲存為 `apiModel.json`：

   ```json

   {
    "apiVersion": "vlabs",
    "properties": {
        "orchestratorProfile": {
            "orchestratorType": "Kubernetes",
            "kubernetesConfig": {
                "useManagedIdentity": false
            }
        },
        "masterProfile": {
            "count": 1,
            "dnsPrefix": "",
            "vmSize": "Standard_D2_v3",
            "cosmosEtcd": true
        },
        "agentPoolProfiles": [
            {
                "name": "agent",
                "count": 1,
                "vmSize": "Standard_D2_v3",
                "availabilityProfile": "AvailabilitySet"
            }
        ],
        "linuxProfile": {
            "adminUsername": "azureuser",
            "ssh": {
                "publicKeys": [
                    {
                        "keyData": ""
                    }
                ]
            }
        }
    }
   }
   ```

   在 JSON/叢集定義檔中，要注意的關鍵參數是 **"cosmosEtcd": true**。 此參數位在 "masterProfile" 屬性中，並指出要使用 Azure Cosmos etcd API，而不是一般的 etcd 的部署。 

1. 使用下列命令部署使用 Azure Cosmos DB 的 Azure Kubernetes 叢集：

   ```cmd
   aks-engine deploy \
     --subscription-id <Your_Azure_subscription_ID> \
     --client-id <Service_ principal_appId> \
     --client-secret <Service_ principal_password> \
     --dns-prefix <Region_unique_dns_name > \
     --location centralus \
     --resource-group <Resource_Group_Name> \
     --api-model <Fully_qualified_path_to_the_template_file>  \
     --force-overwrite
   ```

   Azure Kubernetes 引擎會取用概述 Azure Kubernetes 所需圖形、大小和組態的叢集定義。 有幾項功能可透過叢集定義啟用。 在此範例中，您將使用下列參數：

   * **訂用帳戶識別碼：** 已啟用 Azure Cosmos DB etcd API 的 Azure 訂用帳戶識別碼。
   * **用戶端識別碼：** 服務主體的 appId。 `appId` 已在步驟 4 中作為輸出傳回。
   * **用戶端密碼：** 服務主體的密碼或隨機產生的密碼。 此值已在步驟 4 中作為 'password' 參數的輸出傳回。 
   * **dnsPrefix：** 區域唯一的 DNS 名稱。 此值會形成主機名稱的一部分 (暫時的範例值是 - myprod1)。
   * **位置：** 應部署叢集的位置，目前僅支援 "centralus"。

   > [!Note]
   > Azure Cosmos etcd API 目前只能在 "centralus" 區域中部署。 
 
   * **api 模型：** 範本檔案的完整路徑。
   * **強制覆寫：** 此選項可用來自動覆寫輸出目錄中的現有檔案。
 
   以下命令會顯示部署範例：

   ```cmd
   aks-engine deploy \
     --subscription-id 1234fc61-1234-1234-1234-be1234d12c1b \
     --client-id 1234a4e9-4f83-46ca-a704-107457b2e3ab \
     --client-secret 123aecd3-5e37-4f3d-8738-2ac348c2e6a7 \
     --dns-prefix aks-sg-test \
     --location centralus \
     --api-model "C:\Users\demouser\Downloads\apiModel.json"  \
     --force-overwrite
   ```

## <a name="verify-the-deployment"></a>驗證部署

範本部署需要數分鐘才能完成。 成功完成部署之後，您會在命令提示字元中看到下列輸出：

```cmd
WARN[0006] apimodel: missing masterProfile.dnsPrefix will use "aks-sg-test"
WARN[0006] --resource-group was not specified. Using the DNS prefix from the apimodel as the resource group name: aks-sg-test
INFO[0025] Starting ARM Deployment (aks-sg-test-546247491). This will take some time...
INFO[0587] Finished ARM Deployment (aks-sg-test-546247491). Succeeded
```

資源群組現在包含的資源如下：虛擬機器、Azure Cosmos 帳戶 (etcd API)、虛擬網路、可用性設定組和 Kubernetes 叢集所需的其他資源。 

Azure Cosmos 帳戶的名稱將會符合加上 k8s 的指定 DNS 首碼。 您的 Azure Cosmos 帳戶會自動以名為 **EtcdDB** 的資料庫和名為 **EtcdData** 的容器進行佈建。 該容器會儲存所有與 etcd 相關的資料。 容器已佈建特定數目的要求單位，您可以根據工作負載來[調整 (增加/減少) 輸送量](scaling-throughput.md)。 

## <a name="next-steps"></a>後續步驟

* 了解如何[使用 Azure Cosmos 資料庫、容器和項目](databases-containers-items.md)
* 了解如何[最佳化已佈建的輸送量成本](optimize-cost-throughput.md)

