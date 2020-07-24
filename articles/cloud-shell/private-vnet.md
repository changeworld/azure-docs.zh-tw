---
title: Azure 虛擬網路中的 Cloud Shell
description: 將 Cloud Shell 部署到 Azure 虛擬網路
services: Azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/15/2020
ms.author: damaerte
ms.openlocfilehash: 5abc10d149cdb2c054f4122cd805ec22b2e37e2f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87096225"
---
# <a name="deploy-cloud-shell-into-an-azure-virtual-network"></a>將 Cloud Shell 部署到 Azure 虛擬網路
> [!NOTE]
> 這項功能目前處於公開預覽階段。

一般 Cloud Shell 會話會在 Microsoft 網路中的容器中執行，並與您的資源分開。 這表示在容器內執行的命令無法存取只能從特定虛擬網路存取的資源。 例如，您無法使用 SSH 從 Cloud Shell 連線到只有私人 IP 位址的虛擬機器，或使用 kubectl 連線到已鎖定存取權的 Kubernetes 叢集。 

這項選擇性功能可解決這些限制，並可讓您將 Cloud Shell 部署至您所控制的 Azure 虛擬網路。 從該處，容器能夠與您所選取之虛擬網路內的資源互動。  

您可以在下方看到將在此案例中部署和使用的資源架構。

![說明 Cloud Shell 隔離的 VNET 架構。](media/private-vnet/data-diagram.png)

您必須先建立數個資源來支援這項功能，才能在自己的 Azure 虛擬網路中使用 Cloud Shell。 本文說明如何使用 ARM 範本來設定所需的資源。

> [!NOTE]
> 這些資源只需要針對虛擬網路設定一次。 然後，所有具有虛擬網路存取權的系統管理員都可以共用它們。

## <a name="required-network-resources"></a>必要的網路資源

### <a name="virtual-network"></a>虛擬網路
虛擬網路會定義一或多個子網建立所在的位址空間。

需要識別 Cloud Shell 所需的虛擬網路。 這通常會是現有的虛擬網路，其中包含您想要管理的資源，或與包含資源的網路對等互連的網路。

### <a name="subnet"></a>子網路
在選取的虛擬網路中，必須將專用的子網用於 Cloud Shell 容器。 此子網會委派給 Azure 容器實例（ACI）服務。  當使用者要求虛擬網路中的 Cloud Shell 容器時，Cloud Shell 會使用 ACI 來建立此委派子網中的容器。  無法在此子網中建立其他資源。

### <a name="network-profile"></a>網路設定檔
網路設定檔是一種 Azure 資源的網路設定範本，可指定資源的特定網路屬性。

### <a name="azure-relay"></a>Azure 轉送
[Azure 轉送](https://docs.microsoft.com/azure/azure-relay/relay-what-is-it)允許無法直接存取的兩個端點進行通訊。 在此情況下，它是用來允許系統管理員的瀏覽器與私人網路中的容器進行通訊。

您可以設定用於 Cloud Shell 的 Azure 轉送實例，以控制哪些網路可以存取容器資源： 
- 可從公用網際網路存取：在此設定中，Cloud Shell 可讓您從外部連接到其他內部資源。 
- 可從指定的網路存取：在此設定中，系統管理員必須從適當網路中執行的電腦存取 Azure 入口網站，才能使用 Cloud Shell。

## <a name="storage-requirements"></a>儲存體需求
如同標準 Cloud Shell，在虛擬網路中使用 Cloud Shell 時，需要儲存體帳戶。 每位系統管理員都需要檔案共用來儲存其檔案。  儲存體帳戶必須可從 Cloud Shell 所使用的虛擬網路存取。 

## <a name="virtual-network-deployment-limitations"></a>虛擬網路部署限制
* 由於涉及額外的網路資源，啟動虛擬網路中的 Cloud Shell 通常會比標準 Cloud Shell 會話慢。

* 在預覽期間，虛擬網路中 Cloud Shell 支援的區域較少。 這目前僅限於： WestUS 和 WestCentralUS。

* [Azure 轉送](https://docs.microsoft.com/azure/azure-relay/relay-what-is-it)不是免費服務，請參閱其[定價](https://azure.microsoft.com/pricing/details/service-bus/)。 在 Cloud Shell 案例中，會在每個系統管理員使用 Cloud Shell 時使用一個混合式連接。 Cloud Shell 會話完成後，連接將會自動關閉。

## <a name="register-the-resource-provider"></a>註冊資源提供者

ContainerInstances 資源提供者必須在保留您要使用之虛擬網路的訂用帳戶中註冊。 選取適當的訂用帳戶 `Set-AzContext -Subscription {subscriptionName}` ，然後執行：

```powershell
PS> Get-AzResourceProvider -ProviderNamespace Microsoft.ContainerInstance | select ResourceTypes,RegistrationState

ResourceTypes                             RegistrationState
-------------                             -----------------
{containerGroups}                         Registered
...
```

如果**RegistrationState**為 `Registered` ，則不需要採取任何動作。 如果是 `NotRegistered` ，請執行 `Register-AzResourceProvider -ProviderNamespace Microsoft.ContainerInstance` 。 

## <a name="deploy-network-resources"></a>部署網路資源
 
### <a name="create-a-resource-group-and-virtual-network"></a>建立資源群組和虛擬網路
如果您已經有想要連接的所需 VNET，請略過本節。

在 Azure 入口網站中，或使用 Azure CLI、Azure PowerShell 等等，請在新的資源群組中建立資源群組和虛擬網路，**資源群組和虛擬網路必須位於相同區域中**。

> [!NOTE]
> 在公開預覽時，資源群組和虛擬網路必須位於 WestCentralUS 或 WestUS 中。

### <a name="arm-templates"></a>ARM 範本
利用[Azure 快速入門範本](https://aka.ms/cloudshell/docs/vnet/template)來建立虛擬網路中 Cloud Shell 資源，以及使用[Azure 快速入門範本](https://aka.ms/cloudshell/docs/vnet/template/storage)來建立必要的儲存體。 記下您的資源名稱，主要是您的檔案共用名稱。

### <a name="open-relay-firewall"></a>開啟轉送防火牆
流覽至使用上述範本所建立的轉送，選取 [設定] 中的 [網路]，允許從瀏覽器網路存取轉送。 根據預設，只能從已建立的虛擬網路存取轉送。 

### <a name="configuring-cloud-shell-to-use-a-virtual-network"></a>設定 Cloud Shell 以使用虛擬網路。
> [!NOTE]
> 每個系統管理員都必須完成此步驟，才能使用 Cloud Shell。

部署完成上述步驟之後，請流覽至 Azure 入口網站中的 Cloud Shell 或開啟 https://shell.azure.com 。

> [!NOTE]
> 如果過去曾使用過 Cloud Shell，就必須卸載現有的 clouddrive。 若要從作用 `clouddrive unmount` 中的 Cloud Shell 會話執行此動作，請重新整理您的頁面。

連接到 Cloud Shell，系統會提示您第一次執行體驗。 選取您慣用的 shell 體驗，選取 [顯示先進的設定]，然後選取 [顯示 VNET 隔離設定] 方塊。 填寫快顯視窗中的欄位。  大部分欄位都會自動填入可與虛擬網路中 Cloud Shell 相關聯的可用資源。  檔案共用名稱必須由使用者填入。


![說明 Cloud Shell 隔離 VNET 的第一個體驗設定。](media/private-vnet/vnet-settings.png)

## <a name="next-steps"></a>後續步驟
[瞭解 Azure 虛擬網路](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)
