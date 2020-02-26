---
title: 在 Azure 資訊安全中心提供安全性連絡人詳細資料 | Microsoft Docs
description: 本文件說明如何在 Azure 資訊安全中心提供安全性連絡人詳細資料。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 26b5dcb4-ce3f-4f22-8d56-d2bf743cfc90
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/09/2019
ms.author: memildin
ms.openlocfilehash: 15029c3e0bd3959000786af484a42691f00bb704
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77603562"
---
# <a name="provide-security-contact-details-in-azure-security-center"></a>在 Azure 資訊安全中心提供安全性連絡人詳細資料
Azure 資訊安全中心會建議您針對您的 Azure 訂用帳戶提供安全性連絡人詳細資料 (如果您還沒有這麼做)。 如果 Microsoft 安全性回應中心 (MSRC) 發現您的客戶資料遭到非法或未經授權的對象存取，Microsoft 會使用此資訊連絡您。 MSRC 執行 Azure 網路和基礎結構的選取安全性監視，並接收來自協力廠商的威脅情報和濫用客訴。

在每天第一個警示發生時且僅針對高嚴重性警示，才會傳送電子郵件通知。 電子郵件喜好設定只能針對訂用帳戶原則設定。 在訂用帳戶內的資源群組會繼承這些設定。 警示僅適用于 Azure 資訊安全中心的標準層。

所傳送的警示電子郵件通知：
- 僅針對高嚴重性的警示傳送
- 每日傳送給各警示類型的一個電子郵件收件者  
- 在一天內不會傳送給單一收件者超過 3 個電子郵件訊息
- 每個電子郵件訊息均包含單一警示，而非警示的彙總
 
例如，如果電子郵件訊息已傳送警示表示您即將遭受 RDP 攻擊，您在同一天內不會收到另一個表示您即將遭受 RDP 攻擊的電子郵件訊息，即使是另一個警示已觸發。 

> [!NOTE]
> 本文件將使用範例部署來介紹服務。  這不是逐步指南。

## 設定警示的電子郵件通知<a name="email"></a>

1. 從入口網站中，選取 [定價] [ **& 設定**]。
1. 按一下 [訂用帳戶]。
1. 按一下 [電子郵件通知]。

> [!NOTE]
> 如果您正在執行建議，請在 [**建議**] 底下，選取 [**提供安全性連絡人詳細資料**]，選取要提供連絡人資訊的 Azure 訂用帳戶。 這會開啟 [電子郵件通知]。

   ![提供安全性連絡人詳細資料][2]

   * 輸入安全性連絡人的電子郵件地址，若有多個則以逗號分隔。 您可以輸入的電子郵件地址數沒有限制。
   * 輸入一個安全性連絡人國際電話號碼。
   * 若要接收有關高嚴重性警示的電子郵件，請開啟 [傳送給我有關警示的電子郵件]選項。
   * 您可以選擇將電子郵件通知傳送給訂用帳戶擁有者（傳統服務管理員和共同管理員，再加上訂用帳戶範圍中的 RBAC 擁有者角色）。
   * 選取 [儲存] 將安全性連絡人資訊套用至您的訂用帳戶。

## <a name="see-also"></a>另請參閱
如要深入了解資訊安全中心，請參閱下列主題：

* [在 Azure 資訊安全中心設定安全性原則](tutorial-security-policy.md) -- 了解如何為您的 Azure 訂用帳戶及資源群組設定安全性原則。
* [管理 Azure 資訊安全中心的安全性建議](security-center-recommendations.md) -- 了解建議如何協助您保護您的 Azure 資源。
* [Azure 資訊安全中心的安全性健全狀況監視](security-center-monitoring.md) -- 了解如何監視 Azure 資源的健全狀況。
* [管理與回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md) -- 了解如何管理與回應安全性警示。
* [使用 Azure 資訊安全中心監視合作夥伴解決方案](security-center-partner-solutions.md) -- 了解如何監視合作夥伴解決方案的健全狀況。

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]:./media/security-center-provide-security-contacts/provide-contact-details.png
