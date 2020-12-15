---
title: Microsoft 對等互連原則
titleSuffix: Azure
description: Microsoft 對等互連原則
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: conceptual
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 20f25e0add5d05bb2dcf7f3ebdc86ccd5ae889d0
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2020
ms.locfileid: "97510782"
---
# <a name="peering-policy"></a>對等互連原則
Microsoft 會維護一個選擇性的對等互連原則，其設計目的是為了確保客戶體驗的最佳體驗，可支援業界標準和最佳作法，並根據未來需求和策略性的對等互連來進行調整。 因此，Microsoft 保留在必要時對原則提出例外狀況的權利。 下列各節將說明來自您網路的 Microsoft 一般需求。 這些都適用于直接對等互連和交換對等互連要求。 

## <a name="technical-requirements"></a>技術需求

* 具有足夠容量可在沒有擁塞的情況下交換流量的完全多餘網路。
* 對等會有可公開路由傳送的自發系統編號 (ASN) 。
* 支援 IPv4 和 IPv6，而且 Microsoft 預期會在每個對等互連位置中建立這兩種類型的會話。
* 不支援 MD5。
* **ASN 詳細資料：**

    * Microsoft 會管理 AS8075 以及下列 Asn： AS8068、AS8069、AS12076。 如需 Asn 與 AS8075 對等互連的完整清單，請參考 AS-SET MICROSOFT。
    * 所有與 Microsoft 的對等互連都同意不接受來自 AS12076 (Express Route) 在任何情況下的路由，並應在所有對等上篩選出 AS12076。

* **路由原則：**
    * 對等將至少有一個可公開路由傳送/24。
    * Microsoft 將會覆寫已收到的多結束鑒別子 (MED-V) 。
    * Microsoft 偏好接收來自對等的 BGP 社區標記，以指出路由來源。
    * 我們建議對等互連將最大前置詞設為 2000 (IPv4) 和 500 (IPv6) 與 Microsoft 對等互連會話的路由。
    * 除非事先同意，否則對等應會在與 Microsoft 對等的所有位置中宣告一致的路由。
    * 一般來說，使用 AS8075 的對等互連會話將通告所有的 MICROSOFT 路由。 Microsoft 可能會宣佈一些區域細節。
    * 這兩個合作物件都不會建立靜態路由、最後手段的路由，或針對未透過 BGP 宣告的路由，將流量傳送給另一方。
    * 對等互連必須在公用網際網路路由登錄 (IRR) 資料庫中註冊其路由，以進行篩選，並將這項資訊保持在最新狀態。      
    * 對等將遵守適用于路線安全性的 MANRS 產業標準。  Microsoft 可自行斟酌選擇： 1. ) 不會與未簽署及註冊路由的公司建立對等互連;2. ) 移除不正確 RPKI 路由;3. ) 不接受未登錄和簽署之已建立對等的路由。 

## <a name="operational-requirements"></a>營運需求
* 全公司員工的全天候網路作業中心 (的 NOC) ，能夠協助解決所有技術和效能問題、安全性違規、阻絕服務攻擊，或是源自于對等或客戶的任何其他濫用。
* 對等應該會在 [PeeringDB](https://www.peeringdb.com) 上擁有完整且最新的設定檔，包括來自公司網域與電話號碼的全天候 NOC 電子郵件。 我們會使用此資訊來驗證對等的詳細資料，例如 NOC 資訊、技術連絡人資訊，以及它們在對等互連設備上的存在等。不接受個人 Yahoo、Gmail 和 hotmail 帳戶。

## <a name="physical-connection-requirements"></a>實體連接需求
* 您可以與 Microsoft 連線以進行直接對等互連或 Exchange 對等互連的位置列于 [PeeringDB](https://www.peeringdb.com/net/694)

* **交換對等互連：**
    * 對等的最少應有 10 Gb 的交換連接。
    * 當尖峰使用率超過50% 時，對等應會升級其埠。
    * Microsoft 鼓勵同事保有不同的 exchange 連線能力，以支援容錯移轉案例。

* **直接對等互連：**
    * 互相連線必須使用 100 Gbps 光學光纖的單一模式光纖。
    * Microsoft 只會與 ISP 或網路服務提供者建立直接對等互連。
    * 當尖峰使用率超過50%，而且在每個 metro 中維護不同的容量時（不論是單一位置或 metro 中的多個位置），對等端都應該升級其埠。
    * 每個直接對等互連都包含兩個從對等的路由器（位於對等邊緣的路由器）連線至兩個 Microsoft edge 路由器的連接。 Microsoft 需要跨這些連線的雙重 BGP 會話。 對等可能會選擇不在其結尾部署多餘的裝置。


## <a name="traffic-requirements"></a>流量需求

* 交換對等互連的對等必須至少有 500 Mb 的流量，且小於 2 Gb。 應建立超過 2 Gb 直接對等互連的流量。
* Microsoft 需要至少 2 Gb 的直接對等互連。 每個對等互連位置的雙方都必須支援容錯移轉，以確保在容錯移轉的情況下對等互連保持當地語系化。 

## <a name="next-steps"></a>後續步驟

* 若要瞭解設定與 Microsoft 直接對等互連的步驟，請遵循 [直接對等互連逐步](walkthrough-direct-all.md)解說。
* 若要瞭解設定與 Microsoft 交換對等互連的步驟，請遵循 [Exchange 對等互連逐步](walkthrough-exchange-all.md)解說。
