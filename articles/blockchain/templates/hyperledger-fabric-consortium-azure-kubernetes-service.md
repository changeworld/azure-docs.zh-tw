---
title: Azure Kubernetes Service （AKS）上的 Hyperledger 網狀架構聯盟
description: 如何在 Azure Kubernetes Service 上部署和設定 Hyperledger Fabric 聯盟網路
ms.date: 07/07/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: e1cbfa56f1e4ea9f8cbaa0ad973d06e8b8d486ca
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86085794"
---
# <a name="hyperledger-fabric-consortium-on-azure-kubernetes-service-aks"></a>Azure Kubernetes Service （AKS）上的 Hyperledger 網狀架構聯盟

您可以使用 Hyperledger Fabric （HLF） on Azure Kubernetes Service （AKS）範本來部署和設定 Azure 上的 Hyperledger Fabric 聯盟網路。

閱讀本文之後，您將能夠：

- 取得 Hyperledger 網狀架構的實用知識，以及構成 Hyperledger Fabric 區塊鏈網路建立區塊的各種元件。
- 瞭解如何在適用于生產案例的 Azure Kubernetes Service 上部署和設定 Hyperledger 網狀架構聯盟。

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="choose-an-azure-blockchain-solution"></a>選擇 Azure 區塊鏈解決方案

選擇使用解決方案範本之前，請先將您的案例與可用 Azure 區塊鏈選項的常見使用案例進行比較。

