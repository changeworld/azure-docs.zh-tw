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
ms.date: 11/20/2018
ms.author: genli
ms.openlocfilehash: 063ebc40fd845fe6300b008e7ca048357a2fce49
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95806676"
---
# <a name="troubleshoot-outbound-smtp-connectivity-issues-in-azure"></a>針對 Azure 中的外送 SMTP 連線能力問題進行疑難排解

從 2017 年 11 月 15 日起，直接從虛擬機器 (VM) 傳送到外部網域 (例如 outlook.com 和 gmail.com) 的外寄郵件訊息僅供 Microsoft Azure 中特定訂用帳戶類型使用。 使用 TCP 連接埠 25 的外送 SMTP 連線會遭到封鎖。 (連接埠 25 主要用於未經驗證的電子郵件傳遞。)

這項行為變更僅適用於 2017 年 11 月 15 日之後的新訂用帳戶及新部署。

## <a name="recommended-method-of-sending-email"></a>傳送電子郵件的建議方法

我們建議您使用經驗證的 SMTP 轉送服務 (通常透過 TCP 連接埠 587 或 443 連線，但也支援其他連接埠) 來從 Azure VM 或 Azure 應用程式服務傳送電子郵件。 這些服務會用於維持 IP 或網域信譽，以將協力廠商電子郵件提供者拒絕訊息的可能性降至最低。 這類 SMTP 轉送服務包括但不限於 [SendGrid](https://sendgrid.com/partners/azure/)。 您可能也會有在內部部署上執行並且可以加以使用的安全 SMTP 轉送服務。

無論訂用帳戶類型為何，在 Azure 中使用這些電子郵件傳遞服務皆不會受限。

## <a name="enterprise-agreement"></a>Enterprise 合約

針對 Azure Enterprise 合約使用者，在不使用經驗證轉送的情況下傳送電子郵件的技術能力沒有任何變更。 新的和現有 Enterprise 合約使用者可以嘗試從 Azure VM 直接將外寄電子郵件傳遞到外部電子郵件提供者，而不會受到 Azure 平台的任何限制。 雖然不保證電子郵件提供者會接受從任何指定使用者傳入的電子郵件，但 Azure 平台不會針對位於 Enterprise 合約訂用帳戶中的 VM 進行封鎖。 您必須直接使用電子郵件提供者來修正涉及特定提供者的任何訊息傳遞或垃圾郵件篩選問題。

## <a name="pay-as-you-go"></a>Pay-As-You-Go

如果您在2017年11月15日之前註冊隨用隨付訂用帳戶，則嘗試輸出電子郵件傳遞的技術能力不會有任何變更。 您可以繼續嘗試從這些訂用帳戶中的 Azure VM 將外寄電子郵件直接傳遞到外部電子郵件提供者，而不會受到 Azure 平台的任何限制。 同樣地，不保證電子郵件提供者會接受從任何指定使用者的內送電子郵件，並且使用者將需要直接使用電子郵件提供者來修正涉及特定提供者的任何訊息傳遞或垃圾郵件篩選問題。

針對在2017年11月15日之後建立的隨用隨付訂用帳戶，將會有技術限制，會封鎖從這些訂用帳戶內的 Vm 直接傳送的電子郵件。 如果您想要能夠將電子郵件從 Azure Vm 直接傳送給外部電子郵件提供 (者，而不是使用已驗證的 SMTP 轉送) ，您可以在 [Azure 入口網站中的 Azure 虛擬網路 **資源的 [** **診斷和解決** ] 分頁的 [連線] 區段中提出要求，以移除限制。 如果合格，您的訂用帳戶將會啟用，否則您將會收到後續步驟的指示。

當您豁免隨用隨付訂用帳戶，且已在 Azure 入口網站中停止並啟動 Vm 之後，該訂用帳戶中的所有 Vm 都會繼續進行。 豁免只適用于所要求的訂用帳戶，且僅適用于直接路由傳送至網際網路的 VM 流量。

> [!NOTE]
> Microsoft 保留在判斷發生違反服務條款的情況時撤銷此豁免的權利。

## <a name="msdn-azure-pass-azure-in-open-education-azure-for-students-vistual-studio-and-free-trial"></a>MSDN、Azure Pass、Azure in Open、教育版、Azure 學生版、Visual Studio 及免費試用版

如果您在2017年11月15日之後建立了 MSDN、Azure Pass、Azure in Open、教育版、Azure 學生版、免費試用版或任何 Visual Studio 訂用帳戶，您將會有技術限制，會封鎖從這些訂用帳戶內的 Vm 直接傳送到電子郵件提供者的電子郵件。 這些限制的目的為避免不當使用。 任何針對移除此限制所提出的要求都不會獲得認可。

若您使用這些訂用帳戶類型，我們建議您使用 SMTP 轉送服務 (如本文先前所述)，或變更您的訂用帳戶類型。

## <a name="cloud-service-provider-csp"></a>雲端服務提供者 (CSP)

如果您是透過 CSP 使用 Azure 資源，您可以在 Azure 入口網站中提出要求，以移除虛擬網路資源之 [診斷和解決] 分頁的 [連線能力] 區段中的限制。 如果合格，您的訂用帳戶將會啟用，否則您將會收到後續步驟的指示。

## <a name="microsoft-partner-network-mpn-bizspark-plus-or-azure-sponsorship"></a>Microsoft 合作夥伴網路 (MPN) 、BizSpark Plus 或 Azure 贊助

針對 Microsoft 合作夥伴網路 (MPN) 、BizSpark Plus 或 Azure 贊助在2017年11月15日之後建立的訂用帳戶，將會有技術限制，會封鎖從這些訂用帳戶內的 Vm 直接傳送的電子郵件。 如果您想要能夠將電子郵件從 Azure vm 直接傳送給外部電子郵件提供者， (不使用已驗證的 SMTP 轉送) ，您可以使用下列問題類型來提出要求：**技術**  >  **虛擬網路** 連線  >  **Connectivity**  >  **無法傳送電子郵件 (SMTP/埠 25)**。 請務必新增詳細資料，說明為何您的部署需要直接將電子郵件傳送到電子郵件提供者，而不是使用經驗證的轉送。 要求將會依 Microsoft 的決定進行審核和核准。 只有在額外的防詐騙檢查完成後，才可授與要求。 

當您豁免隨用隨付訂用帳戶，且已在 Azure 入口網站中停止並啟動 Vm 之後，該訂用帳戶中的所有 Vm 都會繼續進行。 豁免只適用于所要求的訂用帳戶，且僅適用于直接路由傳送至網際網路的 VM 流量。

## <a name="restrictions-and-limitations"></a>限制事項

- 不支援透過 Azure PaaS 服務（例如 [Azure 防火牆](https://azure.microsoft.com/services/azure-firewall/) ）路由埠25流量。

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員

如果仍需要協助，請 [連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以使用下列問題類型快速解決您的問題：**訂用帳戶管理** 問題類型：**啟用連接埠 25 電子郵件流程的要求**。
