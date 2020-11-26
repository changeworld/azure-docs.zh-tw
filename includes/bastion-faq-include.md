---
title: 包含檔案
description: 包含檔案
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 11/05/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 48a7912b1a6375111b6f9af1496cbbd41966202c
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183876"
---
### <a name="which-regions-are-available"></a><a name="regions"></a>哪些區域可用？

[!INCLUDE [Azure Bastion region availability](bastion-regions-include.md)]

### <a name="do-i-need-a-public-ip-on-my-virtual-machine-to-connect-via-azure-bastion"></a><a name="publicip"></a>我的虛擬機器上是否需要公用 IP 才能透過 Azure Bastion 進行連線？

否。 使用 Azure Bastion 連線至 VM 時，要與 VM 連線的 Azure 虛擬機器上不需要公用 IP。 Bastion 服務將會透過虛擬網路中的虛擬機器私人 IP，開啟 RDP/SSH 工作階段/連線至您的虛擬機器。

### <a name="is-ipv6-supported"></a>是否支援 IPV6？

目前不支援 IPv6。 Azure Bastion 僅支援 IPv4。

### <a name="do-i-need-an-rdp-or-ssh-client"></a><a name="rdpssh"></a>我需要 RDP 或 SSH 用戶端嗎？

否。 您不需要 RDP 或 SSH 用戶端，即可在 Azure 入口網站中對您的 Azure 虛擬機器進行 RDP/SSH 存取。 使用 [Azure 入口網站](https://portal.azure.com)可讓您直接在瀏覽器中對虛擬機器進行 RDP/SSH 存取。

### <a name="do-i-need-an-agent-running-in-the-azure-virtual-machine"></a><a name="agent"></a>我需要在 Azure 虛擬機器中執行代理程式嗎？

否。 您不需要在瀏覽器或 Azure 虛擬機器上安裝代理程式或任何軟體。 Bastion 服務沒有代理程式，不需要任何其他 RDP/SSH 軟體。

### <a name="how-many-concurrent-rdp-and-ssh-sessions-does-each-azure-bastion-support"></a><a name="limits"></a>每個 Azure Bastion 可支援多少個並行 RDP 和 SSH 工作階段？

RDP 和 SSH 都是以使用方式為基礎的通訊協定。 工作階段的高使用量會導致堡壘主機支援較低的工作階段總數。 以下數字是正常的日常工作流程假設。

[!INCLUDE [limits](bastion-limits.md)]

### <a name="what-features-are-supported-in-an-rdp-session"></a><a name="rdpfeaturesupport"></a>RDP 工作階段中支援哪些功能？

目前僅支援文字複製/貼上功能。 不支援檔案複製之類的功能。 請不吝在 [Azure Bastion 意見反應頁面](https://feedback.azure.com/forums/217313-networking?category_id=367303)分享關於新功能的意見反應。

### <a name="does-bastion-hardening-work-with-aadj-vm-extension-joined-vms"></a><a name="aadj"></a>強化的 Bastion 是否會與已加入 AADJ VM 擴充功能的 VM 搭配運作？

這項功能不適用於使用 Azure AD 之使用者且已加入 AADJ VM 擴充功能的電腦。 如需詳細資訊，請參閱 [Windows Azure VM 和 Azure AD](../articles/active-directory/devices/howto-vm-sign-in-azure-ad-windows.md#requirements)。

### <a name="which-browsers-are-supported"></a><a name="browsers"></a>支援哪些瀏覽器？

在 Windows 中，請使用 Microsoft Edge 瀏覽器或 Google Chrome。 若為 Apple Mac，請使用 Google Chrome 瀏覽器。 Windows 和 Mac 也支援 Microsoft Edge Chromium。

### <a name="where-does-azure-bastion-store-customer-data"></a><a name="data"></a>Azure Bastion 會將客戶資料儲存在何處？

Azure Bastion 不會將客戶資料移動或儲存到其部署所在的區域外。

### <a name="are-any-roles-required-to-access-a-virtual-machine"></a><a name="roles"></a>需要哪些角色權限才能存取虛擬機器？

若要建立連線，必須具備下列角色：

* 虛擬機器上的讀取者角色
* 虛擬機器的私人 IP 位址與 NIC 上的讀取者角色
* Azure Bastion 資源上的讀取者角色

### <a name="what-is-the-pricing"></a><a name="pricingpage"></a>價格為何？

如需詳細資訊，請參閱[價格頁面](https://aka.ms/BastionHostPricing)。

### <a name="does-azure-bastion-require-an-rds-cal-for-administrative-purposes-on-azure-hosted-vms"></a><a name="rdscal"></a>Azure Bastion 是否需要 RDS CAL 才能在 Azure 裝載的虛擬機器上進行管理？

否，當 Azure Bastion 僅用於管理目的時，不需要 [RDS CAL](https://www.microsoft.com/p/windows-server-remote-desktop-services-cal/dg7gmgf0dvsv?activetab=pivot:overviewtab) 也可以存取 Windows Server VM。

### <a name="which-keyboard-layouts-are-supported-during-the-bastion-remote-session"></a><a name="keyboard"></a>Bastion 遠端工作階段期間支援哪些鍵盤配置？

Azure Bastion 目前在 VM 內支援 en-us-qwerty 鍵盤配置。  鍵盤配置的其他地區設定支援正在進行中。

### <a name="is-user-defined-routing-udr-supported-on-an-azure-bastion-subnet"></a><a name="udr"></a>Azure Bastion 子網路是否支援使用者定義的路由 (UDR)？

否。 Azure Bastion 子網路不支援 UDR。

若是相同的虛擬網路中同時包含 Azure Bastion 和 Azure 防火牆/網路虛擬設備 (NVA)，您無需強制將 Azure Bastion 子網路的流量導向 Azure 防火牆，因為 Azure Bastion 和您的 VM 間採用私人通訊。 如需詳細資訊，請參閱[透過 Bastion 從 Azure 防火牆後方存取 VM](https://azure.microsoft.com/blog/accessing-virtual-machines-behind-azure-firewall-with-azure-bastion/)。

### <a name="why-do-i-get-your-session-has-expired-error-message-before-the-bastion-session-starts"></a><a name="session"></a>為何在 Bastion 工作階段開始之前，收到「您的工作階段已過期」錯誤訊息？

您應該只能從 Azure 入口網站起始工作階段。 登入 Azure 入口網站並再次開始您的工作階段。 如果您直接從另一個瀏覽器工作階段或索引標籤移至 URL，則預計會發生此錯誤。 這有助於確保您的工作階段更加安全，而且只能透過 Azure 入口網站存取工作階段。

### <a name="how-do-i-handle-deployment-failures"></a><a name="udr"></a>我該如何處理部署失敗？

檢閱任何錯誤訊息，並視需要[在 Azure 入口網站提出支援要求](../articles/azure-portal/supportability/how-to-create-azure-support-request.md)。 部署失敗的原因可能是 [Azure 訂用帳戶限制、配額和條件約束](../articles/azure-resource-manager/management/azure-subscription-service-limits.md)。 具體而言，客戶可能會遇到每個訂用帳戶允許的公用 IP 位址數目限制，進而導致 Azure Bastion 部署失敗。

### <a name="how-do-i-incorporate-azure-bastion-in-my-disaster-recovery-plan"></a><a name="dr"></a>如何在我的災害復原方案中納入 Azure Bastion？

Azure Bastion 會部署在 VNet 或對等互連 VNet 內，並與 Azure 區域相關聯。 您必須負責將 Azure Bastion 部署到災害復原 (DR) 網站 VNet。 發生 Azure 區域失敗時，請執行 VM 到 DR 區域的容錯移轉作業。 然後，使用在 DR 區域中部署的 Azure Bastion 主機來連線到現在在該處部署的 VM。