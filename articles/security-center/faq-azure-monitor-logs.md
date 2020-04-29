---
title: Azure 資訊安全中心常見問題-關於現有 Log Analytics 代理程式的問題
description: 此常見問題會針對已經使用 Log Analytics 代理程式的客戶和考慮 Azure 資訊安全中心（可協助您預防、偵測及回應威脅的產品），回答問題。
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
ms.openlocfilehash: f6384c1e9e14e38b4c44c5ac79a674839b43b4ca
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80436157"
---
# <a name="faq-for-customers-already-using-azure-monitor-logs"></a>已使用 Azure 監視器記錄之客戶的常見問題<a name="existingloganalyticscust"></a>

## <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>資訊安全中心是否會覆寫任何虛擬機器及工作區之間現有的連線？

如果 VM 已將 Log Analytics 代理程式安裝為 Azure 擴充功能，資訊安全中心不會覆寫現有的工作區連接。 相反地，資訊安全中心會使用現有的工作區。 如果「安全性」或「SecurityCenterFree」解決方案已安裝在其所報告的工作區上，VM 將會受到保護。 

資訊安全中心解決方案會安裝在 [資料收集] 畫面中選取的工作區（如果尚未存在），而且解決方案只會套用至相關的 Vm。 當您新增解決方案時，依預設會該解決方案會自動部署到與您 Log Analytics 工作區連線的所有 Windows 與 Linux 代理程式。 [解決方案目標設定](../operations-management-suite/operations-management-suite-solution-targeting.md)讓您能將範圍套用至解決方案。

> [!TIP]
> 如果 Log Analytics 代理程式是直接安裝在 VM 上（而不是 Azure 擴充功能），資訊安全中心不會安裝 Log Analytics 代理程式，而且安全性監視會受到限制。

## <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>資訊安全中心是否會在我現有的 Log Analytics 工作區上安裝解決方案？ 這會對計費造成什麼影響？
當資訊安全中心發現已經有虛擬機器連線到您建立的工作區時，資訊安全中心會根據您的定價層啟用此工作區上的解決方案。 這些解決方案會透過[方案目標設定](../operations-management-suite/operations-management-suite-solution-targeting.md)，只套用至相關的 Azure 虛擬機器，所以收費不會改變。

- **免費層** – 資訊安全中心在工作區安裝 SecurityCenterFree 解決方案。 免費層不會向您收費。
- **標準層** – 資訊安全中心在工作區安裝 'Security' 解決方案。

   ![預設工作區的解決方案](./media/security-center-platform-migration-faq/solutions.png)

## <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>我的環境中已經有工作區，我是否能使用這些工作區來收集安全性資料？
如果 VM 已將 Log Analytics 代理程式安裝為 Azure 擴充功能，資訊安全中心會使用現有已連線的工作區。 資訊安全中心解決方案若尚未安裝，此時會安裝至工作區，且透過[解決方案目標設定](../operations-management-suite/operations-management-suite-solution-targeting.md)，此解決方案只會套用至相關的虛擬機器。

當資訊安全中心在 Vm 上安裝 Log Analytics 代理程式時，它會使用資訊安全中心所建立的預設工作區。

## <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>我的工作區上已經有安全性解決方案。 這會對計費造成什麼影響？
安全性 & Audit 解決方案可用來啟用 Azure Vm 的資訊安全中心標準層功能。 如果工作區已經安裝有 Security & Audit 解決方案，資訊安全中心會使用現有的解決方案。 收費不會改變。