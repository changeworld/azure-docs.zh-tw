---
title: Azure VMware 解決方案（按雲簡單 ） - 為私有雲配置 vSAN 加密
description: 描述如何配置 vSAN 軟體加密功能，以便雲簡單私有雲可以使用在 Azure 虛擬網路中運行的金鑰管理伺服器。
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 638b60bd3612fa25350ecef0a738fea75c2f53d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77020636"
---
# <a name="configure-vsan-encryption-for-cloudsimple-private-cloud"></a>為雲簡單私有雲配置 vSAN 加密

您可以配置 vSAN 軟體加密功能，以便雲簡單私有雲可以使用在 Azure 虛擬網路中運行的金鑰管理伺服器。

VMware 在使用 vSAN 加密時需要使用符合外部 KMIP 1.1 標準的協力廠商金鑰管理伺服器 （KMS） 工具。 您可以利用 VMware 認證且可用於 Azure 的任何受支援的 KMS。

本指南介紹如何使用在 Azure 虛擬網路中運行的 HyTrust 金鑰控制 KMS。 類似的方法可用於任何其他經過認證的協力廠商 KMS 解決方案的 vSAN。

此 KMS 解決方案要求您：

* 在 Azure 虛擬網路中安裝、配置和管理 VMware 認證的協力廠商 KMS 工具。
* 為 KMS 工具提供您自己的許可證。
* 使用在 Azure 虛擬網路中運行的協力廠商 KMS 工具在私有雲中配置和管理 vSAN 加密。

## <a name="kms-deployment-scenario"></a>KMS 部署方案

KMS 伺服器叢集在 Azure 虛擬網路中運行，通過配置的 Azure ExpressRoute 連接從私有雲 vCenter 可進行 IP 可伸路。

![..Azure 虛擬網路中的媒體/KMS 群集](media/vsan-kms-cluster.png)

## <a name="how-to-deploy-the-solution"></a>如何部署解決方案

部署過程具有以下步驟：

