---
title: Azure Kubernetes Service （AKS）上的 Hyperledger 網狀架構聯盟
description: 如何在 Azure Kubernetes Service 上部署和設定 Hyperledger Fabric 聯盟網路
ms.date: 01/08/2020
ms.topic: article
ms.reviewer: v-umha
ms.openlocfilehash: 2312c002e5c2e0b813f8acbdc3e3bff597f204d9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79476435"
---
# <a name="hyperledger-fabric-consortium-on-azure-kubernetes-service-aks"></a>Azure Kubernetes Service （AKS）上的 Hyperledger 網狀架構聯盟

您可以使用 Hyperledger Fabric （HLF） on Azure Kubernetes Service （AKS）範本來部署和設定 Azure 上的 Hyperledger Fabric 聯盟網路。

閱讀本文之後，您將能夠：

- 取得 Hyperledger 網狀架構的實用知識，以及構成 Hyperledger Fabric 區塊鏈網路建立區塊的各種元件。
- 瞭解如何在適用于生產案例的 Azure Kubernetes Service 上部署和設定 Hyperledger 網狀架構聯盟。

## <a name="hyperledger-fabric-consortium-architecture"></a>Hyperledger Fabric 聯盟架構

若要在 Azure 上建立 Hyperledger Fabric 網路，您需要使用對等節點來部署訂購服務和組織。 在範本部署過程中建立的不同基本元件如下：

- **排序者節點**：負責在總帳中進行交易排序的節點。 除了其他節點，已排序的節點會形成 Hyperledger 網狀架構網路的訂購服務。

- **對等節點**：主要裝載總帳和智慧合約的節點，這些是網路的基本元素。

- 網狀**架構 ca**：網狀架構 Ca 是 Hyperledger 網狀架構的憑證授權單位單位（CA）。 網狀架構 CA 可讓您初始化和啟動主控憑證授權單位單位的伺服器進程。 它可讓您管理身分識別和憑證。 每個部署為範本一部分的 AKS 叢集，預設都會有一個網狀架構 CA pod。

- **CouchDB 或 LevelDB**：對等節點的全球狀態資料庫可以儲存在 LevelDB 或 CouchDB 中。 LevelDB 是內嵌在對等節點中的預設狀態資料庫，會將鏈碼資料儲存為簡單的索引鍵/值組，並僅支援索引鍵、索引鍵範圍和複合索引鍵查詢。 CouchDB 是選擇性的替代狀態資料庫，可在鏈碼資料值已模型化為 JSON 時支援豐富的查詢。

部署中的範本會在您的訂用帳戶中啟動各種 Azure 資源。 部署的不同 Azure 資源如下：

- **AKS**叢集：根據客戶提供的輸入參數設定的 Azure Kubernetes 叢集。 AKS 叢集已設定各種 pod 來執行 Hyperledger Fabric 網路元件。 建立的不同 pod 如下：

  - 網狀架構**工具**：網狀架構工具負責設定 Hyperledger 網狀架構元件。
  - **排序者/對等**POD： HLF 網路的節點。
  - **Proxy**：用戶端應用程式可以用來與 AKS 叢集進行介面處理的 NGNIX proxy pod。
  - 網狀**架構 ca**：執行網狀架構 ca 的 pod。
- **于 postgresql**：部署于 postgresql 的實例以維護網狀架構 CA 身分識別。

- **Azure 金鑰保存庫**：部署金鑰保存庫實例，以儲存客戶所提供的網狀架構 CA 認證和根憑證，用於部署範本的重試時，這是為了處理範本的機制。
- **Azure 受控磁片**： azure 受控磁片適用于總帳和對等節點世界狀態資料庫的持續性存放區。
- **公用 ip**：為了與叢集互動而部署之 AKS 叢集的公用 ip 端點。

## <a name="hyperledger-fabric-blockchain-network-setup"></a>Hyperledger 網狀架構區塊鏈網路設定

