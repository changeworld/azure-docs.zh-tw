---
title: Microsoft Azure 中的雙重加密
description: 本文說明 Microsoft Azure 如何為待用資料和傳輸中的資料提供雙重加密。
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/13/2020
ms.author: terrylan
ms.openlocfilehash: 020e8249f57ccb1a14da798a717a00dcc3962389
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2020
ms.locfileid: "88227062"
---
# <a name="double-encryption"></a>雙重加密
雙重加密是指啟用兩個或多個獨立層級的加密，以防止任何一層加密的危害。 使用兩層加密可減少加密資料所帶來的威脅。 例如：

- 資料加密中的設定錯誤
- 加密演算法中的實作為錯誤
- 洩露單一加密金鑰

Azure 會針對待用資料和傳輸中的資料提供雙重加密。

## <a name="data-at-rest"></a>待用資料
Microsoft 為待用資料啟用兩層加密的方法是：

- **使用客戶管理的金鑰進行磁片加密**。 您會提供您自己的金鑰來進行磁片加密。 您可以將自己的金鑰帶入您的 Key Vault (BYOK –攜帶您自己的金鑰) ，或在 Azure Key Vault 中產生新的金鑰，以加密所需的資源。
- **使用平臺管理金鑰的基礎結構加密**。  根據預設，系統會使用平臺管理的加密金鑰，在待用時自動加密磁片。

## <a name="data-in-transit"></a>資料傳輸中
Microsoft 為傳輸中的資料啟用兩層加密的方法為：

- **使用傳輸層安全性 (TLS) 1.2 傳輸加密，以在雲端服務和您之間進行時保護資料**。 所有離開資料中心的流量都會在傳輸中加密，即使流量目的地是相同區域中的另一個網域控制站也一樣。 TLS 1.2 是所使用的預設安全性通訊協定。 TLS 提供增強式驗證、訊息隱私權、完整性 (可偵測訊息竄改、攔截和偽造)、互通性、演算法彈性，以及方便部署和使用。
- 在**基礎結構層提供的額外加密層級**。 使用 IEEE 802.1 AE MAC 安全性標準的資料連結層加密方法 (也稱為 MACsec) 會在基礎網路硬體的點對點應用。 每當 Azure 客戶流量在資料中心之間移動時（不受 Microsoft (或代表 Microsoft) 控制的外部實體界限），封包會在傳送之前先在裝置上加密和解密，以防止實體「攔截」或窺探/wiretapping 攻擊。 由於這項技術是與網路硬體本身整合，因此在網路硬體上提供線路速率加密，而不會增加任何可測量的連結延遲。 針對某個區域內或區域間的所有 Azure 流量，此 MACsec 加密預設為開啟，而且客戶不需要採取任何動作即可啟用。

## <a name="next-steps"></a>後續步驟
瞭解如何 [在 Azure 中使用加密](encryption-overview.md)。