1. [驗證是否滿足先決條件](#verify-prerequisites-are-met)
2. [雲簡單門戶：獲取快速路由對等資訊](#cloudsimple-portal-obtain-expressroute-peering-information)
3. [Azure 門戶：將虛擬網路連接到私有雲](#azure-portal-connect-your-virtual-network-to-your-private-cloud)
4. [Azure 門戶：在虛擬網路中部署 HyTrust 金鑰控制群集](#azure-portal-deploy-a-hytrust-keycontrol-cluster-in-the-azure-resource-manager-in-your-virtual-network)
5. [HyTrust WebUI：配置KMIP伺服器](#hytrust-webui-configure-the-kmip-server)
6. [vCenter UI：配置 vSAN 加密以在 Azure 虛擬網路中使用 KMS 群集](#vcenter-ui-configure-vsan-encryption-to-use-kms-cluster-in-your-azure-virtual-network)

### <a name="verify-prerequisites-are-met"></a>滿足驗證先決條件

在部署之前驗證以下內容：

* 所選 KMS 供應商、工具和版本位於 vSAN 相容性清單中。
* 所選供應商支援要在 Azure 中運行的工具的版本。
* KMS 工具的 Azure 版本符合 KMIP 1.1 標準。
* 已創建 Azure 資源管理器和虛擬網路。
* 雲簡單私有雲已創建。

### <a name="cloudsimple-portal-obtain-expressroute-peering-information"></a>雲簡單門戶：獲取快速路由對等資訊

要繼續設置，需要 ExpressRoute 的授權金鑰和對等電路 URI 以及對 Azure 訂閱的訪問。 此資訊可在雲簡單門戶中的虛擬網路連接頁面上找到。 有關說明，請參閱[設置到私有雲的虛擬網路連接](virtual-network-connection.md)。 如果您在獲取資訊時遇到任何問題，請打開[支援請求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

### <a name="azure-portal-connect-your-virtual-network-to-your-private-cloud"></a>Azure 門戶：將虛擬網路連接到私有雲

1. 使用 Azure 門戶按照[ExpressRoute 配置虛擬網路閘道](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)中的說明為虛擬網路創建虛擬網路閘道。
2. 使用門戶，按照[將虛擬網路連接到 ExpressRoute 電路](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)中的說明，將虛擬網路連結到雲簡單快速路由電路。
3. 使用雲簡單歡迎電子郵件中收到的 CloudSimple 快速路由電路資訊將虛擬網路連結到 Azure 中的雲簡單快速路由電路。
4. 輸入授權金鑰和對等電路 URI，為連接指定名稱，然後按一下 **"確定**"。

![創建虛擬網路時提供 CS 等體電路 URI](media/vsan-azureportal01.png) 

### <a name="azure-portal-deploy-a-hytrust-keycontrol-cluster-in-the-azure-resource-manager-in-your-virtual-network"></a>Azure 門戶：在虛擬網路中的 Azure 資源管理器中部署 HyTrust 金鑰控制群集

要在虛擬網路中的 Azure 資源管理器中部署 HyTrust 金鑰控制群集，請執行以下任務。 有關詳細資訊，請參閱[HyTrust 文檔](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0)。

1. 按照 HyTrust 文檔中的說明創建具有指定入站規則的 Azure 網路安全性群組 （nsg-hytrust）。
2. 在 Azure 中生成 SSH 金鑰組。
3. 從 Azure 應用商店中的映射部署初始金鑰控制節點。  使用生成的金鑰組的公開金鑰，選擇**nsg-hytrust**作為金鑰控制節點的網路安全性群組。
4. 將 KeyControl 的私人 IP 位址轉換為靜態 IP 位址。
5. SSH 到金鑰控制 VM 使用其公共 IP 位址和前面提到的金鑰組的私密金鑰。
6. 當 SSH shell 中提示`No`時，選擇將節點設置為初始金鑰控制節點。
7. 通過重複此過程的步驟 3-5 並選擇`Yes`何時提示添加到現有群集，添加其他 KeyControl 節點。

### <a name="hytrust-webui-configure-the-kmip-server"></a>HyTrust WebUI：配置 KMIP 伺服器

轉到公共*ip*HTTPs:// ，其中*公共 ip*是 KeyControl 節點 VM 的公共 IP 位址。 按照[HyTrust 文檔中](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0)的步驟操作。

1. [配置 KMIP 伺服器](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/configuring-kmip-server.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____2)
2. [為 VMware 加密創建證書包](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-user-for-vmcrypt.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____3)

### <a name="vcenter-ui-configure-vsan-encryption-to-use-kms-cluster-in-your-azure-virtual-network"></a>vCenter UI：配置 vSAN 加密以在 Azure 虛擬網路中使用 KMS 群集

按照 HyTrust 說明[在 vCenter 中創建 KMS 群集](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-KMS-Cluster.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____4)。

![在 vCenter 中添加 KMS 群集詳細資訊](media/vsan-config01.png)

在 vCenter 中，轉到**群集>配置**並選擇 vSAN**的"常規**"選項。 啟用加密並選擇以前添加到 vCenter 的 KMS 群集。

![啟用 vSAN 加密並在 vCenter 中配置 KMS 群集](media/vsan-config02.png)

## <a name="references"></a>參考

### <a name="azure"></a>Azure

[使用 Azure 入口網站為 ExpressRoute 設定虛擬網路閘道](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)

[使用入口網站將虛擬網路連線到 ExpressRoute 線路](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)

### <a name="hytrust"></a>海信

[HyTrust 資料控制和微軟 Azure](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0)

[配置 KMPI 伺服器](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/configuring-kmip-server.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____2)

[為 VMware 加密創建證書包](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-user-for-vmcrypt.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____3)

[在 vSphere 中創建 KMS 群集](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-KMS-Cluster.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____4)
