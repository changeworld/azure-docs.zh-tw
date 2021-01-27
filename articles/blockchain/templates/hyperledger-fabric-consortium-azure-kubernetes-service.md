---
title: 在 Azure Kubernetes Service 上部署 Hyperledger Fabric 聯盟
description: 如何在 Azure Kubernetes Service 上部署和設定 Hyperledger Fabric 聯盟網路
ms.date: 01/08/2021
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: c0e7f3e7ab83f64cebd990de57d48c97891edb7f
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897253"
---
# <a name="deploy-hyperledger-fabric-consortium-on-azure-kubernetes-service"></a>在 Azure Kubernetes Service 上部署 Hyperledger Fabric 聯盟

您可以使用 Azure Kubernetes Service (AKS) 範本上的 Hyperledger 網狀架構，在 Azure 上部署和設定 Hyperledger 網狀架構聯盟網路。

閱讀本文之後，您將能夠：

- 具備 Hyperledger 網狀架構的實用知識，以及構成 Hyperledger Fabric 區塊鏈網路構成要素的元件。
- 瞭解如何在您的生產案例 Azure Kubernetes Service 上部署和設定 Hyperledger Fabric 聯盟網路。

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="choose-an-azure-blockchain-solution"></a>選擇 Azure 區塊鏈解決方案

在您選擇使用解決方案範本之前，請將您的案例與可用 Azure 區塊鏈選項的常見使用案例進行比較：

選項 | 服務模型 | 常見使用案例
-------|---------------|-----------------
解決方案範本 | IaaS | 解決方案範本是 Azure Resource Manager 範本，可讓您用來布建完整設定的區塊鏈網路拓撲。 範本會部署並設定區塊鏈網路類型 Microsoft Azure 計算、網路和儲存體服務。 解決方案範本是在沒有服務等級協定的情況下提供。 使用 [Microsoft Q&頁面](/answers/topics/azure-blockchain-workbench.html) 以取得支援。
[Azure 區塊鏈服務](../service/overview.md) | PaaS | Azure 區塊鏈服務 Preview 簡化了聯盟區塊鏈網路的構成、管理和治理。 針對需要 PaaS、聯盟管理或合約和交易隱私權的解決方案，使用 Azure 區塊鏈服務。
[Azure Blockchain Workbench](../workbench/overview.md) | IaaS 和 PaaS | Azure Blockchain Workbench Preview 是 Azure 服務和功能的集合，可協助您建立及部署區塊鏈應用程式，以與其他組織共用商務程式和資料。 使用 Azure Blockchain Workbench 來建立區塊鏈解決方案的原型，或區塊鏈應用程式的概念證明。 如果沒有服務等級協定，就會提供 Azure Blockchain Workbench。 使用 [Microsoft Q&頁面](/answers/topics/azure-blockchain-workbench.html) 以取得支援。

## <a name="hyperledger-fabric-consortium-architecture"></a>Hyperledger Fabric 聯盟架構

若要在 Azure 上建立 Hyperledger 網狀架構網路，您需要部署具有對等節點的訂購服務和組織。 藉由使用 Hyperledger Fabric on Azure Kubernetes Service 解決方案範本，您可以建立訂單節點或對等節點。 您需要為您想要建立的每個節點部署範本。

在範本部署中建立的基本元件如下：

- **排序者節點**：負責總帳中交易順序的節點。 以及其他節點，已排序的節點會形成 Hyperledger 網狀架構網路的訂購服務。

- **對等節點**：主要裝載總帳和智慧型合約的節點，這是網路的基本元素。

- 網狀 **架構 CA**： Hyperledger FABRIC (CA) 的憑證授權單位單位。 網狀架構 CA 可讓您初始化和啟動裝載憑證授權單位單位的伺服器進程。 它可讓您管理身分識別及憑證。 每個部署為範本一部分的 AKS 叢集預設會有網狀架構 CA pod。

- **CouchDB 或 LevelDB**：對等節點的全球狀態資料庫。 LevelDB 是內嵌于對等節點中的預設狀態資料庫。 它會將鏈碼的資料儲存為簡單的索引鍵/值組，並僅支援索引鍵、索引鍵範圍和複合索引鍵查詢。 CouchDB 是選擇性的替代狀態資料庫，可在鏈碼資料值模型化為 JSON 時支援豐富的查詢。

