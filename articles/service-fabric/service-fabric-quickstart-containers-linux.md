---
title: 在 Azure 中的 Service Fabric 上建立 Linux 容器應用程式
description: 在此快速入門中，您將使用您的應用程式建置 Docker 映像、將映像推送到容器登錄，然後將您的容器部署至 Service Fabric 叢集。
ms.topic: quickstart
ms.date: 07/22/2019
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: d765168ea2eddaf42715536b5da67f15bb8a8cee
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86258556"
---
# <a name="quickstart-deploy-linux-containers-to-service-fabric"></a>快速入門：將 Linux 容器部署至 Service Fabric

Azure Service Fabric 是一個分散式系統平台，可讓您部署及管理可調整和可信賴的微服務與容器。

本快速入門示範如何將 Linux 容器部署到 Azure 上的 Service Fabric 叢集。 完成後，您會有一個投票應用程式，它是由在 Service Fabric 叢集中執行的 Python Web 前端和 Redis 後端所組成。 此外，您也將了解如何容錯移轉應用程式，以及如何調整叢集中的應用程式。

![投票應用程式網頁][quickstartpic]

## <a name="prerequisites"></a>必要條件

若要完成本快速入門：

1. 如果您沒有訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。

2. 安裝 [Azure CLI](/cli/azure/install-azure-cli-apt?view=azure-cli-latest)

