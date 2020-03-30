---
title: Azure 安全基準概述
description: 安全基準概述
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: mbaldwin
ms.custom: security-baselines
ms.openlocfilehash: 2f4b034948605b0a53a0320863608d284719a96d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587527"
---
# <a name="overview-of-the-azure-security-benchmark"></a>Azure 安全基準概述

Azure 安全基準包含可説明您提高 Azure 上應用程式和資料安全性的建議。

此基準測試側重于以雲為中心的控制區域。 這些控制項與眾所周知的安全基準一致，例如互聯網安全中心 （CIS） 控制版本 7.1 中描述的基準。

Azure 安全基準測試中使用了以下控制項： 

- [網路安全性](security-control-network-security.md)
- [日誌記錄和監視](security-control-logging-monitoring.md)
- [身分識別與存取控制](security-control-identity-access-control.md)
- [資料保護](security-control-data-protection.md)
- [弱點管理](security-control-vulnerability-management.md)
- [清查和資產管理](security-control-inventory-asset-management.md)
- [安全設定](security-control-secure-configuration.md)
- [惡意程式碼防禦](security-control-malware-defense.md)
- [資料復原](security-control-data-recovery.md)
- [事件回應](security-control-incident-response.md)
- [滲透測試和 Red Team 練習](security-control-penetration-tests-red-team-exercises.md)

您還可以下載 Azure[安全基準 v1 excel 試算表](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/spreadsheets)。

## <a name="azure-security-benchmark-recommendations"></a>Azure 安全基準建議 

每個建議包括以下資訊： 

- **Azure ID**：對應于建議的 Azure 安全基準 ID。 
- **獨聯體 ID：** 符合本建議的獨聯體基準建議。  
- **責任**：無論是客戶還是服務提供者（或兩者）都負責實施此建議。 安全責任在公共雲中共用。 某些安全控制僅對雲服務提供者可用，因此供應商負責解決這些問題。 這些是一般性觀察 - 對於某些單個服務，其責任將不同于 Azure 安全基準測試中列出的責任。 這些差異在個別服務的基線建議中描述。 
- **詳情**：建議的理由及如何實施該建議的指導。 如果 Azure 安全中心支援該建議，該資訊也將列出。

我們歡迎您提供詳細回饋並積極參與 Azure 安全基準工作。 如果您想提供基準團隊直接輸入，請在 上[https://aka.ms/AzSecBenchmark](https://aka.ms/AzSecBenchmark)填寫表格。

## <a name="next-steps"></a>後續步驟

- 請參閱第一個安全控制：[網路安全](security-control-network-security.md)
- 閱讀[Azure 安全基準簡介](introduction.md)
- 下載[Azure 安全基準 v1 Excel 試算表](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/spreadsheets)
