---
title: Azure VMware Solution by CloudSimple-設定私人雲端的 vSAN 加密
description: 說明如何設定 vSAN 軟體加密功能，讓您的 CloudSimple 私人雲端可以與在 Azure 虛擬網路中執行的金鑰管理伺服器搭配運作。
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 638b60bd3612fa25350ecef0a738fea75c2f53d3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77020636"
---
# <a name="configure-vsan-encryption-for-cloudsimple-private-cloud"></a>設定 CloudSimple 私用雲端的 vSAN 加密

您可以設定 vSAN 軟體加密功能，讓您的 CloudSimple 私人雲端可以與在 Azure 虛擬網路中執行的金鑰管理伺服器搭配運作。

使用 vSAN 加密時，VMware 需要使用外部 KMIP 1.1 相容的協力廠商金鑰管理伺服器（KMS）工具。 您可以利用 VMware 認證且適用于 Azure 的任何受支援 KMS。

本指南說明如何使用在 Azure 虛擬網路中執行的 HyTrust KeyControl KMS。 適用于 vSAN 的任何其他認證的協力廠商 KMS 解決方案都可以使用類似的方法。

此 KMS 解決方案需要您：

* 在您的 Azure 虛擬網路中安裝、設定及管理 VMware 認證的協力廠商 KMS 工具。
* 提供您自己的 KMS 工具授權。
* 使用在 Azure 虛擬網路中執行的協力廠商 KMS 工具，在私人雲端中設定和管理 vSAN 加密。

## <a name="kms-deployment-scenario"></a>KMS 部署案例

KMS 伺服器叢集會在您的 Azure 虛擬網路中執行，並可透過已設定的 Azure ExpressRoute 連線從私人雲端 vCenter 連線到 IP。

![..Azure 虛擬網路中的/media/KMS 叢集](media/vsan-kms-cluster.png)

## <a name="how-to-deploy-the-solution"></a>如何部署解決方案

部署程式具有下列步驟：

