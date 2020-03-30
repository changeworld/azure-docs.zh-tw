---
title: Avere vFXT 必要條件 - Azure
description: Avere vFXT for Azure 的必要條件
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: rohogue
ms.openlocfilehash: a183989cc666f00da4be077c719c40d2524fd6e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252541"
---
# <a name="prepare-to-create-the-avere-vfxt"></a>準備建立 Avere vFXT

本文介紹了創建 Avere vFXT 群集的先決條件任務。

## <a name="create-a-new-subscription"></a>建立新的訂用帳戶

首先請建立新的 Azure 訂用帳戶。 為每個 Avere vFXT 專案使用單獨的訂閱來簡化費用跟蹤和清理，並確保在預配群集工作流時其他專案不受配額或資源限制的影響。

若要在 Azure 入口網站中建立新的 Azure 訂用帳戶：

1. 瀏覽至 [訂用帳戶](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) 刀鋒視窗
1. 按一下位於頂端的 [+ 新增]**** 按鈕
1. 在出現提示時登入
1. 選取供應項目，並逐步執行新建訂用帳戶的步驟

## <a name="configure-subscription-owner-permissions"></a>設定訂用帳戶擁有者權限

具有擁有者權限的使用者應建立 vFXT 叢集。 群集創建要求擁有者接受軟體服務條款，並授權對網路和存儲資源的更改。

有一些解決方法允許非擁有者為 Azure 群集創建 Avere vFXT。 這些方案涉及限制資源並將其他基於角色的存取控制 （RBAC） 角色指派給建立者。 在所有這些情況下，訂閱擁有者還必須提前[接受 Avere vFXT 軟體術語](#accept-software-terms)。

| 狀況 | 限制 | 創建 Avere vFXT 群集所需的訪問角色 |
|----------|--------|-------|
| 資源組管理員創建 vFXT | 必須在資源組中創建虛擬網路、叢集控制器和叢集節點。 | [使用者訪問管理員](../role-based-access-control/built-in-roles.md#user-access-administrator)和[參與者](../role-based-access-control/built-in-roles.md#contributor)角色，都限定在目標資源組。 |
| 使用現有的外部虛擬網路 | 叢集控制器和叢集節點在 vFXT 的資源組中創建，但使用不同的資源組中的現有虛擬網路。 | （1）[使用者訪問管理員](../role-based-access-control/built-in-roles.md#user-access-administrator)和[參與者](../role-based-access-control/built-in-roles.md#contributor)角色，範圍限定為 vFXT 資源組;和 （2）[虛擬機器參與者](../role-based-access-control/built-in-roles.md#virtual-machine-contributor)、[使用者訪問管理員](../role-based-access-control/built-in-roles.md#user-access-administrator)和[Avere 參與者](../role-based-access-control/built-in-roles.md#avere-contributor)角色，這些角色範圍限定在虛擬網路的資源組。 |
| 群集建立者的自訂角色 | 沒有資源放置限制。 此方法為非擁有者提供重要許可權。 | 訂閱擁有者創建一個自訂 RBAC 角色，如[本文](avere-vfxt-non-owner.md)所述。 |

## <a name="quota-for-the-vfxt-cluster"></a>vFXT 叢集配額

檢查是否有足夠的配額用於以下 Azure 元件。 如有需要，請[要求增加配額](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)。

> [!NOTE]
> 此處所列的是 vFXT 叢集本身的虛擬機器和 SSD 元件。 請記住，對於將用於計算伺服器場的 VM 和 SSD，還需要配額。
>
> 請確定您在要執行工作流程的區域中可獲得配額。

|Azure 元件|Quota|
|----------|-----------|
|虛擬機器|3 個或更多E32s_v3（每個叢集節點一個） |
|進階 SSD 儲存體|200 GB 的 OS 空間，外加每個節點 1 TB 到 4 TB 的快取空間 |
|儲存體帳戶 (選擇性) |v2|
|資料後端儲存體 (選擇性) |一個新的 LRS Blob 容器 |
<!-- this table also appears in the overview - update it there if updating here -->

## <a name="accept-software-terms"></a>接受軟體條款

> [!TIP]
> 如果訂閱擁有者將創建 Avere vFXT 群集，請跳過此步驟。

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

## <a name="create-a-storage-service-endpoint-in-your-virtual-network-if-needed"></a>在虛擬網路中創建存儲服務終結點（如果需要）

[服務終結點](../virtual-network/virtual-network-service-endpoints-overview.md)將 Azure Blob 流量保持在本地，而不是將其路由到虛擬網路之外。 建議對使用 Azure Blob 進行後端資料存儲的任何 Avere vFXT。

如果在創建群集時創建新的虛擬網路，則會自動創建終結點。 如果提供現有虛擬網路，如果要在群集創建期間創建新的 Blob 存儲容器，它必須具有 Microsoft 存儲服務終結點。<!-- if there is no endpoint in that situation, the cluster creation will fail -->

> [!TIP]
>
>* 如果要創建新虛擬網路作為群集創建的一部分，請跳過此步驟。
>* 如果在群集創建期間未創建 Blob 存儲，則終結點是可選的。 在這種情況下，如果您決定使用 Azure Blob，則可以稍後創建服務終結點。

從 Azure 門戶創建存儲服務終結點。

1. 在門戶中，打開虛擬網路清單。
1. 選擇群集的虛擬網路。
1. 按一下左側功能表中的**服務終結點**。
1. 按一下頂端的 [新增]****。
1. 選擇服務``Microsoft.Storage``。
1. 選擇群集的子網。
1. 在底部按一下 [新增]****。

   ![含有建立服務端點之註解的 Azure 入口網站螢幕擷取畫面](media/avere-vfxt-service-endpoint.png)

## <a name="next-steps"></a>後續步驟

完成這些先決條件後，可以創建群集。 如需指示，請參閱[部署 vFXT 叢集](avere-vfxt-deploy.md)。
