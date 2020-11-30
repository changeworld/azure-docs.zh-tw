---
title: 如何啟用您的 Azure VMware 解決方案資源
description: 瞭解如何提交支援要求，以啟用您的 Azure VMware 解決方案資源。 您也可以在現有的 Azure VMware 解決方案私人雲端中要求更多主機。
ms.topic: how-to
ms.date: 11/12/2020
ms.openlocfilehash: 526d6b38f4b4e3f6c4806b71b4728dee90cf558a
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96325072"
---
# <a name="how-to-enable-azure-vmware-solution-resource"></a>如何啟用 Azure VMware 解決方案資源
瞭解如何提交支援要求，以啟用您的 [Azure VMware 解決方案](introduction.md) 資源。 您也可以在現有的 Azure VMware 解決方案私人雲端中要求更多主機。

## <a name="eligibility-criteria"></a>適用性準則

Azure 訂閱需要 Azure 帳戶。 Azure 訂用帳戶必須符合下列其中一個準則：

* [Azure Enterprise 合約 (EA) ](../cost-management-billing/manage/ea-portal-agreements.md)的訂用帳戶與 Microsoft 一起使用。
* 雲端解決方案提供者 (CSP) Azure 方案下的受控訂用帳戶。


## <a name="enable-azure-vmware-solution-for-ea-customers"></a>啟用適用于 EA 客戶的 Azure VMware 解決方案
在您建立 Azure VMware 解決方案資源之前，您必須提交支援票證以配置您的主機。 一旦支援小組收到您的要求後，最多需要五個工作天的時間來確認您的要求，並配置您的主機。 如果您有現有的 Azure VMware 解決方案私人雲端，且想要配置更多主機，您將會經歷相同的程序。


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
   - 主機數目
   - 任何其他詳細資料

   >[!NOTE]
   >Azure VMware 解決方案建議至少有三部主機，以啟動您的私用雲端和多餘的 N + 1 部主機。 

1. 選取 [ **審核 + 建立** ] 以提交要求。

   支援代表需要五個工作天的時間，才能確認您的要求。

   >[!IMPORTANT] 
   >如果您已經有現有的 Azure VMware 解決方案，而且您正在要求其他主機，請注意，我們需要五個工作天來配置主機。 

1. 在您可以布建主機之前，請確定您已在 Azure 入口網站中註冊 **MICROSOFT AVS** 資源提供者。  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>
   ```

   如需註冊資源提供者的其他方式，請參閱 [Azure 資源提供者和類型](../azure-resource-manager/management/resource-providers-and-types.md)。

## <a name="enable-azure-vmware-solution-for-csp-customers"></a>為 CSP 客戶啟用 Azure VMware 解決方案 

Csp 必須使用 [Microsoft 合作夥伴中心](https://partner.microsoft.com) 為其客戶啟用 Azure VMware 解決方案。 

   >[!IMPORTANT] 
   >Azure VMware 解決方案服務不提供必要的多租使用者。 不支援裝載需要它的合作夥伴。 

1. 在 **合作夥伴中心** 中，選取 [ **CSP** ] 以存取 [ **客戶** ] 區域。

   :::image type="content" source="media/enable-azure-vmware-solution/csp-customers-screen.png" alt-text="Microsoft 合作夥伴中心用戶端區域" lightbox="media/enable-azure-vmware-solution/csp-customers-screen.png":::

1. 選取您的客戶，然後選取 [ **新增產品**]。

   :::image type="content" source="media/enable-azure-vmware-solution/csp-partner-center.png" alt-text="Microsoft 合作夥伴中心" lightbox="media/enable-azure-vmware-solution/csp-partner-center.png":::

1. 選取 [ **Azure 方案** ]，然後選取 [ **新增至購物車**]。 

1. 檢查並完成客戶的 Azure 方案訂用帳戶一般設定。 如需詳細資訊，請參閱 [Microsoft 合作夥伴中心檔](/partner-center/azure-plan-manage)。

設定好 Azure 方案和所需的 vSphere RBAC 許可權之後，您將會參與 Microsoft，以啟用 Azure 方案訂用帳戶的配額。 使用系統 **管理員代表** (AOBO) 程式存取合作夥伴中心的 Azure 入口網站。

1. 登入[合作夥伴中心](https://partner.microsoft.com)。

1. 選取 [ **CSP** ] 以存取 [ **客戶** ] 區域。

1. 展開 [客戶詳細資料]，然後選取 [ **Microsoft Azure 管理入口網站**]。

1. 在 Azure 入口網站的 [說明 **+ 支援**] 下，建立 **[新的支援要求](https://rc.portal.azure.com/#create/Microsoft.Support)** ，並為票證提供下列資訊：
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
   - 主機數目
   - 任何其他詳細資料
   - 要裝載多個客戶嗎？

   >[!NOTE]
   >Azure VMware 解決方案建議至少有三部主機，以啟動您的私用雲端和多餘的 N + 1 部主機。 

1. 選取 [ **審核 + 建立** ] 以提交要求。

   支援代表需要五個工作天的時間，才能確認您的要求。

   >[!IMPORTANT] 
   >如果您已經有現有的 Azure VMware 解決方案，而且您正在要求其他主機，請注意，我們需要五個工作天來配置主機。 

1. 如果訂用帳戶是由服務提供者所管理，則其管理小組必須 **在合作夥伴中心時，代表** (AOBO) 程式存取 Azure 入口網站。 其中一個 Azure 入口網站啟動 [Cloud Shell](../cloud-shell/overview.md) 實例，並註冊 **Microsoft AVS** 資源提供者，然後繼續部署 Azure VMware 解決方案私人雲端。  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>
   ```

   如需註冊資源提供者的其他方式，請參閱 [Azure 資源提供者和類型](../azure-resource-manager/management/resource-providers-and-types.md)。

1. 如果訂用帳戶是由客戶直接管理，則在訂用帳戶中擁有足夠許可權的使用者必須完成 **MICROSOFT AVS** 資源提供者的註冊，請參閱 [Azure 資源提供者和類型](../azure-resource-manager/management/resource-providers-and-types.md) ，以取得更多詳細資料，以及註冊資源提供者的方法。 


## <a name="next-steps"></a>後續步驟

當您啟用 Azure VMware 解決方案資源，且已備妥適當的網路功能之後，您就可以 [建立私人雲端](tutorial-create-private-cloud.md)。