1. [確認符合必要條件](#verify-prerequisites-are-met)
2. [CloudSimple 入口網站：取得 ExpressRoute 對等互連資訊](#cloudsimple-portal-obtain-expressroute-peering-information)
3. [Azure 入口網站：將您的虛擬網路連線到私人雲端](#azure-portal-connect-your-virtual-network-to-your-private-cloud)
4. [Azure 入口網站：在您的虛擬網路中部署 HyTrust KeyControl 叢集](#azure-portal-deploy-a-hytrust-keycontrol-cluster-in-the-azure-resource-manager-in-your-virtual-network)
5. [HyTrust WebUI：設定 KMIP 伺服器](#hytrust-webui-configure-the-kmip-server)
6. [vCenter UI：將 vSAN 加密設定為在您的 Azure 虛擬網路中使用 KMS 叢集](#vcenter-ui-configure-vsan-encryption-to-use-kms-cluster-in-your-azure-virtual-network)

### <a name="verify-prerequisites-are-met"></a>確認符合必要條件

在部署之前，請確認下列事項：

* 選取的 KMS 廠商、工具和版本位於 vSAN 相容性清單上。
* 選取的廠商支援在 Azure 中執行的工具版本。
* Azure 版本的 KMS 工具與 KMIP 1.1 相容。
* 已建立 Azure Resource Manager 和虛擬網路。
* 已建立 CloudSimple 私用雲端。

### <a name="cloudsimple-portal-obtain-expressroute-peering-information"></a>CloudSimple 入口網站：取得 ExpressRoute 對等互連資訊

若要繼續進行設定，您需要 ExpressRoute 的授權金鑰和對等線路 URI，再加上 Azure 訂用帳戶的存取權。 這項資訊可在 CloudSimple 入口網站的 [虛擬網路連線] 頁面上取得。 如需指示，請參閱[設定與私人雲端的虛擬網路](virtual-network-connection.md)連線。 如果您在取得資訊時遇到任何問題，請開啟[支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

### <a name="azure-portal-connect-your-virtual-network-to-your-private-cloud"></a>Azure 入口網站：將您的虛擬網路連線到私人雲端

1. 遵循[使用 Azure 入口網站設定 ExpressRoute 的虛擬網路閘道](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)中的指示，為您的虛擬網路建立虛擬網路閘道。
2. 遵循[使用入口網站將虛擬網路連線到 expressroute 電路](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)中的指示，將您的虛擬網路連結至 CloudSimple ExpressRoute 線路。
3. 使用來自 CloudSimple 的歡迎電子郵件中所收到的 CloudSimple ExpressRoute 線路資訊，將您的虛擬網路連結至 Azure 中的 CloudSimple ExpressRoute 線路。
4. 輸入 [授權金鑰] 和 [對等線路 URI]，並指定連線的名稱，然後按一下 **[確定]**。

![建立虛擬網路時提供 CS 對等線路 URI](media/vsan-azureportal01.png) 

### <a name="azure-portal-deploy-a-hytrust-keycontrol-cluster-in-the-azure-resource-manager-in-your-virtual-network"></a>Azure 入口網站：在虛擬網路的 Azure Resource Manager 中部署 HyTrust KeyControl 叢集

若要在虛擬網路的 Azure Resource Manager 中部署 HyTrust KeyControl 叢集，請執行下列工作。 如需詳細資訊，請參閱[HyTrust 檔](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0)。

1. 遵循 HyTrust 檔中的指示，建立具有指定輸入規則的 Azure 網路安全性群組（nsg-hytrust）。
2. 在 Azure 中產生 SSH 金鑰組。
3. 從 Azure Marketplace 中的映射部署初始 KeyControl 節點。  使用所產生金鑰組的公開金鑰，然後選取 [ **nsg-hytrust** ] 作為 [KeyControl] 節點的 [網路安全性群組]。
4. 將 KeyControl 的私人 IP 位址轉換為靜態 IP 位址。
5. 透過 SSH 連線到 KeyControl VM，其使用其公用 IP 位址和先前提及之金鑰組的私密金鑰。
6. 在 SSH 命令介面中出現提示時`No` ，選取將節點設定為初始 KeyControl 節點。
7. 重複此程式的步驟3-5，並在出現新增至現有`Yes`叢集的提示時選取，以新增其他 KeyControl 節點。

### <a name="hytrust-webui-configure-the-kmip-server"></a>HyTrust WebUI：設定 KMIP 伺服器

移至 [HTTPs://] [*公用-ip*]，其中 [*公用 Ip* ] 是 KeyControl 節點 VM 的公用 ip 位址。 請遵循[HyTrust 檔](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0)中的下列步驟。

1. [設定 KMIP 伺服器](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/configuring-kmip-server.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____2)
2. [建立 VMware 加密的憑證配套](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-user-for-vmcrypt.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____3)

### <a name="vcenter-ui-configure-vsan-encryption-to-use-kms-cluster-in-your-azure-virtual-network"></a>vCenter UI：將 vSAN 加密設定為在您的 Azure 虛擬網路中使用 KMS 叢集

遵循 HyTrust 指示，[在 vCenter 中建立 KMS](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-KMS-Cluster.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____4)叢集。

![在 vCenter 中新增 KMS 叢集詳細資料](media/vsan-config01.png)

在 vCenter 中，移至 [叢集] **> [設定**]，然後選取 **[一般**] 選項作為 vSAN。 啟用加密，並選取先前已新增至 vCenter 的 KMS 叢集。

![啟用 vSAN 加密並在 vCenter 中設定 KMS 叢集](media/vsan-config02.png)

## <a name="references"></a>參考

### <a name="azure"></a>Azure

[使用 Azure 入口網站為 ExpressRoute 設定虛擬網路閘道](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)

[使用入口網站將虛擬網路連線到 ExpressRoute 線路](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)

### <a name="hytrust"></a>HyTrust

[HyTrust DataControl 和 Microsoft Azure](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0)

[設定 KMPI 伺服器](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/configuring-kmip-server.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____2)

[建立 VMware 加密的憑證配套](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-user-for-vmcrypt.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____3)

[在 vSphere 中建立 KMS 叢集](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-KMS-Cluster.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____4)
