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
ms.openlocfilehash: 64a9600a3014f7e85a7f924d38882bfadaf631db
ms.sourcegitcommit: d0fd35f4f0f3ec71159e9fb43fcd8e89d653f3f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2020
ms.locfileid: "80387813"
---
# <a name="provide-security-contact-details-in-azure-security-center"></a>在 Azure 資訊安全中心提供安全性連絡人詳細資料
Azure 資訊安全中心會建議您為您的 Azure 訂用帳戶提供安全性連絡人詳細資料 (如果您還沒有這麼做)。 如果 Microsoft 安全性回應中心 (MSRC) 發現您的客戶資料遭到非法或未經授權的對象存取，Microsoft 會使用此資訊連絡您。 MSRC 執行 Azure 網路和基礎結構的選取安全性監視，並接收來自協力廠商的威脅情報和濫用客訴。

在每天第一個警示發生時且僅針對高嚴重性警示，才會傳送電子郵件通知。 電子郵件喜好設定只能針對訂用帳戶原則設定。 在訂用帳戶內的資源群組會繼承這些設定。 警報僅在 Azure 安全中心的"標準層"中可用。

所傳送的警示電子郵件通知：
- 每日傳送給各警示類型的一個電子郵件收件者  
- 在一天內不會傳送給單一收件者超過 3 個電子郵件訊息
- 每個電子郵件訊息均包含單一警示，而非警示的彙總
- 僅針對高嚴重性的警示傳送

> [!TIP]
> 對於具有其他嚴重性級別的警報，創建[工作流自動化](workflow-automation.md)以使用將電子郵件發送給相關人員的邏輯應用。
 
例如，如果電子郵件訊息已傳送警示表示您即將遭受 RDP 攻擊，您在同一天內不會收到另一個表示您即將遭受 RDP 攻擊的電子郵件訊息，即使是另一個警示已觸發。 

> [!NOTE]
> 本文件將使用範例部署來介紹服務。  這不是逐步指南。

## <a name="set-up-email-notifications-for-alerts"></a>為警報設置電子郵件通知<a name="email"></a>

1. 打開**電子郵件通知**頁面：

    - 對於警報，打開**定價&設置**，選擇相關訂閱，並選擇**電子郵件通知**。

    - 如果要實施建議，請在 **"建議"** 下選擇"**提供安全連絡人詳細資訊**"，選擇 Azure 訂閱以提供其聯繫資訊。 這會開啟 [電子郵件通知]****。

   ![提供安全性連絡人詳細資料][2]

1. 輸入安全性連絡人的電子郵件地址，若有多個則以逗號分隔。 您可以輸入的電子郵件地址數量沒有限制。

1. 若要接收有關高嚴重性警示的電子郵件，請開啟 [傳送給我有關警示的電子郵件] **** 選項。 對於其他嚴重性級別，請使用[工作流自動化](workflow-automation.md)中所述的邏輯應用。

1. 您可以向訂閱擁有者（經典服務管理員和共同管理員，以及訂閱範圍內的 RBAC 擁有者角色）發送電子郵件通知。

1. 要將安全聯繫資訊應用於訂閱，請選擇"**保存**"。

## <a name="see-also"></a>另請參閱
如要深入了解資訊安全中心，請參閱下列主題：

* [在 Azure 安全中心設置安全性原則](tutorial-security-policy.md)-- 瞭解如何為 Azure 訂閱和資源組配置安全性原則。
* [在 Azure 安全中心管理安全建議](security-center-recommendations.md)-- 瞭解建議如何説明保護 Azure 資源。
* [Azure 安全中心中的安全運行狀況監視](security-center-monitoring.md)-- 瞭解如何監視 Azure 資源的運行狀況。
* [管理和回應 Azure 安全中心中的安全警報](security-center-managing-and-responding-alerts.md)-- 瞭解如何管理和回應安全警報。
* [使用 Azure 資訊安全中心監視合作夥伴解決方案](security-center-partner-solutions.md) -- 了解如何監視合作夥伴解決方案的健全狀況。

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]:./media/security-center-provide-security-contacts/provide-contact-details.png
