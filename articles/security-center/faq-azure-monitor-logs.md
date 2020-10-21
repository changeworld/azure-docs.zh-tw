---
title: Azure 資訊安全中心常見問題-現有的 Log Analytics 代理程式相關問題
description: 此常見問題會回答已使用 Log Analytics 代理程式並考慮 Azure 資訊安全中心（可協助您防止、偵測及回應威脅的產品）的客戶問題。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 3a2854f4124a570de4fb467eb6a5d49a3214742e
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92341816"
---
# <a name="faq-for-customers-already-using-azure-monitor-logs"></a>已使用 Azure 監視器記錄的客戶常見問題<a name="existingloganalyticscust"></a>

## <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>資訊安全中心是否會覆寫任何虛擬機器及工作區之間現有的連線？

如果 VM 已安裝 Log Analytics 代理程式作為 Azure 擴充功能，則安全性中心不會覆寫現有的工作區連接。 相反地，資訊安全中心會使用現有的工作區。 VM 將受到保護，前提是「安全性」或「SecurityCenterFree」解決方案已安裝在其所報告的工作區上。 

在 [資料收集] 畫面中選取的工作區上安裝資訊安全中心解決方案（如果尚未存在），且解決方案只會套用至相關的 Vm。 當您新增解決方案時，依預設會該解決方案會自動部署到與您 Log Analytics 工作區連線的所有 Windows 與 Linux 代理程式。 [解決方案目標設定](../azure-monitor/insights/solution-targeting.md)讓您能將範圍套用至解決方案。

> [!TIP]
> 如果 Log Analytics 代理程式是直接安裝在 VM (而不是 Azure 擴充) ，資訊安全中心不會安裝 Log Analytics 代理程式，且安全性監視會受到限制。

## <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>資訊安全中心是否會在我現有的 Log Analytics 工作區上安裝解決方案？ 這會對計費造成什麼影響？
當資訊安全中心發現 VM 已連線到您所建立的工作區時，資訊安全中心會根據您的定價設定，在此工作區上啟用解決方案。 這些解決方案會透過[方案目標設定](../azure-monitor/insights/solution-targeting.md)，只套用至相關的 Azure 虛擬機器，所以收費不會改變。

- **Azure Defender 關閉** –資訊安全中心會在工作區上安裝 ' SecurityCenterFree ' 解決方案。 您將不會向您收費。
- **Azure Defender on** –資訊安全中心會在工作區上安裝「安全性」解決方案。

   ![預設工作區的解決方案](./media/security-center-platform-migration-faq/solutions.png)

## <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>我的環境中已經有工作區，我是否能使用這些工作區來收集安全性資料？
如果 VM 已安裝 Log Analytics 代理程式作為 Azure 擴充功能，則「安全性中心」會使用現有的連線工作區。 資訊安全中心解決方案若尚未安裝，此時會安裝至工作區，且透過[解決方案目標設定](../azure-monitor/insights/solution-targeting.md)，此解決方案只會套用至相關的虛擬機器。

當資訊安全中心在 Vm 上安裝 Log Analytics 代理程式時，如果資訊安全中心未指向現有的工作區，則會使用資訊安全中心所建立的預設工作區 () 。

## <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>我的工作區上已經有安全性解決方案。 這會對計費造成什麼影響？
安全性 & Audit 解決方案可用來啟用適用于 **伺服器的 Azure Defender**。 如果工作區已經安裝有 Security & Audit 解決方案，資訊安全中心會使用現有的解決方案。 收費不會改變。