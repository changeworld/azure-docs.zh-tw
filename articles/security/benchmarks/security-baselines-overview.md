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
ms.openlocfilehash: c16d7247b781fea04cfa2d53b8854cff14e039c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616439"
---
# <a name="overview-of-azure-security-baselines"></a>Azure 安全基線概述

Azure 安全基線通過改進的工具、跟蹤和安全功能説明您增強產品的安全性，並在保護環境時為您提供一致的體驗。

Azure 服務安全基線側重于以雲為中心的控制區域。 這些控制措施與眾所周知的安全基準（如互聯網安全中心 （CIS） 描述的安全基準一致。 我們的基線為[Azure 安全基準](overview.md)中列出的控制區域提供了指導。

每個建議包括以下資訊：
- **Azure ID**：對應于建議的 Azure 安全基準 ID。
- **建議**：直接遵循 Azure ID 後，該建議提供控制項的高級說明。
- **指導**：建議的理由和如何實施該建議的指導的連結。 如果 Azure 安全中心支援該建議，該資訊也將列出。
- **責任**：誰負責實施控制。 可能的情況是客戶責任、微軟責任或分擔責任。
- **Azure 安全中心監視**：控制項是否由 Azure 安全中心監視，並帶有指向引用的連結。

所有建議（包括不適用於此特定服務的建議）都包含在基線中，以便全面瞭解 Azure 安全基準與每個服務的關係。 有時可能有一些控制項由於各種原因不適用，例如，IaaS/以計算為中心的控制項（例如特定于作業系統建構管理的控制項）可能不適用於 PaaS 服務。