若要開始，您需要可支援部署數個虛擬機器和標準儲存體帳戶的 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，則可[建立免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

使用下列步驟設定 Hyperledger Fabric 區塊鏈網路：

- [部署排序者/對等組織](#deploy-the-ordererpeer-organization)
- [建立聯盟](#build-the-consortium)
- [執行原生 HLF 作業](#run-native-hlf-operations)

## <a name="deploy-the-ordererpeer-organization"></a>部署排序者/對等組織

若要開始使用 HLF 網路元件部署，請流覽至[Azure 入口網站](https://portal.azure.com)。 選取 [**建立資源] > 區塊鏈**> 搜尋**Azure Kubernetes Service 上**的 [Hyperledger 網狀架構]。

1. 選取 [**建立**] 以啟動範本部署。 **Azure Kubernetes Service 上的 [建立 Hyperledger 網狀架構**] 隨即顯示。

    ![Azure Kubernetes Service 範本上的 Hyperledger 網狀架構](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-fabric-aks.png)

2. 在 [**基本**] 頁面中輸入專案詳細資料。

    ![Azure Kubernetes Service 範本上的 Hyperledger 網狀架構](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-basics.png)

3. 輸入下列詳細資料：
    - **訂**用帳戶：選擇您要部署 HLF 網路元件的訂用帳戶名稱。
    - **資源群組**：建立新的資源群組，或選擇現有的空白資源群組，資源群組會保存部署為範本一部分的所有資源。
    - **區域**：選擇您想要為 HLF 元件部署 azure Kubernetes 叢集的 azure 區域。 此範本適用于 AKS 可用的所有區域，請務必選擇您的訂用帳戶未達到虛擬機器（VM）配額限制的區域。
    - **資源前置**詞：用來命名已部署之資源的前置詞。 資源前置詞的長度必須少於六個字元，且字元的組合必須同時包含數位和字母。
4. 選取 [網狀**架構設定**] 索引標籤，以定義將會部署的 HLF 網路元件。

    ![Azure Kubernetes Service 範本上的 Hyperledger 網狀架構](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-settings.png)

5. 輸入下列詳細資料：
    - **組織名稱**：各種資料平面作業所需的網狀架構組織名稱。 組織名稱在每個部署中必須是唯一的。 
    - 網狀**架構網路元件**：根據您想要設定的區塊鏈網路元件，選擇 [訂購服務] 或 [對等節點]。
    - **節點數目**-以下是兩種類型的節點：
        - 訂購服務-選取要為網路提供容錯能力的節點數目。 只有3、5和7是支援的排序者節點計數。
        - 對等節點-您可以根據您的需求選擇1-10 節點。
    - **對等節點世界狀態資料庫**：選擇 [LevelDB] 和 [CoucbDB]。 當使用者在 [網狀架構網路元件] 下拉式選單中選擇 [對等節點] 時，就會顯示此欄位。
    - 網狀**架構使用者名稱**：輸入用於網狀架構 CA 驗證的使用者名稱。
    - 網狀**架構 ca 密碼**：輸入 fabric ca 驗證的密碼。
    - **確認密碼**：確認網狀架構 CA 密碼。
    - **憑證**：如果您想要使用自己的根憑證來初始化網狀架構 ca，請選擇 [上傳 fabric ca 的根憑證] 選項，否則依預設 Fabric ca 會建立自我簽署憑證。
    - **根憑證**：上傳需要初始化網狀架構 CA 的根憑證（公開金鑰）。 支援 pem 格式的憑證，憑證應以 UTC 時區為有效。
    - **根憑證私密金鑰**：上傳根憑證的私密金鑰。 如果您有結合公用和私密金鑰的 pem 憑證，也請在這裡上傳。


6. 選取 [ **AKS 叢集設定**] 索引標籤，以定義 Azure Kubernetes 叢集設定，這是將在其上設定網狀架構網路元件的基礎結構。

    ![Azure Kubernetes Service 範本上的 Hyperledger 網狀架構](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-aks-cluster-settings-1.png)

7. 輸入下列詳細資料：
    - **Kubernetes 叢集名稱**：所建立之 AKS 叢集的名稱。 此欄位會根據所提供的資源前置詞預先填入，如有必要，您可以變更。
    - **Kubernetes 版本**：將在叢集上部署的 Kubernetes 版本。 根據 [**基本**] 索引標籤中選取的區域，可用的支援版本可能會變更。
    - **DNS 首碼**： AKS 叢集的網域名稱系統（DNS）名稱前置詞。 在建立叢集之後，您將使用 DNS 來連線至 Kubernetes API。
    - **節點大小**： Kubernetes 節點的大小，您可以從 Azure 上提供的 VM 庫存單位（sku）清單中選擇。 為了達到最佳效能，我們建議採用標準 DS3 v2。
    - **節點計數**：要在叢集中部署的 Kubernetes 節點數目計數。 我們建議讓此節點計數至少等於或大於 [網狀架構設定] 中指定的 HLF 節點數目。
    - **服務主體用戶端識別碼**：輸入現有服務主體的用戶端識別碼，或建立新的，這是 AKS authentication 的必要項。 請參閱[建立服務主體](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-3.2.0#create-a-service-principal)的步驟。
    - **服務主體用戶端密碼**：輸入服務主體用戶端識別碼中提供之服務主體的用戶端密碼。
    - **確認用戶端密碼**：確認服務主體用戶端密碼中提供的用戶端密碼。
    - **啟用容器監視**：選擇啟用 AKS 監視，可讓 AKS 記錄推送至指定的 Log Analytics 工作區。
    - **Log analytics 工作區**： log analytics 工作區將會填入在啟用監視時所建立的預設工作區。

8. 提供上述所有詳細資料之後，請選取 [**審查] 和 [建立**] 索引標籤。[審查] 和 [建立] 會觸發您所提供值的驗證。
9. 通過驗證之後，您可以選取 [**建立**]。
部署通常需要10-12 分鐘的時間，可能會根據指定的 AKS 節點大小和數目而有所不同。
10. 部署成功之後，您會在右上角透過 Azure 通知來通知您。
11. 選取 [**移至資源群組**]，以檢查範本部署過程中建立的所有資源。 所有資源名稱的開頭都是 [**基本**] 設定中提供的前置詞。

## <a name="build-the-consortium"></a>建立聯盟

若要建立區塊鏈聯盟文章部署訂購服務和對等節點，您需要依序執行下列步驟。 **建立您的網路**腳本（byn.sh），協助您設定聯盟、建立通道，以及安裝鏈碼。

> [!NOTE]
> 提供的組建您的網路（byn）腳本嚴格用於示範/devtest 案例。 針對生產等級設定，我們建議使用原生 HLF Api。

所有執行 byn 腳本的命令都可以透過 Azure Bash 命令列介面（CLI）來執行。 您可以透過登入 Azure shell web 版本 ![Azure Kubernetes Service 範本上的 Hyperledger 網狀架構](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) 選項，位於 Azure 入口網站的右上角。 在命令提示字元中，輸入 bash，然後輸入以切換至 bash CLI。

如需詳細資訊，請參閱[Azure shell](https://docs.microsoft.com/azure/cloud-shell/overview) 。

![Azure Kubernetes Service 範本上的 Hyperledger 網狀架構](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


下載 byn.sh 和 fabric-yaml 檔案。

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/consortiumScripts/byn.sh -o byn.sh; chmod 777 byn.sh
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/consortiumScripts/fabric-admin.yaml -o fabric-admin.yaml
```
**在 Azure CLI Bash shell 上設定下列環境變數**：

設定通道資訊和排序者組織資訊

```bash
SWITCH_TO_AKS_CLUSTER() { az aks get-credentials --resource-group $1 --name $2 --subscription $3; }
ORDERER_AKS_SUBSCRIPTION=<ordererAKSClusterSubscriptionID>
ORDERER_AKS_RESOURCE_GROUP=<ordererAKSClusterResourceGroup>
ORDERER_AKS_NAME=<ordererAKSClusterName>
ORDERER_DNS_ZONE=$(az aks show --resource-group $ORDERER_AKS_RESOURCE_GROUP --name $ORDERER_AKS_NAME --subscription $ORDERER_AKS_SUBSCRIPTION -o json | jq .addonProfiles.httpApplicationRouting.config.HTTPApplicationRoutingZoneName | tr -d '"')
ORDERER_END_POINT="orderer1.$ORDERER_DNS_ZONE:443"
CHANNEL_NAME=<channelName>
```
設定對等組織資訊

```bash
PEER_AKS_RESOURCE_GROUP=<peerAKSClusterResourceGroup>
PEER_AKS_NAME=<peerAKSClusterName>
PEER_AKS_SUBSCRIPTION=<peerAKSClusterSubscriptionID>
#Peer organization name is case-sensitive. Specify exactly the same name, which was provided while creating the Peer AKS Cluster.
PEER_ORG_NAME=<peerOrganizationName>
```

建立一個 Azure 檔案共用，以在對等和排序者組織之間共用各種公開憑證。

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

移至排序者組織 AKS 叢集和發出命令以建立新的通道

```bash
SWITCH_TO_AKS_CLUSTER $ORDERER_AKS_RESOURCE_GROUP $ORDERER_AKS_NAME $ORDERER_AKS_SUBSCRIPTION
./byn.sh createChannel "$CHANNEL_NAME"
```

**聯盟管理命令**

在指定的順序中執行下列命令，以在通道和聯盟中加入對等組織。

1. 移至對等組織 AKS 叢集，並上傳其在 Azure 檔案儲存體上的成員服務提供（MSP）。

    ```bash
    SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
    ./byn.sh uploadOrgMSP "$AZURE_FILE_CONNECTION_STRING"
    ```

2. 前往排序者組織 AKS 叢集，並在 channel 和聯盟中新增對等組織。

    ```bash
    SWITCH_TO_AKS_CLUSTER $ORDERER_AKS_RESOURCE_GROUP $ORDERER_AKS_NAME $ORDERER_AKS_SUBSCRIPTION
    #add peer in consortium
    ./byn.sh addPeerInConsortium "$PEER_ORG_NAME" "$AZURE_FILE_CONNECTION_STRING"
    #add peer in channel
    ./byn.sh addPeerInChannel "$PEER_ORG_NAME" "$CHANNEL_NAME" "$AZURE_FILE_CONNECTION_STRING"
    ```

3. 回到 [對等組織] 和 [發出命令]，以聯結通道中的對等節點。

    ```bash
    SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
    ./byn.sh joinNodesInChannel "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
    ```

同樣地，若要在通道中新增更多對等組織，請依據所需的對等組織更新對等 AKS 環境變數，並執行步驟1到3。

**鏈碼管理命令**

執行下列命令以執行鏈碼相關的作業。 這些命令會在示範鏈碼上執行所有作業。 這個示範鏈碼有兩個變數 "a" 和 "b"。 在鏈碼具現化時，"a" 會初始化為1000，而 "b" 會以2000初始化。 在每次叫用鏈碼時，10個單位會從 "a" 傳送到 "b"。 鏈碼上的查詢作業會顯示「a」變數的世界狀態。

執行在對等組織 AKS 叢集上執行的下列命令。

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

## <a name="run-native-hlf-operations"></a>執行原生 HLF 作業

協助客戶開始在 AKS 上的 HLF 網路上執行 Hyperledger native 命令。 提供的範例應用程式會使用網狀架構 NodeJS SDK 來執行 HLF 作業。 系統會提供命令來建立新的使用者身分識別，並安裝您自己的鏈碼。

### <a name="before-you-begin"></a>開始之前

針對應用程式的初始設定，請遵循下列命令：

- 下載應用程式檔
- 產生連線設定檔和系統管理員設定檔
- 匯入管理使用者身分識別

完成初始設定之後，您可以使用 SDK 來達成下列作業：

- 使用者身分識別產生
- 鏈碼作業

上述命令可以從 Azure Cloud Shell 執行。

### <a name="download-application-files"></a>下載應用程式檔

執行應用程式的第一個設定是下載資料夾中的所有應用程式檔。

**建立應用程式資料夾並在資料夾中輸入**：

```bash
mkdir app
cd app
```
執行下列命令以下載所有必要的檔案和套件：

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/application/setup.sh | bash
```
此命令需要一些時間來載入所有的封裝。 成功執行命令之後，您會在目前的目錄`node_modules`中看到資料夾。 所有必要的套件都會載入到`node_modules`資料夾中。

### <a name="generate-connection-profile-and-admin-profile"></a>產生連線設定檔和系統管理員設定檔

在`profile` `app`資料夾內建立目錄

```bash
cd app
mkdir ./profile
```
在 Azure cloud shell 上設定這些環境變數

```bash
# Organization name whose connection profile is to be generated
ORGNAME=<orgname>
# Organization AKS cluster resource group
AKS_RESOURCE_GROUP=<resourceGroup>
```

執行下列命令以產生組織的連線設定檔和系統管理員設定檔

```bash
./getConnector.sh $AKS_RESOURCE_GROUP | sed -e "s/{action}/gateway/g"| xargs curl > ./profile/$ORGNAME-ccp.json
./getConnector.sh $AKS_RESOURCE_GROUP | sed -e "s/{action}/admin/g"| xargs curl > ./profile/$ORGNAME-admin.json
```

它會在設定檔資料夾中`profile` ，分別使用名稱`<orgname>-ccp.json`和`<orgname>-admin.json`來建立組織的連線設定檔和管理員。

同樣地，為每個排序者和對等組織產生連線設定檔和系統管理員設定檔。


### <a name="import-admin-user-identity"></a>匯入管理使用者身分識別

最後一個步驟是在錢包中匯入組織的系統管理員使用者身分識別。

```bash
npm run importAdmin -- -o <orgName>

```
上述命令會執行 importAdmin，將系統管理員使用者身分識別匯入錢包。 腳本會從系統管理員設定檔`<orgname>-admin.json`讀取管理身分識別，並將其匯入以錢包執行 HLF 作業。

腳本會使用檔案系統錢包來儲存身分識別。 它會根據連線設定檔中「錢包」欄位所指定的路徑來建立錢包。 根據預設，會使用`<orgname>`初始化「錢包」欄位，這表示會在目前的目錄`<orgname>`中建立名為的資料夾來儲存身分識別。 如果您想要建立其他路徑的錢包，請在連線設定檔中修改「錢包」欄位，再執行註冊管理使用者和任何其他 HLF 作業。

同樣地，為每個組織匯入管理使用者身分識別。

如需命令中傳遞之引數的詳細資訊，請參閱命令說明。

```bash
npm run importAdmin -- -h

```

### <a name="user-identity-generation"></a>使用者身分識別產生

在指定的順序中執行下列命令，以產生 HLF 組織的新使用者識別。

> [!NOTE]
> 開始進行使用者身分識別產生步驟之前，請確定應用程式的初始設定已完成。

在 azure cloud shell 上設定下列環境變數

```bash
# Organization name for which user identity is to be generated
ORGNAME=<orgname>
# Name of new user identity. Identity will be registered with the Fabric-CA using this name.
USER_IDENTITY=<username>

```

註冊並註冊新的使用者

若要註冊並註冊新的使用者，請執行下列執行 registerUser 的命令。 它會將產生的使用者身分識別儲存在錢包中。

```bash
npm run registerUser -- -o $ORGNAME -u $USER_IDENTITY

```

> [!NOTE]
> 系統管理員使用者身分識別是用來發出新使用者的註冊命令。 因此，在執行此命令之前，必須先在錢包中擁有系統管理員使用者身分識別。 否則，此命令將會失敗。

如需命令中傳遞之引數的詳細資訊，請參閱命令說明

```bash
npm run registerUser -- -h

```

### <a name="chaincode-operations"></a>鏈碼作業


> [!NOTE]
> 開始進行任何鏈碼作業之前，請確定應用程式的初始設定已完成。

在 Azure Cloud shell 上設定下列鏈碼特定環境變數：

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

以下是可執行檔鏈碼作業：

- 安裝鏈碼
- 具現化鏈碼
- 叫用鏈碼
- 查詢鏈碼

### <a name="install-chaincode"></a>安裝鏈碼

執行下列命令以在對等組織上安裝鏈碼。

```bash
npm run installCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION

```
它會在環境變數中`ORGNAME`設定的組織的所有對等節點上安裝鏈碼。 如果您的通道中有兩個或多個對等組織，而您想要在所有的節點上安裝鏈碼，請針對每個對等組織分別執行命令。

請遵循下列步驟：

- 將`ORGNAME`設定`<peerOrg1Name>`為， `installCC`併發出命令。
- 將`ORGNAME`設定`<peerOrg2Name>`為， `installCC`併發出命令。

  針對每個對等組織執行此程式。

如需命令中傳遞之引數的詳細資訊，請參閱命令說明。

```bash
npm run installCC -- -h

```

### <a name="instantiate-chaincode"></a>具現化鏈碼

執行下列命令，在對等上具現化鏈碼。

```bash
npm run instantiateCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -v $CC_VERSION -l $CC_LANG -c $CHANNEL -f <instantiateFunc> -a <instantiateFuncArgs>

```
將具現化函式名稱和以逗號分隔`<instantiateFunc>`的`<instantiateFuncArgs>`引數清單分別傳入和。 例如，在[fabrcar 鏈碼](https://github.com/hyperledger/fabric-samples/blob/release/chaincode/fabcar/fabcar.go)中，將鏈碼設定`<instantiateFunc>`為`"Init"` ，並`<instantiateFuncArgs>`將設為空`""`字串。

> [!NOTE]
> 從通道中的任何一個對等組織執行一次命令。
> 交易成功提交至排序者後，排序者會將此交易散發給通道中的所有對等組織。 因此，鏈碼會在通道中的所有對等組織的所有對等節點上具現化。

如需命令中傳遞之引數的詳細資訊，請參閱命令說明

```bash
npm run instantiateCC -- -h

```

### <a name="invoke-chaincode"></a>叫用鏈碼

執行下列命令來叫用鏈碼函數：

```bash
npm run invokeCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL -f <invokeFunc> -a <invokeFuncArgs>

```
分別在和`<invokeFunction>` `<invokeFuncArgs>`中傳遞叫用函式名稱和以逗號分隔的引數清單。 繼續進行 fabcar 鏈碼範例，以叫用將 initLedger 函`<invokeFunction>`式`"initLedger"`設`<invokeFuncArgs>`為`""`和的。

> [!NOTE]
> 從通道中的任何一個對等組織執行一次命令。
> 交易成功提交至排序者後，排序者會將此交易散發給通道中的所有對等組織。 因此，在通道中所有對等組織的所有對等節點上都會更新世界狀態。

如需命令中傳遞之引數的詳細資訊，請參閱命令說明

```bash
npm run invokeCC -- -h

```

### <a name="query-chaincode"></a>查詢鏈碼

執行下列命令以查詢鏈碼：

```bash
npm run queryCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL -f <queryFunction> -a <queryFuncArgs>

```

分別傳遞查詢函式名稱和以逗號分隔的`<queryFunction>`引數清單。 `<queryFuncArgs>` 同樣地， `fabcar`將鏈碼做為參考，以查詢全球狀態中的所有車輛`<queryFunction>`設定`"queryAllCars"`為`<queryArgs>`和`""`。

如需命令中傳遞之引數的詳細資訊，請參閱命令說明

```bash
npm run queryCC -- -h

```
