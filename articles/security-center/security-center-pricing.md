---
title: Azure 安全中心層的定價
description: Azure 安全中心提供兩層 - 免費和標準。 此頁演示如何從免費升級到標準版。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/13/2019
ms.author: memildin
ms.openlocfilehash: 60a88e667918533f2c507846fa75b0e036ba5262
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921278"
---
# <a name="upgrade-to-standard-tier-for-enhanced-security"></a>升級到標準層，增強安全性
Azure 資訊安全中心提供統一的安全性管理和進階的威脅防護，保護 Azure、內部部署和其他雲端中執行的工作負載。 其提供了針對混合式雲端工作負載的可見性和控制能力、可降低威脅曝露度的主動防禦措施、還有智慧型偵測可幫助您跟上網路攻擊快速發展的腳步。

## <a name="pricing-tiers"></a>定價層
資訊安全中心提供兩個層級：

- 首次訪問 Azure 門戶中的 Azure 安全中心儀表板，或者通過 API 以程式設計方式啟用，則在所有 Azure 訂閱上啟用**免費**層。 免費套餐提供安全性原則、持續安全評估和可操作的安全建議，以説明您保護 Azure 資源。
- **標準**層將免費層的功能擴展到在私人雲端和其他公用雲端中執行的工作負載，為您的混合式雲端工作負載提供統一的安全性管理和威脅防護。 標準層還添加了威脅防護功能，這些功能使用內置的行為分析和機器學習來識別攻擊和零日漏洞利用、訪問和應用程式控制，以減少遭受網路攻擊和惡意軟體的風險等。 此外，標準層還增加了虛擬機器的漏洞掃描。 您可以免費試用標準層。 安全中心標準支援 Azure 資源，包括 VM、虛擬機器規模集、應用服務、SQL 伺服器和存儲帳戶。 如果您有 Azure 安全中心標準，則可以根據資源類型退出宣告支援。 

大多數 VM 的免費層安全評估以及許多標準層安全警報都需要安裝 Microsoft 監視代理 （MMA） 功能。 您可以啟用安全中心的自動設定，以自動部署 Azure VM 的代理。

## <a name="try-standard-tier-free-for-30-days"></a>免費試用標準套餐 30 天
標準套餐在前 30 天免費。 在 30 天結束時，如果您選擇繼續使用服務，我們將自動開始根據使用量計費。

您可以將整個 Azure 訂閱升級到標準層，該層由訂閱中的所有資源繼承。

要獲取標準層：

1. 在**安全中心**主功能表上選擇**定價&設置**。
2. 選擇要升級到標準的訂閱。
3. 選擇**定價層**。
4. 選取 [標準版]**** 以進行升級。
5. 按一下 [儲存]****。

[![安全中心定價](media/security-center-pricing/pricing-tier-page.png)](media/security-center-pricing/pricing-tier-page.png#lightbox)

> [!NOTE]
> 要啟用所有安全中心功能，必須將標準定價層應用於包含適用虛擬機器的訂閱。 配置工作區的定價不會為 Azure 資源啟用即時 VM 訪問、自我調整應用程式控制項和網路檢測。
>

## <a name="why-upgrade-to-standard"></a>為什麼要升級到標準版？
資訊安全中心為您的混合式雲端工作負載提供增強的安全性與威脅防護，包括：

- **混合式安全性** – 取得您所有內部部署和雲端工作負載中安全性的統一檢視。 套用安全性原則，並持續評估您混合式雲端工作負載的安全性，以確保符合安全性標準。 從多個來源（包括防火牆和其他合作夥伴解決方案）收集、搜索和分析安全資料。
- **安全警報**- 使用高級分析和 Microsoft 智慧安全圖，在不斷演變的網路攻擊中獲得優勢。 有效率的調控內建行為分析和機器學習服務，以發現攻擊和零時差惡意探索。 監視網路、機器和雲端服務中是否有傳入攻擊和侵入後活動。 使用互動式工具和內容相關威脅情報來簡化調查。
- **虛擬機器的漏洞掃描**- 輕鬆將掃描器部署到所有虛擬機器，為漏洞管理提供業界最先進的解決方案。 直接在安全中心內查看、調查和修復調查結果。 
- **訪問和應用程式控制**- 通過應用適合您特定工作負載的機器學習支援白名單建議來阻止惡意軟體和其他不需要的應用程式。 通過即時、受控地訪問 Azure VM 上的管理埠，減少網路攻擊表面。 這大大減少了暴力和其他網路攻擊的暴露。
- **容器安全功能**- 受益于容器化環境中的漏洞管理和即時威脅保護。 啟用容器註冊表資源時，可能需要長達 12 小時，直到啟用所有功能。


## <a name="next-steps"></a>後續步驟
在本文中，已向您介紹資訊安全中心的價格。 若要深入了解標準層的增強式安全性和進階威脅防護，請參閱：

- [Azure 安全中心中的威脅保護](threat-protection.md)
- [及時的 VM 存取控制](security-center-just-in-time.md)
- [容器安全性概觀](container-security.md)
- [您選擇的貨幣定價詳細資訊，並根據您的地區](https://azure.microsoft.com/pricing/details/security-center/)