部署上的範本會在您的訂用帳戶中加速各種 Azure 資源。 已部署的 Azure 資源如下：

- **AKS** 叢集：根據客戶提供的輸入參數設定的 Azure Kubernetes Service 叢集。 AKS 叢集有針對執行 Hyperledger 網狀架構網路元件而設定的各種 pod。 建立的 pod 如下：

  - 網狀 **架構工具**：負責設定 Hyperledger Fabric 元件的工具。
  - **排序者/對等** Pod： Hyperledger 網狀架構網路的節點。
  - **Proxy**： NGNIX Proxy pod，用戶端應用程式可以透過此 pod 來與 AKS 叢集通訊。
  - 網狀架構 **ca**：執行網狀架構 ca 的 pod。
- **于 postgresql**：維護網狀架構 CA 身分識別的資料庫實例。

- **Key vault**：部署 Azure Key Vault 服務的實例，用來儲存網狀架構 CA 認證和客戶提供的根憑證。 保存庫會在範本部署重試時使用，以處理範本的機制。
- **受控磁片**： Azure 受控磁碟服務的實例，可為總帳和對等節點的世界狀態資料庫提供持續性存放區。
- **公用 IP**：部署來與叢集通訊的 AKS 叢集端點。

## <a name="deploy-the-orderer-and-peer-organization"></a>部署排序者和對等組織

