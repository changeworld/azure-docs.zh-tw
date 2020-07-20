---
title: 針對 Azure 中的外送 SMTP 連線能力進行疑難排解 | Microsoft Docs
description: 了解如何針對 Azure 中外送 SMTP 連線能力的問題進行疑難排解。
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
ms.openlocfilehash: 148a8bf7626d8b8ee687658990fdaf2fce9f7de7
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83590089"
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
若您在 2017 年 11 月 15 日之前註冊隨用隨付或 Microsoft 合作夥伴網路訂閱供應項目，嘗試外寄電子郵件傳遞的技術能力不會有任何變更。 您可以繼續嘗試從這些訂用帳戶中的 Azure VM 將外寄電子郵件直接傳遞到外部電子郵件提供者，而不會受到 Azure 平台的任何限制。 同樣地，不保證電子郵件提供者會接受從任何指定使用者的內送電子郵件，並且使用者將需要直接使用電子郵件提供者來修正涉及特定提供者的任何訊息傳遞或垃圾郵件篩選問題。

針對在 2017 年 11 月 15 日之後建立的隨用隨付或 Microsoft 合作夥伴網路訂用帳戶，將會受到技術性限制，而無法從這些訂用帳戶內的 VM 直接傳送電子郵件。 如果您想要將電子郵件從 Azure VM 直接傳送給外部電子郵件提供者 (不使用經驗證的 SMTP 轉送) 的功能，可以提出移除限制的要求。 要求將在 Microsoft 的斟酌下審查與核准，而且只會在另外完成反詐騙檢查之後授權。 若要提出要求，請使用下列問題類型來開啟支援案例：[技術] > [虛擬網路] > [連線] > [無法傳送電子郵件 (SMTP/連接埠 25)]。 請務必新增詳細資料，說明為何您的部署需要直接將電子郵件傳送到電子郵件提供者，而不是使用經驗證的轉送。

在隨用隨付或 Microsoft 合作夥伴網路訂用帳戶受到豁免，而且已存 Azure 入口網站「停止後啟動」VM 之後，該訂用帳戶內的 VM 將會在之後受到豁免。 豁免僅適用於要求的訂用帳戶，且僅適用於直接路由傳送至網際網路的虛擬機器流量。 不支援透過 Azure PaaS 服務 (例如 [Azure 防火牆](https://azure.microsoft.com/services/azure-firewall/)) 路由傳送連接埠 25 流量。

> [!NOTE]
> Microsoft 保留在判斷發生違反服務條款的情況時撤銷此豁免的權利。

## <a name="msdn-azure-pass-azure-in-open-education-bizspark-and-free-trial"></a>MSDN、Azure Pass、Azure in Open、Education、BizSpark 及免費試用
若您在 2017 年 11 月 15 日之後建立 MSDN、Azure Pass、Azure in Open、Education、BizSpark、Azure 贊助、Azure 學生、免費試用或任何 Visual Studio 訂用帳戶，由於技術性限制，您將無法從這些訂用帳戶內的 VM 直接將電子郵件傳送給電子郵件提供者。 這些限制的目的為避免不當使用。 任何針對移除此限制所提出的要求都不會獲得認可。

若您使用這些訂用帳戶類型，我們建議您使用 SMTP 轉送服務 (如本文先前所述)，或變更您的訂用帳戶類型。

## <a name="cloud-service-provider-csp"></a>雲端服務提供者 (CSP)

若您是透過 CSP 使用 Azure 資源，可以在無法使用安全 SMTP 轉送時要求 CSP 代您向 Microsoft 提出解除封鎖豁免要求。

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員

如果仍需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以使用下列問題類型快速解決您的問題：**訂用帳戶管理**問題類型：**啟用連接埠 25 電子郵件流程的要求**。
