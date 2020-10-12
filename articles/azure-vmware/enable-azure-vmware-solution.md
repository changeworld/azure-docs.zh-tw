---
title: 如何啟用您的 Azure VMware 解決方案資源
description: 瞭解如何提交支援要求，以啟用您的 Azure VMware 解決方案資源。 您也可以在現有的 Azure VMware 解決方案私人雲端中要求更多節點。
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 72a7aca97067ce4e9ed0e901e4016c82b3549eb1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90817891"
---
# <a name="how-to-enable-azure-vmware-solution-resource"></a>如何啟用 Azure VMware 解決方案資源
瞭解如何提交支援要求，以啟用您的 Azure VMware 解決方案資源。 您也可以在現有的 Azure VMware 解決方案私人雲端中要求更多節點。

## <a name="eligibility-criteria"></a>適用性準則

* 您將需要 [Azure Enterprise 合約 (EA) ](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-agreements) 的 Microsoft。
* Azure 訂閱需要 Azure 帳戶。


## <a name="enable-azure-vmware-solution-resource"></a>啟用 Azure VMware 解決方案資源
建立 Azure VMware 解決方案資源之前，您必須提交支援票證，以配置您的節點。 一旦支援小組收到您的要求後，最多需要五個工作天的時間來確認您的要求，並配置您的節點。 如果您有現有的 Azure VMware 解決方案私人雲端，且想要配置更多節點，您將會經歷相同的程序。


1. 在您的 Azure 入口網站中的 [說明 **+ 支援**] 下，建立 **[新的支援要求](https://rc.portal.azure.com/#create/Microsoft.Support)** ，並為票證提供下列資訊：
   - **問題類型：** 技術需求
   - **訂** 用帳戶：選取您的訂用帳戶
   - **服務：** 所有服務都 > Azure VMware 解決方案
   - **資源：** 一般問題 
   - **摘要：** 需要容量
   - **問題類型：** 容量管理問題
   - **問題子類型：** 其他主機配額/容量的客戶要求

1. 在支援票證的 **描述** 中，于 [ **詳細資料** ] 索引標籤上提供：

   - POC 或生產 
   - 區域名稱
   - 節點數目
   - 任何其他詳細資料

   >[!NOTE]
   >Azure VMware 解決方案建議至少有三個節點來啟動您的私用雲端和冗余 N + 1 個節點。 

1. 選取 [ **審核 + 建立** ] 以提交要求。

   支援代表需要五個工作天的時間，才能確認您的要求。

   >[!IMPORTANT] 
   >如果您已經有現有的 Azure VMware 解決方案，而且您正在要求其他節點，請注意，我們需要五個工作天來配置節點。 

1. 在您可以布建節點之前，請確定您已在 Azure 入口網站中註冊 **MICROSOFT AVS** 資源提供者。  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>
   ```

   如需註冊資源提供者的其他方式，請參閱 [Azure 資源提供者和類型](../azure-resource-manager/management/resource-providers-and-types.md)。