若要開始，您需要可支援部署數個虛擬機器和標準儲存體帳戶的 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，您可以 [建立免費的 azure 帳戶](https://azure.microsoft.com/free/)。

若要開始部署 Hyperledger Fabric 網路元件，請移至 [Azure 入口網站](https://portal.azure.com)。

1. 選取 [**建立資源**]  >  **區塊鏈**，然後 **在 Azure Kubernetes Service (preview) 上搜尋 Hyperledger Fabric**。

2. 在 [ **基本** ] 索引標籤上輸入專案詳細資料。

    ![顯示 [基本] 索引標籤的螢幕擷取畫面。](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-basics.png)

3. 輸入下列詳細資料：
    - **訂** 用帳戶：選擇您想要部署 Hyperledger Fabric 網路元件的訂用帳戶名稱。
    - **資源群組**：建立新的資源群組，或選擇現有的空白資源群組。 資源群組會保留所有部署為範本一部分的資源。
    - **區域**：選擇您要為 Hyperledger Fabric 元件部署 Azure Kubernetes Service 叢集的 Azure 區域。 範本適用于 AKS 可使用的所有區域。 選擇您的訂用帳戶未達到虛擬機器 (VM) 配額限制的區域。
    - **資源首碼**：輸入用來命名已部署之資源的前置詞。 它必須少於6個字元，且字元的組合必須同時包含數位和字母。
4. 選取 [網狀 **架構設定** ] 索引標籤，定義將部署的 Hyperledger Fabric 網路元件。

    ![顯示 [網狀架構設定] 索引標籤的螢幕擷取畫面。](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-settings.png)

5. 輸入下列詳細資料：
    - **組織名稱**：輸入各種資料平面作業所需的 Hyperledger Fabric 組織名稱。 每個部署的組織名稱都必須是唯一的。
    - 網狀 **架構網路元件**：根據您要設定的區塊鏈網路元件，選擇 **訂購服務** 或 **對等節點**。
    - **節點數目**：以下是兩種類型的節點：
        - **訂購服務**：選取要為網路提供容錯的節點數目。 支援的 order 節點計數為3、5和7。
        - **對等節點**：您可以根據您的需求選擇1到10個節點。
    - **對等節點世界狀態資料庫**：選擇 LevelDB 和 CouchDB。 當您在 [網狀 **架構網路元件**] 下拉式清單中選擇 [**對等節點**] 時，會顯示此欄位。
    - 網狀 **架構 ca 使用者名稱**：輸入用於網狀架構 ca 驗證的使用者名稱。
    - 網狀 **架構 ca 密碼**：輸入 fabric ca 驗證的密碼。
    - **確認密碼**：確認網狀架構 CA 密碼。
    - **憑證**：如果您想要使用自己的根憑證來初始化網狀架構 ca，請選擇 [ **上傳網狀架構 ca 的根憑證** ] 選項。 否則，網狀架構 CA 預設會建立自我簽署憑證。
    - 跟 **證書**：上傳根憑證 (公開金鑰) ，必須將網狀架構 CA 初始化。 支援 pem 格式的憑證。 憑證必須是有效的，而且必須是 UTC 時區。
    - **根憑證私密金鑰**：上傳根憑證的私密金鑰。 如果您有包含合併的公開和私密金鑰的 pem 憑證，也請在此上傳。


6. 選取 [ **AKS 叢集設定** ] 索引標籤，以定義將設定 Hyperledger Fabric 網路元件的基礎結構 Azure Kubernetes Service 叢集設定。

    ![顯示 [K S 叢集設定] 索引標籤的螢幕擷取畫面。](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-aks-cluster-settings-1.png)

7. 輸入下列詳細資料：
    - **Kubernetes 叢集名稱**：如有必要，請變更 AKS 叢集的名稱。 此欄位會根據所提供的資源前置詞預先填入。
    - **Kubernetes 版本**：選擇將在叢集中部署的 Kubernetes 版本。 根據您在 [ **基本** ] 索引標籤上選取的區域，可用的支援版本可能會變更。
    - **Dns 首碼**：輸入 AKS 叢集的網域名稱系統 (DNS) 名稱首碼。 當您在建立叢集之後管理容器時，您將會使用 DNS 連線到 Kubernetes API。
    - **節點大小**：針對 Kubernetes 節點的大小，您可以從 Azure 上提供的 VM 庫存單位 (sku) 清單中選擇。 為了達到最佳效能，我們建議標準 DS3 v2。
    - **節點計數**：輸入要在叢集中部署的 Kubernetes 節點數目。 建議您讓此節點計數保持等於或大於 [網狀 **架構設定** ] 索引標籤上指定的 Hyperledger Fabric 節點數目。
    - **服務主體用戶端識別碼**：輸入現有服務主體的用戶端識別碼，或建立一個新的服務主體。 AKS authentication 需要服務主體。 請參閱 [建立服務主體的步驟](/powershell/azure/create-azure-service-principal-azureps#create-a-service-principal)。
    - **服務主體用戶端密碼**：輸入服務主體的用戶端識別碼中提供的服務主體用戶端密碼。
    - **確認用戶端密碼**：確認服務主體的用戶端密碼。
    - **啟用容器監視**：選擇啟用 AKS 監視，可讓 AKS 記錄推送至指定的 Log Analytics 工作區。
    - **Log analytics 工作區**： log analytics 工作區將會填入啟用監視時所建立的預設工作區。

8. 選取 [ **審核] 和 [建立** ] 索引標籤。此步驟會觸發您所提供之值的驗證。
9. 通過驗證之後，請選取 [ **建立**]。

    部署通常需要10到12分鐘。 時間可能會根據指定的 AKS 節點大小和數目而有所不同。
10. 成功部署之後，您會在右上角透過 Azure 通知通知您。
11. 選取 [ **移至資源群組** ]，以檢查範本部署中建立的所有資源。 所有資源名稱的開頭都是 [ **基本** ] 索引標籤上提供的前置詞。

## <a name="build-the-consortium"></a>打造聯盟

若要在部署訂購服務和對等節點之後建立區塊鏈聯盟，請依序執行下列步驟。 Azure Hyperledger Fabric 腳本 (azhlf) 可協助您設定聯盟、建立通道，以及執行鏈碼作業。

> [!NOTE]
> Azure Hyperledger Fabric (azhlf) 腳本已更新為提供更多功能。 如果您想要參考舊的腳本，請參閱 [GitHub 上的讀我檔案](https://github.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/blob/master/consortiumScripts/README.md)。 此腳本與 Azure Kubernetes Service 範本版本2.0.0 和更新版本上的 Hyperledger Fabric 相容。 若要檢查部署的版本，請遵循 [疑難排解](#troubleshoot)的步驟。

> [!NOTE]
> 此腳本僅提供協助示範、開發和測試案例。 此腳本所建立的通道和聯盟具有基本的 Hyperledger 網狀架構原則，可簡化示範、開發和測試案例。 針對生產環境設定，我們建議使用原生 Hyperledger Fabric Api，根據您組織的合規性需求來更新通道/聯盟 Hyperledger 網狀架構原則。


執行 Azure Hyperledger Fabric 腳本的所有命令都可以透過 Azure Bash 命令列介面 (CLI) 來執行。 您可以使用 ![ Azure 入口網站右上角的 [Hyperledger Fabric on Azure Kubernetes Service 範本] 選項來登入 Azure Cloud Shell ](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) 。 在命令提示字元中，輸入 `bash` 並選取 Enter 鍵以切換至 BASH CLI，或從 Cloud Shell 工具列選取 **Bash** 。

如需詳細資訊，請參閱 [Azure Cloud Shell](../../cloud-shell/overview.md) 。

![顯示 Azure Cloud Shell 中命令的螢幕擷取畫面。](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


下圖顯示在排序者組織與對等組織之間建立聯盟的逐步流程。 下列各節顯示完成這些步驟的詳細命令。

![建立聯盟之流程的圖表。](./media/hyperledger-fabric-consortium-azure-kubernetes-service/process-to-build-consortium-flow-chart.png)

完成初始設定之後，請使用用戶端應用程式來完成下列作業：  

- 通道管理
- 聯盟管理
- 鏈碼管理

### <a name="download-client-application-files"></a>下載用戶端應用程式檔

第一個設定是下載用戶端應用程式檔。 執行下列命令以下載所有必要的檔案和套件：

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/azhlfToolSetup.sh | bash
cd azhlfTool
npm install
npm run setup
```

這些命令會從公用 GitHub 存放庫複製 Azure Hyperledger Fabric 用戶端應用程式程式碼，然後載入所有相依的 npm 套件。 成功執行命令之後，您可以在目前的目錄中看到 node_modules 資料夾。 所有必要的套件都會載入 node_modules 資料夾。

### <a name="set-up-environment-variables"></a>設定環境變數

所有環境變數都會遵循 Azure 資源命名慣例。

#### <a name="set-environment-variables-for-the-orderer-organizations-client"></a>為排序者組織的用戶端設定環境變數

```bash
ORDERER_ORG_SUBSCRIPTION=<ordererOrgSubscription>
ORDERER_ORG_RESOURCE_GROUP=<ordererOrgResourceGroup>
ORDERER_ORG_NAME=<ordererOrgName>
ORDERER_ADMIN_IDENTITY="admin.$ORDERER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

#### <a name="set-environment-variables-for-the-peer-organizations-client"></a>為對等組織的用戶端設定環境變數

```bash
PEER_ORG_SUBSCRIPTION=<peerOrgSubscritpion>
PEER_ORG_RESOURCE_GROUP=<peerOrgResourceGroup>
PEER_ORG_NAME=<peerOrgName>
PEER_ADMIN_IDENTITY="admin.$PEER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

根據聯盟中的對等組織數目，您可能需要重複對等命令，並據以設定環境變數。

#### <a name="set-environment-variables-for-an-azure-storage-account"></a>設定 Azure 儲存體帳戶的環境變數

```bash
STORAGE_SUBSCRIPTION=<subscriptionId>
STORAGE_RESOURCE_GROUP=<azureFileShareResourceGroup>
STORAGE_ACCOUNT=<azureStorageAccountName>
STORAGE_LOCATION=<azureStorageAccountLocation>
STORAGE_FILE_SHARE=<azureFileShareName>
```

使用下列命令來建立 Azure 儲存體帳戶。 如果您已經有 Azure 儲存體帳戶，請略過此步驟。

```bash
az account set --subscription $STORAGE_SUBSCRIPTION
az group create -l $STORAGE_LOCATION -n $STORAGE_RESOURCE_GROUP
az storage account create -n $STORAGE_ACCOUNT -g  $STORAGE_RESOURCE_GROUP -l $STORAGE_LOCATION --sku Standard_LRS
```

使用下列命令，在 Azure 儲存體帳戶中建立檔案共用。 如果您已經有檔案共用，請略過此步驟。

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
az storage share create  --account-name $STORAGE_ACCOUNT  --account-key $STORAGE_KEY  --name $STORAGE_FILE_SHARE
```

使用下列命令來產生 Azure 檔案共用的連接字串。

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
SAS_TOKEN=$(az storage account generate-sas --account-key $STORAGE_KEY --account-name $STORAGE_ACCOUNT --expiry `date -u -d "1 day" '+%Y-%m-%dT%H:%MZ'` --https-only --permissions lruwd --resource-types sco --services f | tr -d '"')
AZURE_FILE_CONNECTION_STRING=https://$STORAGE_ACCOUNT.file.core.windows.net/$STORAGE_FILE_SHARE?$SAS_TOKEN

```

### <a name="import-an-organization-connection-profile-admin-user-identity-and-msp"></a>匯入組織連線設定檔、管理使用者身分識別和 MSP

使用下列命令，從 Azure Kubernetes Service 叢集提取組織的連線設定檔、管理使用者身分識別和受控服務提供者 (MSP) ，並將這些身分識別儲存在用戶端應用程式的本機存放區中。 本機存放區的範例是 *azhlfTool/* store 目錄。

針對排序者組織：

```bash
./azhlf adminProfile import fromAzure -o $ORDERER_ORG_NAME -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION
./azhlf connectionProfile import fromAzure -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION -o $ORDERER_ORG_NAME   
./azhlf msp import fromAzure -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION -o $ORDERER_ORG_NAME
```

對於對等組織：

```bash
./azhlf adminProfile import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
./azhlf connectionProfile import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
./azhlf msp import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
```

### <a name="create-a-channel"></a>建立通道

從排序者組織的用戶端，使用下列命令來建立只包含排序者組織的通道。  

```bash
./azhlf channel create -c $CHANNEL_NAME -u $ORDERER_ADMIN_IDENTITY -o $ORDERER_ORG_NAME
```

### <a name="add-a-peer-organization-for-consortium-management"></a>新增聯盟管理的對等組織

>[!NOTE]
> 在開始進行任何聯盟作業之前，請確定您已完成用戶端應用程式的初始設定。  

依指定的循序執行下列命令，以在通道和聯盟中新增對等組織： 

1.  從對等組織的用戶端，在 Azure 儲存體上傳對等組織的 MSP。

      ```bash
      ./azhlf msp export toAzureStorage -f  $AZURE_FILE_CONNECTION_STRING -o $PEER_ORG_NAME
      ```
2.  從排序者組織的用戶端，下載 Azure 儲存體的對等組織 MSP。 然後發出命令，將對等組織新增至通道和聯盟。

      ```bash
      ./azhlf msp import fromAzureStorage -o $PEER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel join -c  $CHANNEL_NAME -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ./azhlf consortium join -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ```

3.  從排序者組織的用戶端，在 Azure 儲存體上傳排序者的連線設定檔，讓對等組織可以使用此連線設定檔連接到排序者節點。

      ```bash
      ./azhlf connectionProfile  export toAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ```

4.  從對等組織的用戶端，從 Azure 儲存體下載排序者的連線設定檔。 然後執行命令，將對等節點加入通道中。

      ```bash
      ./azhlf connectionProfile  import fromAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel joinPeerNodes -o $PEER_ORG_NAME  -u $PEER_ADMIN_IDENTITY -c $CHANNEL_NAME --ordererOrg $ORDERER_ORG_NAME
      ```

同樣地，若要在通道中新增更多對等組織，請根據所需的對等組織更新對等環境變數，然後重做步驟1到4。

### <a name="set-anchor-peers"></a>設定錨點對等

從對等組織的用戶端，執行命令以針對指定通道中的對等組織設定錨點對等。

>[!NOTE]
> 執行此命令之前，請確定已使用聯盟管理命令將對等組織新增至通道。

```bash
./azhlf channel setAnchorPeers -c $CHANNEL_NAME -p <anchorPeersList> -o $PEER_ORG_NAME -u $PEER_ADMIN_IDENTITY --ordererOrg $ORDERER_ORG_NAME
```

`<anchorPeersList>` 這是要設定為錨點對等之對等節點的空格分隔清單。 例如：

  - `<anchorPeersList>` `"peer1"` 如果您只想要將 peer1 節點設定為錨點對等，則設定為。
  - `<anchorPeersList>` `"peer1" "peer3"` 如果您想要將 peer1 和 peer3 節點設定為錨點對等，請設定為。

## <a name="chaincode-management-commands"></a>鏈碼管理命令

>[!NOTE]
> 在開始進行任何鏈碼作業之前，請確定您已完成用戶端應用程式的初始設定。  

### <a name="set-the-chaincode-specific-environment-variables"></a>設定鏈碼特定的環境變數

```bash
# Peer organization name where the chaincode operation will be performed
ORGNAME=<PeerOrgName>
USER_IDENTITY="admin.$ORGNAME"  
# If you are using chaincode_example02 then set CC_NAME=“chaincode_example02”
CC_NAME=<chaincodeName>  
# If you are using chaincode_example02 then set CC_VERSION=“1” for validation
CC_VERSION=<chaincodeVersion>
# Language in which chaincode is written. Supported languages are 'node', 'golang', and 'java'  
# Default value is 'golang'  
CC_LANG=<chaincodeLanguage>  
# CC_PATH contains the path where your chaincode is placed. This is the absolute path to the chaincode project root directory.
# If you are using chaincode_example02 to validate then CC_PATH=“/home/<username>/azhlfTool/samples/chaincode/src/chaincode_example02/go”
CC_PATH=<chaincodePath>  
# Channel on which chaincode will be instantiated/invoked/queried  
CHANNEL_NAME=<channelName>  
```

### <a name="install-chaincode"></a>安裝鏈碼  

執行下列命令，在對等組織上安裝鏈碼。  

```bash
./azhlf chaincode install -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION  

```
此命令會在環境變數中設定之對等組織的所有對等節點上安裝鏈碼 `ORGNAME` 。 如果有兩個或多個對等組織在您的通道中，而您想要在所有電腦上安裝鏈碼，請為每個對等組織分別執行此命令。  

請遵循下列步驟：  

1.  `ORGNAME`根據設定和 `USER_IDENTITY` `peerOrg1` 執行 `./azhlf chaincode install` 命令。  
2.  `ORGNAME`根據設定和 `USER_IDENTITY` `peerOrg2` 執行 `./azhlf chaincode install` 命令。  

### <a name="instantiate-chaincode"></a>具現化鏈碼  

從對等用戶端應用程式中執行下列命令，以將通道上的鏈碼具現化。  

```bash
./azhlf chaincode instantiate -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -v $CC_VERSION -c $CHANNEL_NAME -f <instantiateFunc> --args <instantiateFuncArgs>
```

在和中，分別傳遞具現化函式名稱和以空格分隔的引數清單 `<instantiateFunc>` `<instantiateFuncArgs>` 。 例如，若要將 chaincode_example02 具現化，請將設 `<instantiateFunc>` 為，並將設定為 `init` `<instantiateFuncArgs>` `"a" "2000" "b" "1000"` 。

您也可以使用旗標來傳遞集合的設定 JSON 檔案 `--collections-config` 。 或者，在具現 `-t` 化用於私用交易的鏈碼時，使用旗標來設定暫時性引數。

例如：

```bash
./azhlf chaincode instantiate -c $CHANNEL_NAME -n $CC_NAME -v $CC_VERSION -o $ORGNAME -u $USER_IDENTITY --collections-config <collectionsConfigJSONFilePath>
./azhlf chaincode instantiate -c $CHANNEL_NAME -n $CC_NAME -v $CC_VERSION -o $ORGNAME -u $USER_IDENTITY --collections-config <collectionsConfigJSONFilePath> -t <transientArgs>
```

`<collectionConfigJSONFilePath>`元件是 JSON 檔案的路徑，其中包含為私用資料鏈碼具現化所定義的集合。 您可以在下列路徑找到相對於 *azhlfTool* 目錄的範例集合設定 JSON 檔案： `./samples/chaincode/src/private_marbles/collections_config.json` 。
以 `<transientArgs>` 字串格式傳遞有效的 JSON。 Escape 任何特殊字元。 例如：`'{\\\"asset\":{\\\"name\\\":\\\"asset1\\\",\\\"price\\\":99}}'`

> [!NOTE]
> 從通道中的任何一個對等組織執行一次命令。 將交易成功提交至排序者之後，排序者會將此交易散發給通道中的所有對等組織。 然後，鏈碼會在通道中所有對等組織上的所有對等節點上具現化。  

### <a name="invoke-chaincode"></a>叫用鏈碼  

從對等組織的用戶端，執行下列命令來叫用鏈碼函數：  

```bash
./azhlf chaincode invoke -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <invokeFunc> -a <invokeFuncArgs>  
```

分別在和中傳遞叫用函式名稱和以空格分隔的引數清單  `<invokeFunction>`    `<invokeFuncArgs>`   。 繼續進行 chaincode_example02 的鏈碼範例，以執行叫用作業，並將設  `<invokeFunction>`   為  `invoke`   和  `<invokeFuncArgs>`   `"a" "b" "10"` 。  

>[!NOTE]
> 從通道中的任何一個對等組織執行一次命令。 將交易成功提交至排序者之後，排序者會將此交易散發給通道中的所有對等組織。 然後，會在通道中所有對等組織的所有對等節點上更新世界狀態。  


### <a name="query-chaincode"></a>查詢鏈碼  

執行下列命令以查詢鏈碼：  

```bash
./azhlf chaincode query -o $ORGNAME -p <endorsingPeers> -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <queryFunction> -a <queryFuncArgs> 
```
簽署對等是安裝鏈碼，並呼叫以執行交易的對等。 您必須將設定 `<endorsingPeers>` 為包含來自目前對等組織的對等節點名稱。 列出指定之鏈碼和頻道組合（以空格分隔）的簽署對等。 例如：`-p "peer1" "peer3"`。

如果您使用 *azhlfTool* 來安裝鏈碼，請將任何對等節點名稱做為值傳遞給簽署對等引數。 鏈碼會安裝在該組織的每個對等節點上。 

分別在和中傳遞查詢函式名稱和以空格分隔的引數清單  `<queryFunction>`    `<queryFuncArgs>`   。 再次進行 chaincode_example02。鏈碼為參考，以查詢世界狀態中 "a" 的值，並將設為，並將設定為  `<queryFunction>`    `query`  `<queryArgs>` `"a"` 。  

## <a name="troubleshoot"></a>疑難排解

### <a name="find-deployed-version"></a>尋找部署的版本

執行下列命令來尋找您範本部署的版本。 根據已部署範本的資源群組來設定環境變數。

```bash
SWITCH_TO_AKS_CLUSTER $AKS_CLUSTER_RESOURCE_GROUP $AKS_CLUSTER_NAME $AKS_CLUSTER_SUBSCRIPTION
kubectl describe pod fabric-tools -n tools | grep "Image:" | cut -d ":" -f 3
```

### <a name="patch-previous-version"></a>修補先前的版本

如果您遇到在 v 3.0.0 以下範本版本的任何部署上執行鏈碼的問題，請遵循下列步驟，利用修正程式修補您的對等節點。

下載對等部署腳本。

```bash
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/scripts/patchPeerDeployment.sh -o patchPeerDeployment.sh; chmod 777 patchPeerDeployment.sh
```

使用下列命令來執行腳本，以取代您對等的參數。

```bash
source patchPeerDeployment.sh <peerOrgSubscription> <peerOrgResourceGroup> <peerOrgAKSClusterName>
```

等候所有的對等節點進行修補。 您一律可以使用下列命令，在不同的 shell 實例中檢查對等節點的狀態。

```bash
kubectl get pods -n hlf
```

## <a name="support-and-feedback"></a>支援與意見反應

若要隨時掌握 Azure 區塊鏈工程團隊的最新區塊鏈服務供應專案和資訊，請造訪 [Azure 區塊鏈的 blog](https://azure.microsoft.com/blog/topics/blockchain/)。

若要提供產品意見反應或要求新功能，請透過 [Azure 區塊鏈服務的意見反應論壇](https://aka.ms/blockchainuservoice) \(英文\) 張貼或票選想法。

### <a name="community-support"></a>社群支援

與 Microsoft 工程師和 Azure 區塊鏈群體專家交流：

- [Microsoft Q&頁面](/answers/topics/azure-blockchain-workbench.html) 
   
  區塊鏈範本的工程支援僅限於部署問題。
- [Microsoft 技術小組](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)
