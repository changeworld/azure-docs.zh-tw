---
title: 虛擬網路中的 Azure 春季 Cloud access 應用程式
description: 在虛擬網路中的 Azure 春季雲端存取應用程式。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/11/2020
ms.custom: devx-track-java
ms.openlocfilehash: b6e523fc4a3dcd0eb8c8ed7a44b3d7d9fcdd7d34
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98887179"
---
# <a name="access-your-application-in-a-private-network"></a>在私人網路中存取您的應用程式

本檔說明如何在私人網路中存取您應用程式的端點。  為了取得存取權，您必須在訂用帳戶中建立 **Azure 私人 DNS 區域** ，以轉譯/解析私人完整功能變數名稱 (FQDN) 至其 IP 位址。

當您在虛擬網路中部署 Azure 春季雲端服務實例中的應用程式 **端點** 時，端點會是私人 FQDN。 只能在私人網路中存取網域。 應用程式和服務會使用應用程式端點。 其中包括 [驗證預備部署](spring-cloud-howto-staging-environment.md#verify-the-staging-deployment)中所述的 **測試端點**。 [串流 Azure 中](spring-cloud-howto-log-streaming.md)所述的 **記錄串流**（即時），也適用于私人網路內。

## <a name="create-a-private-dns-zone"></a>建立私人 DNS 區域

下列程式會針對私人網路中的應用程式建立私人 DNS 區域。

1. 開啟 Azure 入口網站。 從頂端的搜尋方塊中，搜尋 **私人 dns 區域**，然後從結果中選取 **私人 dns 區域** 。

2. 在 [ **私人 DNS 區域** ] 頁面上，選取 [ **+ 新增**]。

3. 填寫 [ **建立私人 DNS 區域** ] 頁面上的表單。 輸入 **<span>private.azuremicroservices.io</span>** 做為區域的 **名稱** 。

4. 選取 [檢閱 + 建立]  。

5. 選取 [建立]  。

建立區域可能需要幾分鐘的時間。

## <a name="link-the-virtual-network"></a>連結虛擬網路

若要將私人 DNS 區域連結至虛擬網路，您必須建立虛擬網路連結。

1. 選取上述建立的私人 DNS 區域資源： **<span>private.azuremicroservices.io</span>** 

2. 在左窗格中，選取 [虛擬網路連結]。

3. 選取 [新增]。

4. 針對 **連結名稱**，請輸入 **azure-**

5. 針對 [ **虛擬網路**]，選取您所建立的虛擬網路，如在 [azure 虛擬網路中部署 azure 春天雲端 (VNet 插入)](spring-cloud-tutorial-deploy-in-azure-virtual-network.md)中所述。

    ![新增虛擬網路連結](media/spring-cloud-access-app-vnet/add-virtual-network-link.png)

6. 按一下 [確定]  。

## <a name="create-dns-record"></a>建立 DNS 記錄

若要使用私人 DNS 區域來轉譯/解析 DNS，您必須在區域中建立「A」類型記錄。

1. 選取您所建立的虛擬網路資源，如在 [azure 虛擬網路中部署 Azure 春天雲端 (VNet 插入) ](spring-cloud-tutorial-deploy-in-azure-virtual-network.md)中所述。

2. 在 [ **已連線的裝置** ] 搜尋方塊中，輸入 *kubernetes-internal*。

3. 在篩選的結果中，尋找連接到服務實例之服務執行時間 **子網** 的 **裝置**，並複製其 **IP 位址**。 在此範例中，IP 位址是 *10.1.0.7*。

    [![建立 DNS 記錄 ](media/spring-cloud-access-app-vnet/create-dns-record.png)](media/spring-cloud-access-app-vnet/create-dns-record.png)

或者，您可以使用下列 az CLI 命令來提取 IP：

```azurecli
SPRING_CLOUD_RG= # Resource group name of your Azure Spring Cloud service instance
SPRING_CLOUD= # Name of your Azure Spring Cloud service instance

SERVICE_RUNTIME_RG=`az spring-cloud show -g $SPRING_CLOUD_RG -n $SPRING_CLOUD --query \
"properties.networkProfile.serviceRuntimeNetworkResourceGroup" -o tsv`

IP_ADDRESS=`az network lb frontend-ip list --lb-name kubernetes-internal -g \
$SERVICE_RUNTIME_RG --query "[0].privateIpAddress" -o tsv`
```

4. 選取上述建立的私人 DNS 區域資源： **<span>private.azuremicroservices.io</span>**。

5. 選取 [+ 記錄集]。

6. 在 [ **新增記錄集**] 中，輸入或選取這項資訊：

    |設定     |值                                                                      |
    |------------|---------------------------------------------------------------------------|
    |名稱        |輸入 *\**                                                                 |
    |類型        |選取                                                               |
    |TTL         |輸入 *1*                                                                  |
    |TTL 單位    |選取時 **數**                                                           |
    |IP 位址  |輸入在步驟3中複製的 IP 位址。 在範例中，輸入 *10.1.0.7*。    |

    然後選取 [確定]  。

    ![新增私人 DNS 區域記錄](media/spring-cloud-access-app-vnet/private-dns-zone-add-record.png)

## <a name="assign-private-fqdn-for-your-application"></a>為您的應用程式指派私人 FQDN

遵循 [組建和部署微服務應用程式](spring-cloud-tutorial-deploy-in-azure-virtual-network.md)中的程式之後，您可以為您的應用程式指派私人 FQDN。

1. 選取部署在虛擬網路中的 Azure 春季雲端服務實例，然後開啟左側功能表中的 [ **應用程式** ] 索引標籤。

2. 選取應用程式以顯示 [ **總覽** ] 頁面。

3. 選取 [ **指派端點** ] 以將私人 FQDN 指派給您的應用程式。 這可能需要數分鐘的時間。

    ![指派私人端點](media/spring-cloud-access-app-vnet/assign-private-endpoint.png)

4. 指派的私人 FQDN (標示的 **URL**) 現已可供使用。 只能在私人網路記憶體取，但無法在網際網路上存取。

## <a name="access-application-private-fqdn"></a>存取應用程式私用 FQDN

指派之後，您就可以在私人網路中存取應用程式的私人 FQDN。 例如，您可以在相同的虛擬網路或對等互連的虛擬網路中建立 jumpbox 電腦，並在該 jumpbox 電腦上存取私人 FQDN。

![存取 vnet 中的私人端點](media/spring-cloud-access-app-vnet/access-private-endpoint.png)

## <a name="next-steps"></a>後續步驟

- [使用應用程式閘道和 Azure 防火牆將應用程式公開至網際網路](spring-cloud-expose-apps-gateway-azure-firewall.md)

## <a name="see-also"></a>另請參閱

- [針對 VNET 中的 Azure Spring Cloud 進行疑難排解](spring-cloud-troubleshooting-vnet.md)
- [客戶在 VNET 中執行 Azure Spring Cloud 的責任](spring-cloud-vnet-customer-responsibilities.md)