---
title: Avere vFXT 必要條件 - Azure
description: Avere vFXT for Azure 的必要條件
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 01/21/2020
ms.author: rohogue
ms.openlocfilehash: 538f3f18e1c8889834b9ee6588b85c9094dfbebe
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87496707"
---
# <a name="prepare-to-create-the-avere-vfxt"></a>準備建立 Avere vFXT

本文說明建立 Avere vFXT 叢集的先決條件工作。

## <a name="create-a-new-subscription"></a>建立新的訂用帳戶

首先請建立新的 Azure 訂用帳戶。 針對每個 Avere vFXT 專案使用個別的訂用帳戶，以簡化費用追蹤和清除，並在布建叢集工作流程時，確保其他專案不會受到配額或資源節流的影響。

若要在 Azure 入口網站中建立新的 Azure 訂用帳戶：

1. 瀏覽至 [訂用帳戶](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) 刀鋒視窗
1. 按一下位於頂端的 [+ 新增]**** 按鈕
1. 在出現提示時登入
1. 選取供應項目，並逐步執行新建訂用帳戶的步驟

## <a name="configure-subscription-owner-permissions"></a>設定訂用帳戶擁有者權限

具有擁有者權限的使用者應建立 vFXT 叢集。 建立叢集需要擁有者接受軟體條款，並授權對網路和存放裝置資源進行變更。

有一些因應措施可讓非擁有者建立 Avere vFXT for Azure 叢集。 這些案例牽涉到限制資源，並將額外的 Azure 角色指派給建立者。 在所有這些情況下，訂用帳戶擁有者也必須在一段時間內[接受 Avere vFXT 軟體條款](#accept-software-terms)。

| 狀況 | 限制 | 建立 Avere vFXT 叢集所需的存取角色 |
|----------|--------|-------|
| 資源群組系統管理員建立 vFXT | 必須在資源群組內建立虛擬網路、叢集控制器和叢集節點。 | [使用者存取系統管理員](../role-based-access-control/built-in-roles.md#user-access-administrator)和[參與者](../role-based-access-control/built-in-roles.md#contributor)角色，其範圍僅限於目標資源群組。 |
| 使用現有的外部虛擬網路 | 叢集控制器和叢集節點會建立在 vFXT 的資源群組內，但會使用不同資源群組中的現有虛擬網路。 | （1）「[使用者存取系統管理員](../role-based-access-control/built-in-roles.md#user-access-administrator)」和「[參與者](../role-based-access-control/built-in-roles.md#contributor)」角色的範圍設定為 vFXT 資源群組;和（2）[虛擬機器參與者](../role-based-access-control/built-in-roles.md#virtual-machine-contributor)、[使用者存取系統管理員](../role-based-access-control/built-in-roles.md#user-access-administrator)，以及範圍設定為虛擬網路資源群組的[Avere 參與者](../role-based-access-control/built-in-roles.md#avere-contributor)角色。 |
| 叢集建立者的自訂角色 | 沒有資源位置限制。 這個方法會為非擁有者提供重要的許可權。 | 訂用帳戶擁有者會建立 Azure 自訂角色，如[這篇文章](avere-vfxt-non-owner.md)中所述。 |

## <a name="quota-for-the-vfxt-cluster"></a>vFXT 叢集配額

請確認您有足夠的配額可用於下列 Azure 元件。 如有需要，請[要求增加配額](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)。

> [!NOTE]
> 此處所列的是 vFXT 叢集本身的虛擬機器和 SSD 元件。 請記住，您也需要您將用於計算伺服器陣列的 Vm 和 Ssd 配額。
>
> 請確定您在要執行工作流程的區域中可獲得配額。

|Azure 元件|Quota|
|----------|-----------|
|虛擬機器|3個或多個 E32s_v3 （每個叢集節點一個） |
|進階 SSD 儲存體|200 GB 的 OS 空間，外加每個節點 1 TB 到 4 TB 的快取空間 |
|儲存體帳戶 (選擇性) |v2|
|資料後端儲存體 (選擇性) |一個新的 LRS Blob 容器 |
<!-- this table also appears in the overview - update it there if updating here -->

## <a name="accept-software-terms"></a>接受軟體條款

> [!TIP]
> 如果訂用帳戶擁有者將建立 Avere vFXT 叢集，請略過此步驟。

建立叢集的期間，您必須接受 Avere vFXT 軟體的服務條款。 如果您不是訂用帳戶擁有者，請由訂用帳戶擁有者事先接受這項條款。

每個訂用帳戶只需要執行此步驟一次。

若要事先接受軟體條款：

1. 在 Azure 入口網站中或瀏覽到 <https://shell.azure.com>，以開啟 Cloud Shell。 使用您的訂用帳戶識別碼登入。

   ```azurecli
    az login
    az account set --subscription <subscription ID>
   ```

1. 發出下列命令以接受服務條款，並啟用 Avere vFXT for Azure 軟體映像的程式設計存取：

   ```azurecli
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-controller:latest
   ```

## <a name="create-a-storage-service-endpoint-in-your-virtual-network-if-needed"></a>在您的虛擬網路中建立儲存體服務端點（如有需要）

[服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)會將 Azure Blob 流量保留在本機，而不是將其路由至虛擬網路外部。 建議使用 Azure Blob 來儲存後端資料的任何 Avere vFXT for Azure 叢集。

如果您在建立叢集時建立新的虛擬網路，則會自動建立端點。 如果您提供現有的虛擬網路，如果您想要在叢集建立期間建立新的 Blob 儲存體容器，則必須擁有 Microsoft 儲存體服務端點。<!-- if there is no endpoint in that situation, the cluster creation will fail -->

> [!TIP]
>
>* 如果您要建立新的虛擬網路做為叢集建立的一部分，請略過此步驟。
>* 如果您不是在叢集建立期間建立 Blob 儲存體，則端點是選擇性的。 在此情況下，如果您決定使用 Azure Blob，您可以在稍後建立服務端點。

從 Azure 入口網站建立儲存體服務端點。

1. 在入口網站中，開啟您的虛擬網路清單。
1. 選取叢集的虛擬網路。
1. 按一下左側功能表中的 [**服務端點**]。
1. 按一下頂端的 [新增]****。
1. 選擇服務 ``Microsoft.Storage`` 。
1. 選取叢集的子網。
1. 在底部按一下 [新增]****。

   ![含有建立服務端點之註解的 Azure 入口網站螢幕擷取畫面](media/avere-vfxt-service-endpoint.png)

## <a name="next-steps"></a>後續步驟

完成這些必要條件之後，您就可以建立叢集。 如需指示，請參閱[部署 vFXT 叢集](avere-vfxt-deploy.md)。
