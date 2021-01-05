---
title: 針對 Azure 中的外送 SMTP 連線能力進行疑難排解 | Microsoft Docs
description: 瞭解傳送電子郵件的建議方法，以及如何針對 Azure 中的輸出 SMTP 連線問題進行疑難排解。
services: virtual-network
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: genli
ms.openlocfilehash: e1e7e78cab1f3a240737b5e25e0dff28c420add8
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97883107"
---
# <a name="troubleshoot-outbound-smtp-connectivity-problems-in-azure"></a>針對 Azure 中的輸出 SMTP 連線能力問題進行疑難排解

自2017年11月15日起，直接傳送至外部網域的輸出電子郵件訊息 (例如，從虛擬機器 (VM) 的 outlook.com 和 gmail.com) ，只會提供給 Azure 中的特定訂用帳戶類型使用。 使用 TCP 連接埠 25 的外送 SMTP 連線會遭到封鎖。  (埠25主要用於未經驗證的電子郵件傳遞。 ) 

這項行為變更僅適用于在2017年11月15日之後建立的訂用帳戶和部署。

## <a name="recommended-method-of-sending-email"></a>傳送電子郵件的建議方法

建議您使用已驗證的 SMTP 轉送服務，從 Azure Vm 或 Azure App Service 傳送電子郵件。  (這些轉送服務通常會透過 TCP 埠587或443來連線，但支援其他埠。 ) 這些服務可用來維持 IP 或網域信譽，以將協力廠商電子郵件提供者拒絕訊息的可能性降至最低。 [SendGrid](https://sendgrid.com/partners/azure/) 是其中一項 SMTP 轉送服務，但還有其他服務。 您也可能會有在內部部署執行的安全 SMTP 轉送服務，可供您使用。

無論訂用帳戶類型為何，在 Azure 中使用這些電子郵件傳遞服務皆不會受限。

## <a name="enterprise-agreement"></a>Enterprise 合約

針對 Azure Enterprise 合約使用者，在不使用經驗證轉送的情況下傳送電子郵件的技術能力沒有任何變更。 新的和現有 Enterprise 合約使用者可以嘗試從 Azure VM 直接將外寄電子郵件傳遞到外部電子郵件提供者，而不會受到 Azure 平台的任何限制。 不保證電子郵件提供者會接受來自任何指定使用者的傳入電子郵件。 但 Azure 平臺不會封鎖 Enterprise 合約訂用帳戶內 Vm 的傳遞嘗試。 您必須直接使用電子郵件提供者來修正涉及特定提供者的任何訊息傳遞或垃圾郵件篩選問題。

## <a name="pay-as-you-go"></a>隨用隨付

如果您在2017年11月15日之前註冊，則針對隨用隨付訂用帳戶，您的技術能力不會有任何變更可嘗試輸出電子郵件傳遞。 您仍然可以直接從這些訂用帳戶內的 Azure Vm 將輸出電子郵件傳遞給外部電子郵件提供者，而不會受到 Azure 平臺的任何限制。 同樣地，也不保證電子郵件提供者會接受來自任何指定使用者的傳入電子郵件。 使用者必須直接使用電子郵件提供者來修正涉及特定提供者的任何訊息傳遞或垃圾郵件篩選問題。

針對在2017年11月15日之後建立的隨用隨付訂用帳戶，將會有技術限制，會封鎖從訂用帳戶內的 Vm 直接傳送的電子郵件。 如果您想要能夠將電子郵件從 Azure Vm 直接傳送給外部電子郵件提供者 (而不是使用已驗證的 SMTP 轉送) 而且您的帳戶在使用付款記錄的情況下，您可以要求移除該限制。 您可以在 Azure 入口網站中的 Azure 虛擬網路資源的 [**診斷和解決**] 分頁的 [連線 **] 區段中**，進行這項操作。 如果接受您的要求，您的訂用帳戶將會啟用，否則您將會收到後續步驟的指示。 

當您豁免隨用隨付訂用帳戶並在 Azure 入口網站中停止並重新啟動 Vm 之後，該訂用帳戶中的所有 Vm 都將會被免除。 豁免只適用于所要求的訂用帳戶，且僅適用于直接路由傳送至網際網路的 VM 流量。

> [!NOTE]
> 如果判斷出違反服務條款的規定，Microsoft 會保留撤銷這些豁免的權利。

## <a name="msdn-azure-pass-azure-in-open-education-azure-for-students-visual-studio-and-free-trial"></a>MSDN、Azure Pass、Azure in Open、教育版、Azure 學生版、Visual Studio 及免費試用版

如果您在2017年11月15日之後建立了下列其中一種訂用帳戶類型，您將會有技術限制，會封鎖從訂用帳戶內的 Vm 直接傳送到電子郵件提供者的電子郵件：
- MSDN
- Azure Pass
- Azure in Open
- 教育訓練
- Azure 學生版
- 免費試用
- 任何 Visual Studio 訂用帳戶  

限制是為了防止濫用。 將不會授與移除這些限制的要求。

如果您要使用這些訂用帳戶類型，建議您使用 SMTP 轉送服務（如本文稍早所述），或變更您的訂用帳戶類型。

## <a name="cloud-solution-provider"></a>雲端解決方案提供者

如果您是透過雲端解決方案提供者使用 Azure 資源，您可以在 Azure 入口網站中的虛擬網路資源，提出要求以移除 [**診斷和解決**]**窗格的 [連線] 區段中** 的限制。 如果接受您的要求，您的訂用帳戶將會啟用，否則您將會收到後續步驟的指示。

## <a name="microsoft-partner-network-bizspark-plus-or-azure-sponsorship"></a>Microsoft 合作夥伴網路、BizSpark Plus 或 Azure 贊助

針對在2017年11月15日之後建立的下列類型的訂用帳戶，將會有技術限制，會封鎖從訂用帳戶內的 Vm 直接傳送的電子郵件：

- Microsoft 合作夥伴網路 (MPN) 
- BizSpark Plus
- Azure 贊助

如果您想要能夠將電子郵件從 Azure vm 直接傳送給外部電子郵件提供者 (而不是使用已驗證的 SMTP 轉送) ，您可以使用下列問題類型開啟支援案例來提出要求：**技術**  >  **虛擬網路** 連線  >    >  **無法傳送電子郵件 (SMTP/埠 25)**。 請務必新增您的部署為何必須將郵件直接傳送到郵件提供者，而不是使用已驗證轉送的詳細資料。 要求將會依 Microsoft 的決定進行審核和核准。 只有在完成額外的 antifraud 檢查之後，才會授與要求。 

在豁免訂用帳戶，且已停止並重新啟動 Azure 入口網站中的 Vm 之後，該訂用帳戶中的所有 Vm 都將會被免除。 豁免只適用于所要求的訂用帳戶，且僅適用于直接路由傳送至網際網路的 VM 流量。

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員

如果仍需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。 使用此問題類型：**技術**  >  **虛擬網路** 連線  >    >  **無法 (SMTP/埠 25) 傳送電子郵件**。
