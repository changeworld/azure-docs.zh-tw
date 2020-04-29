---
title: 使用 Azure 資訊安全中心來加強安全性狀態 | Microsoft Docs
description: 本文可協助您透過在「Azure 資訊安全中心」中監視資源來加強安全性狀態。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 3bd5b122-1695-495f-ad9a-7c2a4cd1c808
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: memildin
ms.openlocfilehash: 40352b7ae1f3fb6b1178f2dfe70cdca9871c76b7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77603779"
---
# <a name="strengthen-your-security-posture-with-azure-security-center"></a>使用 Azure 資訊安全中心來加強安全性狀態
本文可協助您加強安全性狀態。 您可以使用「Azure 資訊安全中心」中的監視功能來確保您的資源安全性儘可能嚴密周到，並監視是否符合原則規範。

## <a name="how-do-you-strengthen-your-security-posture"></a>如何加強您的安全性狀態？
我們通常都將監視想像成監看及等候事件發生，以便對該情況作出反應。 加強安全性狀態係指擁有一個可稽核資源的主動策略，以識別出不符合組織標準或最佳做法的系統。

在您為訂用帳戶的資源啟用[安全性原則](tutorial-security-policy.md)之後，資訊安全中心會分析您資源的安全性狀態，以找出潛在的弱點。 您可以立即取得網路組態的相關資訊。 視您已安裝代理程式的 VM 和電腦數目而定，可能需要一個小時以上的時間才能收集有關 VM 和電腦的組態資訊，例如安全性更新狀態和作業系統組態。 您可以在 [建議]**** 圖格中檢視問題與強化網路與補救風險的完整清單。

您可以在檢視每個資源類型的資源安全性狀態與任何問題：

- 若要監視您電腦資源與應用程式的健康情況，並接收有關如何改進其安全性的建議，請參閱[保護 Azure 資訊安全中心內的機器和應用程式](security-center-virtual-machine-protection.md)
- 若要監視您的網路資源 (例如虛擬機器、網路安全性群組與端點) 並接收有關如何改進其安全性的建議，請參閱[保護 Azure 資訊安全中心內的網路](security-center-network-recommendations.md)以取得詳細資訊。 
- 若要監視您的資料與儲存體資源 (例如 SQL 伺服器與儲存體帳戶) 並接收有關如何改進其安全性的建議，請參閱[保護 Azure 資訊安全中心內的 Azure SQL 服務和資料](security-center-sql-service-recommendations.md)以取得詳細資訊。 
- 若要監視您的身分識別與存取資源 (包括 MFA 與帳戶權限) 並接收有關如何改進其安全性的建議，請參閱[在 Azure 資訊安全中心監視身分識別和存取](security-center-identity-access.md)以取得詳細資訊。 
- 若要監視對您資源的即時存取，請參閱[使用即時管理虛擬機器存取](security-center-just-in-time.md)以取得詳細資訊。 


如需如何套用建議的詳細資訊，請參閱[實作 Azure 資訊安全中心的安全性建議](security-center-recommendations.md)。



![資源安全性健康情況圖格](./media/security-center-monitoring/security-center-monitoring-fig1-newUI-2017.png)



## <a name="see-also"></a>請參閱
在本文中，您已來了解如何使用「Azure 資訊安全中心」的監視功能。 若要深入了解「Azure 資訊安全中心」，請參閱下列主題：

* [在 Azure 資訊安全中心設定安全性原則](tutorial-security-policy.md)：了解如何在 Azure 資訊安全中心設定安全性設定。
* [管理與回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md)：了解如何管理與回應安全性警示。
* [使用 Azure 資訊安全中心監視合作夥伴解決方案](security-center-partner-solutions.md)：了解如何監視合作夥伴解決方案的健全狀況。