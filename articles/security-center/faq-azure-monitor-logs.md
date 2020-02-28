---
title: Azure 資訊安全中心常見問題-現有 MMAs 的相關問題
description: 此常見問題會回答已使用 Microsoft Monitoring Agent 的客戶問題，並考慮 Azure 資訊安全中心，這是可協助您預防、偵測及回應威脅的產品。
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
ms.openlocfilehash: 5c433140c3982813e372fd3f63243a96197d220c
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77661884"
---
# 已使用 Azure 監視器記錄之客戶的常見問題<a name="existingloganalyticscust"></a>

## <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>資訊安全中心是否會覆寫任何虛擬機器及工作區之間現有的連線？

如果虛擬機器已經以擴充功能的形式安裝 Microsoft Monitoring Agent，資訊安全中心不會覆寫既存的工作區連線。 相反地，資訊安全中心會使用現有的工作區。 如果「安全性」或「SecurityCenterFree」解決方案已安裝在其所報告的工作區上，VM 將會受到保護。 

資訊安全中心解決方案會安裝在 [資料收集] 畫面中選取的工作區（如果尚未存在），而且解決方案只會套用至相關的 Vm。 當您新增解決方案時，依預設會該解決方案會自動部署到與您 Log Analytics 工作區連線的所有 Windows 與 Linux 代理程式。 [解決方案目標設定](../operations-management-suite/operations-management-suite-solution-targeting.md)讓您能將範圍套用至解決方案。

> [!TIP]
> 如果 Microsoft Monitoring Agent 直接安裝在 VM （而不是 Azure 擴充功能）上，資訊安全中心不會安裝 Microsoft Monitoring Agent，而且安全性監視會受到限制。

## <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>資訊安全中心是否會在我現有的 Log Analytics 工作區上安裝解決方案？ 這會對計費造成什麼影響？
當資訊安全中心發現已經有虛擬機器連線到您建立的工作區時，資訊安全中心會根據您的定價層啟用此工作區上的解決方案。 這些解決方案會透過[方案目標設定](../operations-management-suite/operations-management-suite-solution-targeting.md)，只套用至相關的 Azure 虛擬機器，所以收費不會改變。

- **免費層** – 資訊安全中心在工作區安裝 SecurityCenterFree 解決方案。 免費層不會向您收費。
- **標準層** – 資訊安全中心在工作區安裝 'Security' 解決方案。

   ![預設工作區的解決方案](./media/security-center-platform-migration-faq/solutions.png)

## <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>我的環境中已經有工作區，我是否能使用這些工作區來收集安全性資料？
如果虛擬機器已經以擴充套件的形式安裝 Microsoft Monitoring Agent，資訊安全中心會使用現有已連線的工作區。 資訊安全中心解決方案若尚未安裝，此時會安裝至工作區，且透過[解決方案目標設定](../operations-management-suite/operations-management-suite-solution-targeting.md)，此解決方案只會套用至相關的虛擬機器。

資訊安全中心在虛擬機器上安裝 Microsoft Monitoring Agent 時，會使用資訊安全中心建立的預設工作區。

## <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>我的工作區上已經有安全性解決方案。 這會對計費造成什麼影響？
安全性 & Audit 解決方案可用來啟用 Azure Vm 的資訊安全中心標準層功能。 如果工作區已經安裝有 Security & Audit 解決方案，資訊安全中心會使用現有的解決方案。 收費不會改變。