選項 | 服務模型 | 一般使用案例
-------|---------------|-----------------
解決方案範本 | IaaS | 解決方案範本是 Azure Resource Manager 範本，可用來布建完整設定的區塊鏈網路拓撲。 這些範本會針對指定的區塊鏈網路類型，部署和設定 Microsoft Azure 計算、網路和儲存體服務。 解決方案範本是在沒有服務等級協定的情況下提供。 使用[Microsoft Q&支援的問題頁面](https://docs.microsoft.com/answers/topics/azure-blockchain-workbench.html)。
[Azure 區塊鏈服務](../service/overview.md) | PaaS | Azure 區塊鏈 Service Preview 簡化了聯盟區塊鏈網路的構成、管理和治理。 針對需要 PaaS、聯盟管理或合約和交易隱私權的解決方案，使用 Azure 區塊鏈 Service。
[Azure Blockchain Workbench](../workbench/overview.md) | IaaS 和 PaaS | Azure Blockchain Workbench 預覽是 Azure 服務和功能的集合，目的是要協助您建立及部署區塊鏈應用程式，以與其他組織共用商務程序和資料。 使用 Azure Blockchain Workbench 來建立區塊鏈解決方案的原型或區塊鏈應用程式概念證明。 Azure Blockchain Workbench 目前沒有服務等級合約。 使用[Microsoft Q&支援的問題頁面](https://docs.microsoft.com/answers/topics/azure-blockchain-workbench.html)。

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

- **Azure key vault**：部署金鑰保存庫實例，以儲存客戶所提供的網狀架構 CA 認證和根憑證，以在範本部署重試時用來處理範本的機制。
- **Azure 受控磁片**： azure 受控磁片適用于總帳和對等節點世界狀態資料庫的持續性存放區。
- **公用 ip**：為了與叢集互動而部署之 AKS 叢集的公用 ip 端點。

## <a name="hyperledger-fabric-blockchain-network-setup"></a>Hyperledger 網狀架構區塊鏈網路設定

若要開始，您需要可支援部署數個虛擬機器和標準儲存體帳戶的 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，則可[建立免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

使用下列步驟設定 Hyperledger Fabric 區塊鏈網路：

- [部署排序者/對等組織](#deploy-the-ordererpeer-organization)
- [建立聯盟](#build-the-consortium)

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

若要建立區塊鏈聯盟文章部署訂購服務和對等節點，您需要依序執行下列步驟。 Azure HLF 腳本（azhlf），可協助您設定聯盟、建立通道和鏈碼作業。

> [!NOTE]
> 腳本中有更新，此更新是為了提供更多功能的 Azure HLF 腳本。 如果您想要參考舊的腳本，請[參閱這裡](https://github.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/blob/master/consortiumScripts/README.md)。 此腳本與 Azure Kubernetes Service 範本版本2.0.0 和更新版本上的 Hyperledger 網狀架構相容。 若要檢查部署的版本，請遵循[疑難排解](#troubleshoot)中的步驟。

> [!NOTE]
> 提供的 Azure HLF （azhlf）腳本僅協助示範/DevTest 案例。 此腳本所建立的通道和聯盟具有基本的 HLF 原則，可簡化示範/DevTest 案例。 針對生產環境設定，我們建議您使用原生 HLF Api，依據您的組織合規性需求來更新通道/聯盟 HLF 原則。


所有執行 Azure HLF 腳本的命令都可以透過 Azure Bash 命令列來執行。 介面（CLI）。 您可以透過登入 Azure shell web 版本  ![Azure Kubernetes Service 範本上的 Hyperledger 網狀架構](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) 選項，位於 Azure 入口網站的右上角。 在命令提示字元中，輸入 bash，然後輸入以切換至 bash CLI。

如需詳細資訊，請參閱[Azure shell](https://docs.microsoft.com/azure/cloud-shell/overview) 。

![Azure Kubernetes Service 範本上的 Hyperledger 網狀架構](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


下圖顯示在排序者組織和對等組織之間建立聯盟的逐步流程。 下列各節會說明執行這些步驟的詳細命令。

![Azure Kubernetes Service 範本上的 Hyperledger 網狀架構](./media/hyperledger-fabric-consortium-azure-kubernetes-service/process-to-build-consortium-flow-chart.png)

針對用戶端應用程式的初始設定，請遵循下列命令： 

1.  [下載用戶端應用程式檔](#download-client-application-files)
2.  [設定環境變數](#setup-environment-variables)
3.  [匯入組織連線設定檔、系統管理員使用者和 MSP](#import-organization-connection-profile-admin-user-identity-and-msp)

完成初始設定之後，您可以使用用戶端應用程式來達成下列作業：  

- [通道管理命令](#channel-management-commands)
- [聯盟管理命令](#consortium-management-commands)
- [鏈碼管理命令](#chaincode-management-commands)

### <a name="download-client-application-files"></a>下載用戶端應用程式檔

第一個設定是下載用戶端應用程式檔。 執行下列命令以下載所有必要的檔案和套件：

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/azhlfToolSetup.sh | bash
cd azhlfTool
npm install
npm run setup

```
這些命令會從公用 GitHub 存放庫複製 Azure HLF 用戶端應用程式程式碼，然後再載入所有相依的 npm 套件。 成功執行命令之後，您可以在目前目錄中看到 node_modules 資料夾。 所有必要的套件都會載入 node_modules 資料夾中。


### <a name="setup-environment-variables"></a>設定環境變數

> [!NOTE]
> 所有環境變數都遵循 Azure 資源命名慣例。


**為排序者組織用戶端設定下列環境變數**


```bash
ORDERER_ORG_SUBSCRIPTION=<ordererOrgSubscription>
ORDERER_ORG_RESOURCE_GROUP=<ordererOrgResourceGroup>
ORDERER_ORG_NAME=<ordererOrgName>
ORDERER_ADMIN_IDENTITY="admin.$ORDERER_ORG_NAME"
CHANNEL_NAME=<channelName>
```
**針對對等組織用戶端設定下列環境變數**

```bash
PEER_ORG_SUBSCRIPTION=<peerOrgSubscritpion>
PEER_ORG_RESOURCE_GROUP=<peerOrgResourceGroup>
PEER_ORG_NAME=<peerOrgName>
PEER_ADMIN_IDENTITY="admin.$PEER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

> [!NOTE]
> 根據聯盟中的對等組織數目，您可能需要重複執行對等命令，並據以設定環境變數。

**設定下列環境變數，以設定 Azure 儲存體帳戶**

```bash
STORAGE_SUBSCRIPTION=<subscriptionId>
STORAGE_RESOURCE_GROUP=<azureFileShareResourceGroup>
STORAGE_ACCOUNT=<azureStorageAccountName>
STORAGE_LOCATION=<azureStorageAccountLocation>
STORAGE_FILE_SHARE=<azureFileShareName>
```

請遵循下列步驟來建立 Azure 儲存體帳戶。 如果您已經建立 Azure 儲存體帳戶，請略過下列步驟

```bash
az account set --subscription $STORAGE_SUBSCRIPTION
az group create -l $STORAGE_LOCATION -n $STORAGE_RESOURCE_GROUP
az storage account create -n $STORAGE_ACCOUNT -g  $STORAGE_RESOURCE_GROUP -l $STORAGE_LOCATION --sku Standard_LRS
```

請遵循下列步驟，在 Azure 儲存體帳戶中建立檔案共用。 如果您已經建立檔案共用，請略過下列步驟

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
az storage share create  --account-name $STORAGE_ACCOUNT  --account-key $STORAGE_KEY  --name $STORAGE_FILE_SHARE
```

請遵循下列步驟來產生 Azure 檔案共用連接字串

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
SAS_TOKEN=$(az storage account generate-sas --account-key $STORAGE_KEY --account-name $STORAGE_ACCOUNT --expiry `date -u -d "1 day" '+%Y-%m-%dT%H:%MZ'` --https-only --permissions lruwd --resource-types sco --services f | tr -d '"')
AZURE_FILE_CONNECTION_STRING=https://$STORAGE_ACCOUNT.file.core.windows.net/$STORAGE_FILE_SHARE?$SAS_TOKEN

```

### <a name="import-organization-connection-profile-admin-user-identity-and-msp"></a>匯入組織連線設定檔、系統管理員使用者身分識別和 MSP

請發出下列命令，以從 Azure Kubernetes Cluster 提取組織的連線設定檔、系統管理員使用者身分識別和 MSP，並將這些身分識別儲存在用戶端應用程式本機存放區中，亦即 "azhlfTool/store" 目錄。

針對排序者組織：

```bash
./azhlf adminProfile import fromAzure -o $ORDERER_ORG_NAME -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION
./azhlf connectionProfile import fromAzure -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION -o $ORDERER_ORG_NAME   
./azhlf msp import fromAzure -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION -o $ORDERER_ORG_NAME
```

針對對等組織：

```bash
./azhlf adminProfile import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
./azhlf connectionProfile import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
./azhlf msp import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
```

### <a name="channel-management-commands"></a>通道管理命令

> [!NOTE]
> 開始進行任何通道作業之前，請確定已完成用戶端應用程式的初始設定。  

以下是兩個通道管理命令：

1. [建立通道命令](#create-channel-command)
2. [設定錨點對等命令](#setting-anchor-peers-command)


#### <a name="create-channel-command"></a>建立通道命令

從排序者組織用戶端，發出命令以建立新的通道。 此命令將會建立一個通道，其中僅包含排序者組織。  

```bash
./azhlf channel create -c $CHANNEL_NAME -u $ORDERER_ADMIN_IDENTITY -o $ORDERER_ORG_NAME
```

#### <a name="setting-anchor-peers-command"></a>設定錨點對等命令
從對等組織用戶端，發出下列命令，以針對指定通道上的對等組織設定錨點對等。

>[!NOTE]
> 執行此命令之前，請確定已使用聯盟管理命令在通道中新增對等組織。

```bash
./azhlf channel setAnchorPeers -c $CHANNEL_NAME -p <anchorPeersList> -o $PEER_ORG_NAME -u $PEER_ADMIN_IDENTITY
```

`<anchorPeersList>`這是要設定為錨點對等節點的空格分隔清單。 例如，

  - `<anchorPeersList>`如果您只想要將 peer1 節點設定為錨點對等，請設定為 "peer1"。
  - `<anchorPeersList>`如果您想要將 peer1 和 peer3 節點設定為錨點對等，請設定為 "peer1" "peer3"。

### <a name="consortium-management-commands"></a>聯盟管理命令

>[!NOTE]
> 開始進行任何聯盟作業之前，請確定已完成用戶端應用程式的初始設定。  

在指定的順序中執行下列命令，以在通道和聯盟中新增對等組織
1.  從對等組織用戶端上傳 Azure 儲存體上的對等組織 MSP

      ```bash
      ./azhlf msp export toAzureStorage -f  $AZURE_FILE_CONNECTION_STRING -o $PEER_ORG_NAME
      ```
2.  從排序者組織用戶端，從 Azure 儲存體下載對等組織的 MSP，然後發出命令，以在通道/聯盟中新增對等組織。

      ```bash
      ./azhlf msp import fromAzureStorage -o $PEER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel join -c  $CHANNEL_NAME -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ./azhlf consortium join -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ```

3.  從排序者組織用戶端上傳 Azure 儲存體上的排序者連線設定檔，讓對等組織可以使用此連線設定檔連線到排序者節點

      ```bash
      ./azhlf connectionProfile  export toAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ```

4.  從對等組織用戶端，從 Azure 儲存體下載排序者連線設定檔，然後發出命令以在通道中新增對等節點

      ```bash
      ./azhlf connectionProfile  import fromAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel joinPeerNodes -o $PEER_ORG_NAME  -u $PEER_ADMIN_IDENTITY -c $CHANNEL_NAME --ordererOrg $ORDERER_ORG_NAME
      ```

同樣地，若要在通道中新增更多對等組織，請根據所需的對等組織更新對等環境變數，並執行步驟1到4。


### <a name="chaincode-management-commands"></a>鏈碼管理命令

>[!NOTE]
> 開始進行任何鏈碼作業之前，請確定已完成用戶端應用程式的初始設定。  

**設定下列鏈碼特定環境變數**

```bash
# peer organization name where chaincode operation is to be performed
ORGNAME=<PeerOrgName>
USER_IDENTITY="admin.$ORGNAME"  
# If you are using chaincode_example02 then set CC_NAME=“chaincode_example02”
CC_NAME=<chaincodeName>  
# If you are using chaincode_example02 then set CC_VERSION=“1” for validation
CC_VERSION=<chaincodeVersion>
# Language in which chaincode is written. Supported languages are 'node', 'golang' and 'java'  
# Default value is 'golang'  
CC_LANG=<chaincodeLanguage>  
# CC_PATH contains the path where your chaincode is place.
# If you are using chaincode_example02 to validate then CC_PATH=“/home/<username>/azhlfTool/chaincode/src/chaincode_example02/go”
CC_PATH=<chaincodePath>  
# Channel on which chaincode is to be instantiated/invoked/queried  
CHANNEL_NAME=<channelName>  
```

以下是可執行檔鏈碼作業：  

- [安裝鏈碼](#install-chaincode)  
- [具現化鏈碼](#instantiate-chaincode)  
- [叫用鏈碼](#invoke-chaincode)
- [查詢鏈碼](#query-chaincode)


### <a name="install-chaincode"></a>安裝鏈碼  

執行下列命令以在對等組織上安裝鏈碼。  

```bash
./azhlf chaincode install -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION  

```
它會在 ORGNAME 環境變數中設定的對等組織的所有對等節點上安裝鏈碼。 如果您的通道中有兩個或多個對等組織，而您想要在所有的節點上安裝鏈碼，請針對每個對等組織分別執行此命令。  

請遵循下列步驟：  

1.  將 `ORGNAME` 和設定 `USER_IDENTITY` 為每個 peerOrg1 和 issue `./azhlf chaincode install` 命令。  
2.  將 `ORGNAME` 和設定 `USER_IDENTITY` 為每個 peerOrg2 和 issue `./azhlf chaincode install` 命令。  

### <a name="instantiate-chaincode"></a>具現化鏈碼  

從對等用戶端應用程式，執行下列命令以在通道上具現化鏈碼。  

```bash
./azhlf chaincode instantiate -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -v $CC_VERSION -l $CC_LANG -c $CHANNEL_NAME -f <instantiateFunc> --args <instantiateFuncArgs>  
```
將具現化函數名稱和以空格分隔的引數清單 `<instantiateFunc>` 分別傳入和 `<instantiateFuncArgs>` 。 例如，在 chaincode_example02. go 鏈碼中，將鏈碼設定為，並將其具現化為 " `<instantiateFunc>` `init` `<instantiateFuncArgs>` a" "2000" "b" "1000"。

> [!NOTE]
> 從通道中的任何一個對等組織執行一次命令。 交易成功提交至排序者後，排序者會將此交易散發給通道中的所有對等組織。 因此，鏈碼會在通道中的所有對等組織的所有對等節點上具現化。  


### <a name="invoke-chaincode"></a>叫用鏈碼  

從對等組織用戶端，執行下列命令來叫用鏈碼函數：  

```bash
./azhlf chaincode invoke -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <invokeFunc> -a <invokeFuncArgs>  
```

分別傳遞 invoke 函式名稱和以空格分隔的引數清單  `<invokeFunction>`    `<invokeFuncArgs>`   。 繼續進行 chaincode_example02。 go 鏈碼範例，以執行將設定為的叫用作業，並將其設為 "  `<invokeFunction>`    `invoke`    `<invokeFuncArgs>`   a" "b" "10"。  

>[!NOTE]
> 從通道中的任何一個對等組織執行一次命令。 交易成功提交至排序者後，排序者會將此交易散發給通道中的所有對等組織。 因此，在通道中所有對等組織的所有對等節點上都會更新世界狀態。  


### <a name="query-chaincode"></a>查詢鏈碼  

執行下列命令以查詢鏈碼：  

```bash
./azhlf chaincode query -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <queryFunction> -a <queryFuncArgs>  
```
分別在和中傳遞查詢函式名稱和以空格分隔的引數清單  `<queryFunction>`    `<queryFuncArgs>`   。 同樣地，將 chaincode_example02 做為參考，以查詢全球狀態中 "a" 的值，並設定  `<queryFunction>`   為  `query`  `<queryArgs>` "a"。  

## <a name="troubleshoot"></a>疑難排解

**確認正在執行的範本版本**

執行下列命令以尋找範本部署的版本。

根據已部署範本的資源群組，設定下列環境變數。

```bash

SWITCH_TO_AKS_CLUSTER() { az aks get-credentials --resource-group $1 --name $2 --subscription $3; }
AKS_CLUSTER_SUBSCRIPTION=<AKSClusterSubscriptionID>
AKS_CLUSTER_RESOURCE_GROUP=<AKSClusterResourceGroup>
AKS_CLUSTER_NAME=<AKSClusterName>
```
執行下列命令以列印範本版本
```bash
SWITCH_TO_AKS_CLUSTER $AKS_CLUSTER_RESOURCE_GROUP $AKS_CLUSTER_NAME $AKS_CLUSTER_SUBSCRIPTION
kubectl describe pod fabric-tools -n tools | grep "Image:" | cut -d ":" -f 3

```

## <a name="support-and-feedback"></a>支援與意見反應

如需 Azure 區塊鏈服務消息，請瀏覽 [Azure 區塊鏈服務部落格](https://azure.microsoft.com/blog/topics/blockchain/)，即時掌握 Azure 區塊鏈服務工程小組提供的區塊鏈服務供應項目和最新資訊。

若要提供產品意見反應或要求新功能，請透過 [Azure 區塊鏈服務的意見反應論壇](https://aka.ms/blockchainuservoice) \(英文\) 張貼或票選想法。

### <a name="community-support"></a>社群支援

洽詢 Microsoft 工程師和 Azure 區塊鏈服務社群專家。

- [Microsoft Q&問題頁面](https://docs.microsoft.com/answers/topics/azure-blockchain-workbench.html)。 區塊鏈範本的工程支援僅限於部署問題。
- [Microsoft 技術社群](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)