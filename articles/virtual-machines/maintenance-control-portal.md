---
title: 使用 Azure 入口網站進行 Azure 虛擬機器的維護控制
description: 瞭解如何使用維護控制和 Azure 入口網站，控制何時將維護套用至您的 Azure Vm。
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/22/2020
ms.author: cynthn
ms.openlocfilehash: c0cb4800bdabe5eb500422fca55b3060b6422e8e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82139234"
---
# <a name="control-updates-with-maintenance-control-and-the-azure-portal"></a>控制維護控制和 Azure 入口網站的更新

維護控制可讓您決定何時要將更新套用到隔離的 Vm 和 Azure 專用主機。 本主題涵蓋維護控制的 Azure 入口網站選項。 如需使用維護控制、其限制和其他管理選項之優點的詳細資訊，請參閱[管理具有維護控制的平臺更新](maintenance-control.md)。

## <a name="create-a-maintenance-configuration"></a>建立維護設定

1. 登入 Azure 入口網站。

1. 搜尋**維護**設定。

   ![顯示如何開啟維護設定的螢幕擷取畫面](media/virtual-machines-maintenance-control-portal/maintenance-configurations-search.png)

1. 按一下 [加入]  。

   ![顯示如何新增維護設定的螢幕擷取畫面](media/virtual-machines-maintenance-control-portal/maintenance-configurations-add.png)

1. 選擇訂用帳戶和資源群組、提供設定的名稱，然後選擇區域。 按 [下一步]  。

   ![顯示維護設定基本概念的螢幕擷取畫面](media/virtual-machines-maintenance-control-portal/maintenance-configurations-basics.png)

1. 新增標籤和值。 按 [下一步]  。

   ![顯示如何將標記新增至維護設定的螢幕擷取畫面](media/virtual-machines-maintenance-control-portal/maintenance-configurations-tags.png)

1. 檢閱摘要。 按一下 [建立]  。

   ![顯示如何建立維護設定的螢幕擷取畫面](media/virtual-machines-maintenance-control-portal/maintenance-configurations-create.png)

1. 部署完成之後，按一下 [**移至資源**]。

   ![顯示維護設定部署完成的螢幕擷取畫面](media/virtual-machines-maintenance-control-portal/maintenance-configurations-deployment-complete.png)

## <a name="assign-the-configuration"></a>指派設定

在維護設定的 [詳細資料] 頁面上，按一下 [指派]，然後按一下 [**指派資源**]。 

![顯示如何指派資源的螢幕擷取畫面](media/virtual-machines-maintenance-control-portal/maintenance-configurations-add-assignment.png)

選取您想要指派維護設定的資源，然後按一下 **[確定]**。 [**類型**] 欄位會顯示資源是隔離的 VM 還是 Azure 專用主機。 VM 必須正在執行，才能指派設定。 如果您嘗試將設定指派給已停止的 VM，就會發生錯誤。 

<!---Shantanu to add details about the error case--->

![顯示如何選取資源的螢幕擷取畫面](media/virtual-machines-maintenance-control-portal/maintenance-configurations-select-resource.png)

## <a name="check-configuration"></a>檢查設定

您可以確認設定已正確套用，或檢查是否有任何目前使用**維護**設定指派的維護設定。 [**類型**] 資料行會顯示設定是否已指派給隔離的 VM 或 Azure 專用主機。 

![顯示如何檢查維護設定的螢幕擷取畫面](media/virtual-machines-maintenance-control-portal/maintenance-configurations-host-type.png)

您也可以在其 [屬性] 頁面上檢查特定虛擬機器的設定。 按一下 [**維護**] 以查看指派給該虛擬機器的設定。

![顯示如何檢查主機維護的螢幕擷取畫面](media/virtual-machines-maintenance-control-portal/maintenance-configurations-check-config.png)

## <a name="check-for-pending-updates"></a>檢查暫止的更新

還有兩種方式可檢查更新是否擱置中進行維護設定。 在 [**維護**設定] 的詳細資料中，按一下 [**指派**]，然後檢查 [**維護狀態**]。

![顯示如何檢查暫止更新的螢幕擷取畫面](media/virtual-machines-maintenance-control-portal/maintenance-configurations-pending.png)

您也可以使用專用主機的**虛擬機器**或屬性來檢查特定的主機。 

![顯示如何檢查主機維護的螢幕擷取畫面](media/virtual-machines-maintenance-control-portal/maintenance-configurations-pending-vm.png)

## <a name="apply-updates"></a>套用更新

您可以視需要使用**虛擬機器**來套用擱置中的更新。 在 [VM 詳細資料] 上，按一下 [**維護**]，然後按一下 [**立即套用維護**]。

![顯示如何套用暫止更新的螢幕擷取畫面](media/virtual-machines-maintenance-control-portal/maintenance-configurations-apply-updates-now.png)

## <a name="check-the-status-of-applying-updates"></a>檢查套用更新的狀態 

您可以在 [**維護**設定] 或使用 [**虛擬機器**] 中查看設定的更新進度。 在 [VM 詳細資料] 上，按一下 [**維護**]。 在下列範例中，**維護狀態**會顯示 [更新**擱置**中]。

![顯示如何檢查暫止更新狀態的螢幕擷取畫面](media/virtual-machines-maintenance-control-portal/maintenance-configurations-status.png)

## <a name="delete-a-maintenance-configuration"></a>刪除維護設定

若要刪除設定，請開啟 [設定詳細資料]，然後按一下 [**刪除**]。

![顯示如何檢查主機維護的螢幕擷取畫面](media/virtual-machines-maintenance-control-portal/maintenance-configurations-delete.png)


## <a name="next-steps"></a>後續步驟

若要深入瞭解，請參閱[維護和更新](maintenance-and-updates.md)。
