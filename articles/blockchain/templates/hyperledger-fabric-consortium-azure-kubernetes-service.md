---
title: Azure 庫伯奈斯服務 （AKS） 上的超分類帳結構聯合體
description: 如何在 Azure 庫伯奈斯服務上部署和配置超級分類帳結構聯合網路
ms.date: 01/08/2020
ms.topic: article
ms.reviewer: v-umha
ms.openlocfilehash: 2312c002e5c2e0b813f8acbdc3e3bff597f204d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476435"
---
# <a name="hyperledger-fabric-consortium-on-azure-kubernetes-service-aks"></a>Azure 庫伯奈斯服務 （AKS） 上的超分類帳結構聯合體

您可以使用 Azure 庫伯奈斯服務 （AKS） 範本上的超級分類帳結構 （HLF） 在 Azure 上部署和配置超級分類帳結構聯合網路。

閱讀本文之後，您將能夠：

- 獲得 Hyperledger Fabric 的工作知識以及構成 Hyperledger Fabric 區塊鏈網路構建塊的各種元件。
- 瞭解如何在 Azure Kubernetes 服務上部署和配置用於生產方案的超分類帳結構聯合體。

## <a name="hyperledger-fabric-consortium-architecture"></a>超級分類帳結構聯盟架構

要在 Azure 上構建 Hyperledger Fabric 網路，需要使用對等節點部署排序服務和組織。 作為範本部署的一部分創建的不同基本元件包括：

- **訂購者節點**：負責分類帳中的事務排序的節點。 與其他節點一起，有序節點構成了 Hyperledger Fabric 網路的訂購服務。

- **對等節點**：主要承載分類帳和智慧合約的節點，這些網路的基本元素。

- **結構 CA**： 結構 CA 是超級分類帳結構的憑證授權單位 （CA）。 Fabric CA 允許您初始化和啟動承載憑證授權單位的伺服器進程。 它允許您管理標識和證書。 預設情況下，作為範本的一部分部署的每個 AKS 群集都將具有結構 CA pod。

- **CouchDB 或 LevelDB**： 對等節點的世界狀態資料庫可以存儲在 LevelDB 或 CouchDB 中。 LevelDB 是嵌入在對等節點中的預設狀態資料庫，將鏈碼資料存儲為簡單的鍵值對，並且僅支援鍵、鍵範圍和複合金鑰查詢。 CouchDB 是一個可選的備用狀態資料庫，當鏈碼資料值建模為 JSON 時，支援豐富的查詢。

部署上的範本會佔用訂閱中的各種 Azure 資源。 部署的不同 Azure 資源包括：

- **AKS 群集**：Azure Kubernetes 群集，根據客戶提供的輸入參數進行配置。 AKS 群集具有配置為運行 Hyperledger 結構網路元件的各種窗格。 創建的不同窗格包括：

  - **結構工具**：結構工具負責配置超分類元結構元件。
  - **訂購器/對等窗格**：HLF 網路的節點。
  - **代理**：一個 NGNIX 代理窗格，用戶端應用程式可以通過該窗格與 AKS 群集介面。
  - **結構 CA**： 運行結構 CA 的窗格。
- **PostgreSQL**： 部署 PostgreSQL 的實例以維護結構 CA 標識。

- **Azure 金鑰保存庫**：將部署金鑰保存庫實例以保存結構 CA 憑據和客戶提供的根憑證，這些證書用於範本部署重試，這是處理範本的機制。
- **Azure 託管磁片**：Azure 託管磁片用於分類帳和對等節點世界狀態資料庫的持久存儲。
- **公共 IP**：為與群集介面而部署的 AKS 群集的公共 IP 終結點。

## <a name="hyperledger-fabric-blockchain-network-setup"></a>超級帳本織物區塊鏈網路設置

