---
title: 使用 Check Point Point cloudguard Connect 保護 Azure 虛擬中樞
description: 瞭解 Check Point Point cloudguard 連線到安全的 Azure 虛擬中樞
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/30/2020
ms.author: victorh
ms.openlocfilehash: 3c61dc689d19e1a7d6f9b6dbefae846e9458d750
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/01/2020
ms.locfileid: "93146853"
---
# <a name="secure-virtual-hubs-using-check-point-cloudguard-connect"></a>使用 Check Point Point cloudguard Connect 保護虛擬中樞

Check Point Point cloudguard Connect 是 Azure 防火牆管理員中受信任的安全性合作夥伴。 它可保護網際網路 (B2I) 或虛擬網路到網際網路 (V2I) 連線，並具備先進的威脅防護。 

使用 Azure 防火牆管理員中的簡單設定時，您可以透過 Point cloudguard Connect security as a service (SECaaS) ，將分支中樞和虛擬網路連線路由至網際網路。 流量會在從您的中樞傳輸至 IPsec VPN 通道中的 Check Point 雲端服務時受到保護。

當您在 Check Point 入口網站中啟用自動同步時，在 Azure 入口網站中標示為 *受保護* 的任何資源都會自動受到保護。 您不需要管理您的資產兩次。 您只需選擇在 Azure 入口網站中保護一次。

Check Point 在一傘之下合併多個安全性服務。 整合式安全性流量會解密一次，並在單一階段中檢查。 應用程式控制、URL 篩選和內容感知 (DLP) 強制執行安全的 web 使用，並保護您的資料。 IP 和防毒軟體會保護使用者免于已知的網路攻擊。 反 Bot 會封鎖與命令和控制伺服器的連線，並在主機受到感染時發出警示。

威脅模擬 (沙箱) 保護使用者免于遭受未知和零時差的威脅。 Check Point Sandblast mobile protect Zero-Day 保護是雲端裝載的沙灘型技術，可讓您快速隔離和檢查檔案。 它會在虛擬沙箱中執行，以在進入您的網路之前探索惡意行為。 它可在損毀完成之前防止威脅，以節省員工寶貴的時間來回應威脅。 

## <a name="deployment-example"></a>部署範例

觀看下列影片，瞭解如何以受信任的 Azure 安全性合作夥伴的形式部署 Check Point Point cloudguard Connect。

> [!VIDEO https://www.youtube.com/embed/C8AuN76DEmU]

## <a name="next-steps"></a>下一步

- [部署安全性合作夥伴提供者](deploy-trusted-security-partner.md)