3. 安裝 [Service Fabric SDK 和 CLI](service-fabric-get-started-linux.md#installation-methods)

4. 安裝 [Git](https://git-scm.com/)


## <a name="get-the-application-package"></a>取得應用程式套件

若要將容器部署至 Service Fabric，您需要一組可描述個別容器和應用程式的資訊清單檔案 (應用程式定義)。

在主控台中，使用 git 複製應用程式定義的複本；然後將目錄切換至您複本中的 `Voting` 目錄。

```bash
git clone https://github.com/Azure-Samples/service-fabric-containers.git

cd service-fabric-containers/Linux/container-tutorial/Voting
```

## <a name="create-a-service-fabric-cluster"></a>建立 Service Fabric 叢集

若要將應用程式部署至 Azure，您需要用來執行該應用程式的 Service Fabric 叢集。 下列命令會在 Azure 中建立包含五個節點的叢集。  這些命令也會建立自我簽署的憑證，然後將其新增至金鑰保存庫並在本機下載憑證。 新的憑證用來在部署叢集時保護叢集，而且用來驗證用戶端。

```azurecli
#!/bin/bash

# Variables
ResourceGroupName="containertestcluster" 
ClusterName="containertestcluster" 
Location="eastus" 
Password="q6D7nN%6ck@6" 
Subject="containertestcluster.eastus.cloudapp.azure.com" 
VaultName="containertestvault" 
VmPassword="Mypa$$word!321"
VmUserName="sfadminuser"

# Login to Azure and set the subscription
az login

az account set --subscription <mySubscriptionID>

# Create resource group
az group create --name $ResourceGroupName --location $Location 

# Create secure five node Linux cluster. Creates a key vault in a resource group
# and creates a certficate in the key vault. The certificate's subject name must match 
# the domain that you use to access the Service Fabric cluster.  The certificate is downloaded locally.
az sf cluster create --resource-group $ResourceGroupName --location $Location --certificate-output-folder . --certificate-password $Password --certificate-subject-name $Subject --cluster-name $ClusterName --cluster-size 5 --os UbuntuServer1604 --vault-name $VaultName --vault-resource-group $ResourceGroupName --vm-password $VmPassword --vm-user-name $VmUserName
```

> [!Note]
> Web 前端服務設定為在連接埠 80 上接聽傳入流量。 根據預設，連接埠 80 在您的叢集 VM 和 Azure 負載平衡器上是開啟的。
>

## <a name="configure-your-environment"></a>設定環境

Service Fabric 提供了數項可用來管理叢集及其應用程式的工具：

- Service Fabric Explorer，此為以瀏覽器為基礎的工具。
- Service Fabric 命令列介面 (CLI)，此 CLI 會在 Azure CLI 之上執行。 
- PowerShell 命令。

在此快速入門中，您會使用 Service Fabric CLI 和 Service Fabric Explorer (Web 型工具)。 若要使用 Service Fabric Explorer，您需要將憑證 PFX 檔案匯入瀏覽器。 根據預設，PFX 檔案沒有密碼。

Mozilla Firefox 是 Ubuntu 16.04 中的預設瀏覽器。 若要將憑證匯入 Firefox 中，請按一下瀏覽器右上角的功能表按鈕，然後按一下 [選項]。 在 [喜好設定] 頁面上，使用搜尋方塊搜尋「憑證」。 按一下 [檢視憑證]，選取 [您的憑證] 索引標籤上，按一下 [匯入]，並依照提示匯入憑證。

   ![在 Firefox 上安裝憑證](./media/service-fabric-quickstart-containers-linux/install-cert-firefox.png)

## <a name="deploy-the-service-fabric-application"></a>部署 Service Fabric 應用程式

1. 使用 CLI 連線到 Azure 中的 Service Fabric 叢集。 此端點是叢集的管理端點。 您在上一節中建立了 PEM 檔案。 

    ```bash
    sfctl cluster select --endpoint https://containertestcluster.eastus.cloudapp.azure.com:19080 --pem containertestcluster22019013100.pem --no-verify
    ```

2. 使用安裝指令碼將投票應用程式定義複製到叢集、註冊應用程式類型，以及建立應用程式的執行個體。  PEM 憑證檔案應該位於與 install.sh 檔案相同的目錄。

    ```bash
    ./install.sh
    ```

3. 開啟網頁瀏覽器，並瀏覽至叢集的 Service Fabric Explorer 端點。 端點的格式如下：**https://\<my-azure-service-fabric-cluster-url>:19080/Explorer**；例如，`https://containertestcluster.eastus.cloudapp.azure.com:19080/Explorer`。 </br>

4. 展開 [應用程式] 節點，您可以看到投票應用程式類型和您建立的執行個體現在有一個項目。

    ![Service Fabric Explorer][sfx]

5. 若要連線至執行中的容器，請開啟網頁瀏覽器，並瀏覽至叢集的 URL，例如 `http://containertestcluster.eastus.cloudapp.azure.com:80`。 您應會在瀏覽器中看到投票應用程式。

    ![投票應用程式網頁][quickstartpic]

> [!NOTE]
> 您也可以使用 Docker Compose 來部署 Service Fabric 應用程式。 例如，您可以使用 Docker Compose 搭配下列命令，在叢集上部署及安裝應用程式。
>  ```bash
> sfctl compose create --deployment-name TestApp --file-path ../docker-compose.yml
> ```

## <a name="fail-over-a-container-in-a-cluster"></a>容錯移轉叢集中的容器

Service Fabric 可確保如果發生失敗，容器執行個體會自動移至叢集中的其他節點。 您也可以手動清空容器的節點，並將它們依正常程序移至叢集中的其他節點。 Service Fabric 提供數種方式讓您調整服務。 在下列步驟中，您會使用 Service Fabric Explorer。

若要容錯移轉前端容器，請執行下列步驟：

1. 在您的叢集中開啟 Service Fabric Explorer，例如 `https://containertestcluster.eastus.cloudapp.azure.com:19080/Explorer`。
2. 按一下樹狀檢視中的 **fabric:/Voting/azurevotefront** 節點，然後展開分割區節點 (以 GUID 表示)。 請注意樹狀檢視中的節點名稱，其中顯示哪些節點上的容器目前正在執行，例如 `_nodetype_1`。
3. 展開樹狀檢視中的 [節點] 節點。 按一下正在執行容器之節點旁邊的省略符號 (...)。
4. 選擇 [重新啟動] 以重新啟動節點並確認重新啟動動作。 重新啟動會造成容器容錯移轉至叢集中的其他節點。

    ![Service Fabric Explorer 中的節點檢視][sfxquickstartshownodetype]

## <a name="scale-applications-and-services-in-a-cluster"></a>調整叢集中的應用程式和服務

您可以在整個叢集內輕鬆地調整 Service Fabric 服務，以符合服務的負載。 您可以藉由變更叢集中執行的執行個體數目來調整服務。

若要調整 Web 前端服務，請執行下列步驟：

1. 在您的叢集中開啟 Service Fabric Explorer，例如 `https://containertestcluster.eastus.cloudapp.azure.com:19080`。
2. 按一下樹狀檢視中 **fabric:/Voting/azurevotefront** 節點旁邊的省略符號 (三個點)，然後選擇 [調整服務]。

    ![Service Fabric Explorer 調整服務啟動][containersquickstartscale]

    您現在可以選擇調整 Web 前端服務的執行個體數目。

3. 將數字變更為 **2**，然後按一下 [調整服務]。
4. 按一下樹狀檢視中的 **fabric:/Voting/azurevotefront** 節點，然後展開分割區節點 (以 GUID 表示)。

    ![Service Fabric Explorer 調整服務完成][containersquickstartscaledone]

    您現在可以看到服務有兩個執行個體。 在樹狀檢視中，您可看到執行個體在哪些節點上執行。

藉由這項簡單的管理工作，您已讓前端服務可用來處理使用者負載的資源倍增。 請務必了解，您不需要多個服務執行個體，就能讓服務確實可靠地執行。 如果服務失敗，Service Fabric 可確保會有新的服務執行個體在叢集中執行。

## <a name="clean-up-resources"></a>清除資源

使用範本中提供的解除安裝指令碼 (uninstall.sh)，刪除叢集中的應用程式執行個體並取消註冊應用程式類型。 此指令碼會花一些時間來清除執行個體，因此您不應在此指令碼之後立即執行安裝指令碼。 您可以使用 Service Fabric Explorer 來判斷何時已移除執行個體以及取消註冊應用程式類型。

```bash
./uninstall.sh
```

刪除叢集及其取用之所有資源的最簡單方式，就是刪除資源群組。

登入 Azure 並選取您要移除叢集的訂用帳戶識別碼。 您可以登入 Azure 入口網站以尋找您的訂用帳戶識別碼。 使用 [az group delete](/cli/azure/group?view=azure-cli-latest) 命令來刪除資源群組和所有叢集資源。

```azurecli
az login
az account set --subscription <guid>
ResourceGroupName="containertestcluster"
az group delete --name $ResourceGroupName
```

如果您已完成叢集的處理，可以從憑證存放區中移除憑證。 例如：
- 在 Windows 上：使用[憑證 MMC 嵌入式管理單元](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in)。 在新增嵌入式管理單元時，請務必選取 [我的使用者帳戶]。 瀏覽至 `Certificates - Current User\Personal\Certificates` 並移除憑證。
- 在 Mac 上：使用 Keychain 應用程式。
- 在 Ubuntu 上：依照您用來檢視憑證的步驟，將憑證移除。

## <a name="next-steps"></a>後續步驟

在此快速入門中，您已在 Azure 中將 Linux 容器應用程式部署至 Service Fabric 叢集、對應用程式執行容錯移轉，並調整叢集中的應用程式。 若要深入了解如何在 Service Fabric 中使用 Linux 容器，請繼續進行教學課程以了解 Linux 容器應用程式。

> [!div class="nextstepaction"]
> [建立 Linux 容器應用程式](./service-fabric-tutorial-create-container-images.md)

[sfx]: ./media/service-fabric-quickstart-containers-linux/containersquickstartappinstance.png
[quickstartpic]: ./media/service-fabric-quickstart-containers-linux/votingapp.png
[sfxquickstartshownodetype]:  ./media/service-fabric-quickstart-containers-linux/containersquickstartrestart.png
[containersquickstartscale]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscale.png
[containersquickstartscaledone]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscaledone.png
