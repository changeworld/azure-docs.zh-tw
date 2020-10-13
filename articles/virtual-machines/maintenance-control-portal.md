---
title: 使用 Azure 入口網站的 Azure 虛擬機器維護控制
description: 瞭解如何使用維護控制和 Azure 入口網站來控制將維護套用至 Azure Vm 的時機。
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/22/2020
ms.author: cynthn
ms.openlocfilehash: b174e2631131e6bf26d7b1cb62442c8a99102e2e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91397278"
---
# <a name="control-updates-with-maintenance-control-and-the-azure-portal"></a>使用維護控制和 Azure 入口網站來控制更新

維護控制可讓您決定何時要將更新套用至隔離的 Vm 和 Azure 專用主機。 本主題涵蓋維護控制的 Azure 入口網站選項。 如需使用維護控制、其限制和其他管理選項之優點的詳細資訊，請參閱 [使用維護控制管理平臺更新](maintenance-control.md)。

## <a name="create-a-maintenance-configuration"></a>建立維護設定

1. 登入 Azure 入口網站。

1. 搜尋 **維護**設定。

   ![顯示如何開啟維護設定的螢幕擷取畫面](media/virtual-machines-maintenance-control-portal/maintenance-configurations-search.png)

1. 按一下 [新增] 。

   ![顯示如何新增維護設定的螢幕擷取畫面](media/virtual-machines-maintenance-control-portal/maintenance-configurations-add.png)

1. 選擇訂用帳戶和資源群組，提供設定的名稱，然後選擇區域。 按一下 [下一步]。

   ![顯示維護設定基本概念的螢幕擷取畫面](media/virtual-machines-maintenance-control-portal/maintenance-configurations-basics.png)

1. 新增標記和值。 按一下 [下一步]。

   ![顯示如何將標記新增至維護設定的螢幕擷取畫面](media/virtual-machines-maintenance-control-portal/maintenance-configurations-tags.png)

1. 檢閱摘要。 按一下 [建立]。

   ![顯示如何建立維護設定的螢幕擷取畫面](media/virtual-machines-maintenance-control-portal/maintenance-configurations-create.png)

1. 部署完成之後，請按一下 [ **移至資源**]。

   ![顯示維護設定部署完成的螢幕擷取畫面](media/virtual-machines-maintenance-control-portal/maintenance-configurations-deployment-complete.png)

## <a name="assign-the-configuration"></a>指派設定

在維護設定的 [詳細資料] 頁面上，按一下 [指派]，然後按一下 [ **指派資源**]。 

![顯示如何指派資源的螢幕擷取畫面](media/virtual-machines-maintenance-control-portal/maintenance-configurations-add-assignment.png)

選取您要指派維護設定的資源，然後按一下 **[確定]**。 [ **類型** ] 欄會顯示資源是隔離的 VM 或 Azure 專用主機。 VM 必須正在執行，才能指派設定。 如果您嘗試將設定指派給已停止的 VM，就會發生錯誤。 

<!---Shantanu to add details about the error case--->

![顯示如何選取資源的螢幕擷取畫面](media/virtual-machines-maintenance-control-portal/maintenance-configurations-select-resource.png)

## <a name="check-configuration"></a>檢查設定

您可以確認設定已正確套用，或查看目前使用 **維護**設定指派的任何維護設定。 [ **類型** ] 欄會顯示設定是指派給隔離的 VM 或 Azure 專用主機。 

![顯示如何檢查維護設定的螢幕擷取畫面](media/virtual-machines-maintenance-control-portal/maintenance-configurations-host-type.png)

您也可以在其 [屬性] 頁面上檢查特定虛擬機器的設定。 按一下 [ **維護** ] 以查看指派給該虛擬機器的設定。

![顯示如何檢查主機維護的螢幕擷取畫面](media/virtual-machines-maintenance-control-portal/maintenance-configurations-check-config.png)

## <a name="check-for-pending-updates"></a>檢查是否有擱置中的更新

另外還有兩種方式可檢查更新是否擱置進行維護設定。 在 [ **維護**設定] 中，按一下設定的詳細資料，然後按一下 [ **指派** ] 和 [檢查 **維護狀態**]。

![顯示如何檢查暫止更新的螢幕擷取畫面](media/virtual-machines-maintenance-control-portal/maintenance-configurations-pending.png)

您也可以使用 **虛擬機器** 或專用主機的屬性來檢查特定的主機。 

![顯示反白顯示的維護狀態的螢幕擷取畫面。](media/virtual-machines-maintenance-control-portal/maintenance-configurations-pending-vm.png)

## <a name="apply-updates"></a>套用更新

您可以使用 **虛擬機器**隨選套用擱置更新。 在 [VM 詳細資料] 上，按一下 [ **維護** ]，然後按一下 [ **立即套用維護**]。

![顯示如何套用暫止更新的螢幕擷取畫面](media/virtual-machines-maintenance-control-portal/maintenance-configurations-apply-updates-now.png)

## <a name="check-the-status-of-applying-updates"></a>檢查應用程式更新的狀態 

您可以針對 **維護** 設定中的設定或使用 **虛擬機器**，檢查更新的進度。 在 [VM 詳細資料] 上，按一下 [ **維護**]。 在下列範例中， **維護狀態** 顯示更新 **暫**止。

![顯示如何檢查暫止更新狀態的螢幕擷取畫面](media/virtual-machines-maintenance-control-portal/maintenance-configurations-status.png)

## <a name="delete-a-maintenance-configuration"></a>刪除維護設定

若要刪除設定，請開啟設定詳細資料，然後按一下 [ **刪除**]。

![顯示如何刪除設定的螢幕擷取畫面。](media/virtual-machines-maintenance-control-portal/maintenance-configurations-delete.png)


## <a name="next-steps"></a>後續步驟

若要深入瞭解，請參閱 [維護和更新](maintenance-and-updates.md)。