若要開始，您需要可支援部署數個虛擬機器和標準儲存體帳戶的 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，則可[建立免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

使用以下步驟設置超帳本結構區塊鏈網路：

- [部署訂購者/對等組織](#deploy-the-ordererpeer-organization)
- [建立聯合體](#build-the-consortium)
- [運行本機 HLF 操作](#run-native-hlf-operations)

## <a name="deploy-the-ordererpeer-organization"></a>部署訂購者/對等組織

要開始使用 HLF 網路元件部署，導航到[Azure 門戶](https://portal.azure.com)。 選擇**創建一個資源>區塊鏈**>搜索**超級分類帳結構在Azure庫伯奈斯服務**。

1. 選擇 **"創建"** 以啟動範本部署。 Azure**庫伯奈斯服務上的"創建超級分類帳結構**"顯示。

    ![Azure 庫伯奈斯服務範本上的超分類帳結構](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-fabric-aks.png)

2. 在 **"基礎知識"** 頁中輸入專案詳細資訊。

    ![Azure 庫伯奈斯服務範本上的超分類帳結構](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-basics.png)

3. 輸入下列詳細資料：
    - **訂閱**：選擇要部署 HLF 網路元件的訂閱名稱。
    - **資源組**：創建新資源組或選擇現有的空資源組，資源組將保存所有資源作為範本的一部分部署。
    - **區域**：選擇要為 HLF 元件部署 Azure 庫伯奈斯群集的 Azure 區域。 該範本在 AKS 可用的所有區域都可用"確保選擇訂閱未達到虛擬機器 （VM） 配額限制的區域。
    - **資源首碼**：用於命名已部署的資源的首碼。 資源首碼的長度必須少於六個字元，字元的組合必須同時包含數位和字母。
4. 選擇 **"結構設置"** 選項卡以定義將部署的 HLF 網路元件。

    ![Azure 庫伯奈斯服務範本上的超分類帳結構](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-settings.png)

5. 輸入下列詳細資料：
    - **組織名稱**：結構組織的名稱，這是各種資料平面操作所必需的。 組織名稱需要每個部署是唯一的。 
    - **交換矩陣網路元件**：根據要設置的區塊鏈網路元件選擇訂購服務或對等節點。
    - **節點數**- 以下是兩種類型的節點：
        - 訂購服務 - 選擇節點數，為網路提供容錯。 只有 3、5 和 7 是受支援的訂購器節點計數。
        - 對等節點 - 您可以根據您的要求選擇 1-10 個節點。
    - **對等節點世界狀態資料庫**：在 LevelDB 和庫奇布DB 之間進行選擇。 當使用者在 Fabric 網路元件下拉下拉下下拉清單中選擇對等節點時，將顯示此欄位。
    - **結構使用者名**：輸入用於結構 CA 身份驗證的使用者名。
    - **結構 CA 密碼**：輸入結構 CA 身份驗證的密碼。
    - **確認密碼**：確認結構 CA 密碼。
    - **證書**：如果要使用自己的根憑證初始化 Fabric CA，請選擇 Fabric CA 的上載根憑證選項，否則預設情況下 Fabric CA 將創建自簽章憑證。
    - **根憑證**：上載需要初始化結構 CA 的根憑證（公開金鑰）。 支援 .pem 格式的證書，證書應在 UTC 時區中有效。
    - **根憑證私密金鑰**：上載根憑證的私密金鑰。 如果您有 .pem 證書（包含公開金鑰和私密金鑰的組合），請在此處上載。


6. 選擇**AKS 群集設置**選項卡以定義 Azure Kubernetes 群集配置，該配置是將設置 Fabric 網路元件的基礎基礎結構。

    ![Azure 庫伯奈斯服務範本上的超分類帳結構](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-aks-cluster-settings-1.png)

7. 輸入下列詳細資料：
    - **庫伯內斯群集名稱**：所創建的 AKS 群集的名稱。 此欄位根據提供的資源首碼進行預填充，如有必要，可以進行更改。
    - **庫伯內特斯版本**：將在群集上部署的庫伯內特的版本。 根據"**基本"** 選項卡中選擇的區域，支援的版本可能會更改。
    - **DNS 首碼**：AKS 群集的網域名稱系統 （DNS） 名稱首碼。 創建群集後管理容器時，將使用 DNS 連接到 Kubernetes API。
    - **節點大小**：Kubernetes 節點的大小，您可以從 Azure 上可用的 VM 庫存單位 （SKU） 清單中選擇。 為了獲得最佳性能，我們建議使用標準 DS3 v2。
    - **節點計數**：要在群集中部署的 Kubernetes 節點數的計數。 我們建議保持此節點計數至少等於或超過結構設置中指定的 HLF 節點數。
    - **服務主體用戶端 ID**：輸入現有服務主體的用戶端 ID 或創建新的 AKS 身份驗證所必需的。 請參閱，[創建服務主體](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-3.2.0#create-a-service-principal)的步驟。
    - **服務主體用戶端金鑰**：輸入服務主體用戶端 ID 中提供的服務主體的用戶端金鑰。
    - **確認用戶端機密**：確認服務主體用戶端金鑰中提供的用戶端金鑰。
    - **啟用容器監視**：選擇啟用 AKS 監視，使 AKS 日誌能夠推送到指定的日誌分析工作區。
    - **日誌分析工作區**：日誌分析工作區將填充啟用監視時創建的預設工作區。

8. 提供上述所有詳細資訊後，選擇 **"查看並創建**"選項卡。審核和創建將觸發對所提供的值的驗證。
9. 驗證通過後，您可以選擇**創建**。
部署通常需要 10-12 分鐘，可能因指定的 AKS 節點的大小和數量而異。
10. 成功部署後，將通過右上角的 Azure 通知通知您。
11. 選擇 **"轉到資源組**"以檢查作為範本部署的一部分創建的所有資源。 所有資源名稱都將從 **"基本"** 設置中提供的首碼開始。

## <a name="build-the-consortium"></a>建立聯合體

要構建區塊鏈聯盟在部署訂購服務和對等節點後，您需要按循序執行以下步驟。 **構建網路**腳本（byn.sh），它可以説明您設置聯合體、創建頻道和安裝鏈碼。

> [!NOTE]
> 提供構建網路（byn）腳本嚴格用於演示/演示方案。 對於生產級設置，我們建議使用本機 HLF API。

運行 byn 腳本的所有命令都可以通過 Azure Bash 命令列介面 （CLI） 執行。 您可以通過 ![Azure 庫伯奈斯服務範本上的超分類帳結構](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) 選項位於 Azure 門戶的右上角。 在命令提示符上，鍵入 bash 並輸入以切換到 bash CLI。

有關詳細資訊，請參閱[Azure 外殼](https://docs.microsoft.com/azure/cloud-shell/overview)。

![Azure 庫伯奈斯服務範本上的超分類帳結構](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


下載byn.sh和結構管理員.yaml 檔。

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/consortiumScripts/byn.sh -o byn.sh; chmod 777 byn.sh
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/consortiumScripts/fabric-admin.yaml -o fabric-admin.yaml
```
**在 Azure CLI Bash 外殼上設置以下環境變數**：

設置通道資訊和訂購器組織資訊

```bash
SWITCH_TO_AKS_CLUSTER() { az aks get-credentials --resource-group $1 --name $2 --subscription $3; }
ORDERER_AKS_SUBSCRIPTION=<ordererAKSClusterSubscriptionID>
ORDERER_AKS_RESOURCE_GROUP=<ordererAKSClusterResourceGroup>
ORDERER_AKS_NAME=<ordererAKSClusterName>
ORDERER_DNS_ZONE=$(az aks show --resource-group $ORDERER_AKS_RESOURCE_GROUP --name $ORDERER_AKS_NAME --subscription $ORDERER_AKS_SUBSCRIPTION -o json | jq .addonProfiles.httpApplicationRouting.config.HTTPApplicationRoutingZoneName | tr -d '"')
ORDERER_END_POINT="orderer1.$ORDERER_DNS_ZONE:443"
CHANNEL_NAME=<channelName>
```
設置對等組織資訊

```bash
PEER_AKS_RESOURCE_GROUP=<peerAKSClusterResourceGroup>
PEER_AKS_NAME=<peerAKSClusterName>
PEER_AKS_SUBSCRIPTION=<peerAKSClusterSubscriptionID>
#Peer organization name is case-sensitive. Specify exactly the same name, which was provided while creating the Peer AKS Cluster.
PEER_ORG_NAME=<peerOrganizationName>
```

創建一個 Azure 檔共用，以便在對等方和訂購器組織之間共用各種公共證書。

```bash
STORAGE_SUBSCRIPTION=<subscriptionId>
STORAGE_RESOURCE_GROUP=<azureFileShareResourceGroup>
STORAGE_ACCOUNT=<azureStorageAccountName>
STORAGE_LOCATION=<azureStorageAccountLocation>
STORAGE_FILE_SHARE=<azureFileShareName>

az account set --subscription $STORAGE_SUBSCRIPTION
az group create -l $STORAGE_LOCATION -n $STORAGE_RESOURCE_GROUP
az storage account create -n $STORAGE_ACCOUNT -g  $STORAGE_RESOURCE_GROUP -l $STORAGE_LOCATION --sku Standard_LRS
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
az storage share create  --account-name $STORAGE_ACCOUNT  --account-key $STORAGE_KEY  --name $STORAGE_FILE_SHARE
SAS_TOKEN=$(az storage account generate-sas --account-key $STORAGE_KEY --account-name $STORAGE_ACCOUNT --expiry `date -u -d "1 day" '+%Y-%m-%dT%H:%MZ'` --https-only --permissions lruwd --resource-types sco --services f | tr -d '"')
AZURE_FILE_CONNECTION_STRING="https://$STORAGE_ACCOUNT.file.core.windows.net/$STORAGE_FILE_SHARE?$SAS_TOKEN"
```
**通道管理命令**

轉到訂購器組織 AKS 群集併發出命令以創建新通道

```bash
SWITCH_TO_AKS_CLUSTER $ORDERER_AKS_RESOURCE_GROUP $ORDERER_AKS_NAME $ORDERER_AKS_SUBSCRIPTION
./byn.sh createChannel "$CHANNEL_NAME"
```

**聯盟管理命令**

按給定循序執行以下命令，以便在通道和聯合體中添加對等組織。

1. 轉到對等組織 AKS 群集，並在 Azure 檔存儲上載其成員服務提供 （MSP）。

    ```bash
    SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
    ./byn.sh uploadOrgMSP "$AZURE_FILE_CONNECTION_STRING"
    ```

2. 轉到訂購器組織 AKS 群集，並在通道和聯合體中添加對等組織。

    ```bash
    SWITCH_TO_AKS_CLUSTER $ORDERER_AKS_RESOURCE_GROUP $ORDERER_AKS_NAME $ORDERER_AKS_SUBSCRIPTION
    #add peer in consortium
    ./byn.sh addPeerInConsortium "$PEER_ORG_NAME" "$AZURE_FILE_CONNECTION_STRING"
    #add peer in channel
    ./byn.sh addPeerInChannel "$PEER_ORG_NAME" "$CHANNEL_NAME" "$AZURE_FILE_CONNECTION_STRING"
    ```

3. 返回對等組織和發出命令以在通道中加入對等節點。

    ```bash
    SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
    ./byn.sh joinNodesInChannel "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
    ```

同樣，要在通道中添加更多對等組織，請根據所需的對等組織更新對等 AKS 環境變數，並執行步驟 1 到 3。

**鏈碼管理命令**

執行以下命令以執行與鏈碼相關的操作。 這些命令對演示鏈碼執行所有操作。 此演示鏈碼有兩個變數"a"和"b"。 在鏈碼具現化時，"a"用1000初始化，用2000年初始化"b"。 每次調用鏈碼時，10 個單位從"a"轉移到"b"。 鏈碼上的查詢操作顯示"a"變數的世界狀態。

執行對等組織 AKS 群集上執行的以下命令。

```bash
# switch to peer organization AKS cluster. Skip this command if already connected to the required Peer AKS Cluster
SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
```
**鏈碼操作命令**

```bash
PEER_NODE_NAME="peer<peer#>"
./byn.sh installDemoChaincode "$PEER_NODE_NAME"
./byn.sh instantiateDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
./byn.sh invokeDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
./byn.sh queryDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME"
```

## <a name="run-native-hlf-operations"></a>運行本機 HLF 操作

説明客戶開始在 AKS 上的 HLF 網路上執行 Hyperledger 本機命令。 提供了使用結構 NodeJS SDK 執行 HLF 操作的應用程式範例。 提供這些命令是為了創建新的使用者標識並安裝您自己的鏈碼。

### <a name="before-you-begin"></a>開始之前

按照以下命令進行應用程式的初始設置：

- 下載應用程式檔
- 生成連接設定檔和管理設定檔
- 導入管理員使用者標識

完成初始設置後，可以使用 SDK 實現以下操作：

- 使用者標識生成
- 鏈碼操作

上述命令可以從 Azure 雲外殼執行。

### <a name="download-application-files"></a>下載應用程式檔

運行應用程式的第一個設置是下載資料夾中的所有應用程式檔。

**創建應用資料夾並輸入資料夾**：

```bash
mkdir app
cd app
```
執行以下命令以下載所有必需的檔和包：

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/application/setup.sh | bash
```
此命令需要時間來載入所有包。 成功執行命令後，可以看到目前的目錄中的`node_modules`資料夾。 所有必需的包都載入到資料夾中`node_modules`。

### <a name="generate-connection-profile-and-admin-profile"></a>生成連接設定檔和管理設定檔

在`profile`資料夾內`app`創建目錄

```bash
cd app
mkdir ./profile
```
在 Azure 雲外殼上設置這些環境變數

```bash
# Organization name whose connection profile is to be generated
ORGNAME=<orgname>
# Organization AKS cluster resource group
AKS_RESOURCE_GROUP=<resourceGroup>
```

執行以下命令以生成組織的連接設定檔和管理設定檔

```bash
./getConnector.sh $AKS_RESOURCE_GROUP | sed -e "s/{action}/gateway/g"| xargs curl > ./profile/$ORGNAME-ccp.json
./getConnector.sh $AKS_RESOURCE_GROUP | sed -e "s/{action}/admin/g"| xargs curl > ./profile/$ORGNAME-admin.json
```

它將在設定檔資料夾中創建連接設定檔`profile`和組織管理員，分別具有名稱`<orgname>-ccp.json`和`<orgname>-admin.json`名稱。

同樣，為每個訂單人和對等組織生成連接設定檔和管理設定檔。


### <a name="import-admin-user-identity"></a>導入管理員使用者標識

最後一步是在錢包中導入組織的管理員使用者標識。

```bash
npm run importAdmin -- -o <orgName>

```
上述命令執行 importAdmin.js 以將管理員使用者標識導入錢包。 該腳本從管理員設定檔`<orgname>-admin.json`讀取管理員標識，並將其導入錢包以執行 HLF 操作。

腳本使用檔案系統錢包來存儲標識。 它根據連接設定檔中".wallet"欄位中指定的路徑創建錢包。 預設情況下，".wallet"欄位使用`<orgname>`初始化，這意味著在目前的目錄中創建了名為 的`<orgname>`資料夾以存儲標識。 如果要在其他路徑上創建錢包，請修改連接設定檔中的".wallet"欄位，然後再運行註冊管理員使用者和任何其他 HLF 操作。

同樣，導入每個組織的管理員使用者標識。

有關命令中傳遞的參數的更多詳細資訊，請參閱命令説明。

```bash
npm run importAdmin -- -h

```

### <a name="user-identity-generation"></a>使用者標識生成

按給定循序執行以下命令，以便為 HLF 組織生成新的使用者標識。

> [!NOTE]
> 在開始使用使用者標識生成步驟之前，請確保完成應用程式的初始設置。

在 Azure 雲外殼上設置以下環境變數

```bash
# Organization name for which user identity is to be generated
ORGNAME=<orgname>
# Name of new user identity. Identity will be registered with the Fabric-CA using this name.
USER_IDENTITY=<username>

```

註冊並註冊新使用者

要註冊和註冊新使用者，請執行以下命令，執行註冊User.js。 它將生成的使用者標識保存在錢包中。

```bash
npm run registerUser -- -o $ORGNAME -u $USER_IDENTITY

```

> [!NOTE]
> 管理員使用者標識用於為新使用者發出註冊命令。 因此，在執行此命令之前，必須在錢包中具有管理員使用者標識。 否則，此命令將失敗。

有關命令中傳遞的參數的更多詳細資訊，請參閱命令説明

```bash
npm run registerUser -- -h

```

### <a name="chaincode-operations"></a>鏈碼操作


> [!NOTE]
> 在開始任何鏈碼操作之前，請確保完成應用程式的初始設置。

在 Azure 雲外殼上設置以下鏈碼特定環境變數：

```bash
# peer organization name where chaincode is to be installed
ORGNAME=<orgName>
USER_IDENTITY="admin.$ORGNAME"
CC_NAME=<chaincodeName>
CC_VERSION=<chaincodeVersion>
# Language in which chaincode is written. Supported languages are 'node', 'golang' and 'java'
# Default value is 'golang'
CC_LANG=<chaincodeLanguage>
# CC_PATH contains the path where your chaincode is place. In case of go chaincode, this path is relative to 'GOPATH'.
# For example, if your chaincode is present at path '/opt/gopath/src/chaincode/chaincode.go'.
# Then, set GOPATH to '/opt/gopath' and CC_PATH to 'chaincode'
CC_PATH=<chaincodePath>
# 'GOPATH' environment variable. This needs to be set in case of go chaincode only.
export GOPATH=<goPath>
# Channel on which chaincode is to be instantiated/invoked/queried
CHANNEL=<channelName>

````

可以執行以下鏈碼操作：

- 安裝鏈碼
- 具現化鏈碼
- 調用鏈碼
- 查詢鏈碼

### <a name="install-chaincode"></a>安裝鏈碼

執行以下命令以在對等組織上安裝鏈碼。

```bash
npm run installCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION

```
它將在環境變數中`ORGNAME`設置的組織的所有對等節點上安裝鏈碼。 如果通道中有兩個或多個對等組織，並且希望在所有這些組織上安裝鏈碼，請為每個對等組織分別執行這些命令。

按照以下步驟操作：

- 設置為`ORGNAME``<peerOrg1Name>`併發出`installCC`命令。
- 設置為`ORGNAME``<peerOrg2Name>`併發出`installCC`命令。

  為每個對等組織執行它。

有關命令中傳遞的參數的更多詳細資訊，請參閱命令説明。

```bash
npm run installCC -- -h

```

### <a name="instantiate-chaincode"></a>具現化鏈碼

執行以下命令以具現化對等體上的鏈碼。

```bash
npm run instantiateCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -v $CC_VERSION -l $CC_LANG -c $CHANNEL -f <instantiateFunc> -a <instantiateFuncArgs>

```
傳遞具現化函數名稱和逗號分隔的參數清單和`<instantiateFunc>``<instantiateFuncArgs>`分別。 例如，在[fabrcar 鏈碼](https://github.com/hyperledger/fabric-samples/blob/release/chaincode/fabcar/fabcar.go)中，具現化鏈碼`<instantiateFunc>`設置為`"Init"`和`<instantiateFuncArgs>`空字串`""`。

> [!NOTE]
> 從通道中的任何一個對等組織執行一次命令。
> 成功將事務提交到訂購者後，訂購者將此事務分發給通道中的所有對等組織。 因此，鏈碼在通道中所有對等組織上的所有對等節點上具現化。

有關命令中傳遞的參數的更多詳細資訊，請參閱命令説明

```bash
npm run instantiateCC -- -h

```

### <a name="invoke-chaincode"></a>調用鏈碼

執行以下命令以調用鏈碼函數：

```bash
npm run invokeCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL -f <invokeFunc> -a <invokeFuncArgs>

```
傳遞調用函數名稱和逗號分隔的參數清單，並`<invokeFunction>``<invokeFuncArgs>`分別引用。 繼續 fabcar 鏈碼示例，將 initLedger 函數`<invokeFunction>`設置為`"initLedger"``<invokeFuncArgs>`和`""`。

> [!NOTE]
> 從通道中的任何一個對等組織執行一次命令。
> 成功將事務提交到訂購者後，訂購者將此事務分發給通道中的所有對等組織。 因此，世界狀態在通道中所有對等組織的所有對等節點上更新。

有關命令中傳遞的參數的更多詳細資訊，請參閱命令説明

```bash
npm run invokeCC -- -h

```

### <a name="query-chaincode"></a>查詢鏈碼

執行以下命令以查詢鏈碼：

```bash
npm run queryCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL -f <queryFunction> -a <queryFuncArgs>

```

傳遞查詢函數名稱和逗號分隔的參數清單，並`<queryFunction>``<queryFuncArgs>`分別傳遞。 再次，以`fabcar`鏈碼作為參考，查詢世界上`<queryFunction>`所有的汽車設置`"queryAllCars"`和。 `<queryArgs>` `""`

有關命令中傳遞的參數的更多詳細資訊，請參閱命令説明

```bash
npm run queryCC -- -h

```
