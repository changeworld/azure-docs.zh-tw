---
title: 針對連線進行疑難排解-超大規模 (Citus) -適用於 PostgreSQL 的 Azure 資料庫
description: '瞭解如何對適用於 PostgreSQL 的 Azure 資料庫的連接問題進行疑難排解-超大規模 (Citus) '
keywords: postgresql 連線, 連接字串, 連線問題, 暫時性錯誤, 連線錯誤
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 10/8/2019
ms.openlocfilehash: e1c6825820ae943d10157279dfe93922a7521b75
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91295612"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-postgresql---hyperscale-citus"></a>針對適用於 PostgreSQL 的 Azure 資料庫-超大規模 (Citus) 的連接問題進行疑難排解

連接問題可能是由數個專案所造成，例如：

* 防火牆設定
* 連線逾時
* 不正確的登入資訊
* 已達到伺服器群組的連接限制
* 服務基礎結構發生問題
* 服務維護
* 協調器節點容錯移轉至新的硬體

一般而言，超大規模 (Citus) 的連接問題可分類如下：

* 暫時性錯誤 (短期或間歇性)
* 持續性或非暫時性錯誤 (定期重複發生的錯誤)

## <a name="troubleshoot-transient-errors"></a>針對暫時性錯誤進行疑難排解

發生暫時性錯誤的原因有很多。 最常見的包括系統維護、硬體或軟體錯誤，以及協調員節點 vCore 升級。

啟用超大規模 (Citus) 伺服器群組節點的高可用性，可自動減輕這些類型的問題。 不過，您的應用程式仍應做好準備，以便短暫地中斷其連接。 此外，其他事件可能需要較長的時間才能減輕問題，例如當大型交易造成長時間執行的復原時。

### <a name="steps-to-resolve-transient-connectivity-issues"></a>解決暫時性連線問題的步驟

1. 檢查 [Microsoft Azure 服務儀表板](https://azure.microsoft.com/status) ，找出應用程式回報錯誤期間發生的任何已知中斷。
2. 連接至雲端服務的應用程式（例如超大規模 (Citus) ）應該會預期暫時性的錯誤並正常回應。 例如，應用程式應該執行重試邏輯來處理這些錯誤，而不是將它們呈現為使用者的應用程式錯誤。
3. 當伺服器群組接近其資源限制時，錯誤可能看似暫時性的連線問題。 增加節點 RAM 或新增背景工作節點，以及重新平衡資料可能會有所説明。
4. 如果連線問題持續發生，或持續超過60秒，或每天發生一次以上，請選取[Azure 支援](https://azure.microsoft.com/support/options)網站上的 [**取得支援**]，提出 Azure 支援要求。

## <a name="troubleshoot-persistent-errors"></a>針對持續性錯誤進行疑難排解

如果應用程式持續無法連線到超大規模 (Citus) ，最常見的原因是防火牆設定錯誤或使用者錯誤。

* 協調員節點防火牆設定：確定已將超大規模 (Citus) 伺服器防火牆設定為允許來自用戶端的連線，包括 proxy 伺服器和閘道。
* 用戶端防火牆設定：用戶端上的防火牆必須允許連線至您的資料庫伺服器。 某些防火牆需要不只允許依名稱來允許應用程式，但允許伺服器的 IP 位址和埠。
* 使用者錯誤：請仔細檢查連接字串。 您可能會有輸入錯誤的參數，例如伺服器名稱。 您可以在 Azure 入口網站中找到各種語言架構和 psql 的連接字串。 移至超大規模 (Citus) 伺服器群組中的 [ **連接字串** ] 頁面。 也請記住，超大規模 (Citus) 叢集只有一個資料庫，且其預先定義的名稱為 **Citus**。

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>解決永久性連線問題的步驟

1. 設定 [防火牆規則](howto-hyperscale-manage-firewall-using-portal.md) 以允許用戶端 IP 位址。 (僅適用於臨時性的測試目的) 請使用 0.0.0.0 作為起始 IP 位址並使用 255.255.255.255 作為結束 IP 位址來設定防火牆規則。 該規則會對所有 IP 位址開啟伺服器。 如果此規則會解決您的連線問題，請將它移除，並針對適當限制的 IP 位址或位址範圍建立防火牆規則。
2. 在用戶端與網際網路之間的所有防火牆上，請確定已開啟埠5432供輸出連線。
3. 請確認您的連接字串和其他連線設定。
4. 檢查儀表板中的服務健康情況。

## <a name="next-steps"></a>後續步驟

* 瞭解 [適用於 PostgreSQL 的 Azure 資料庫超大規模 (Citus 中的防火牆規則概念) ](concepts-hyperscale-firewall-rules.md)
* 請參閱如何 [管理適用於 PostgreSQL 的 Azure 資料庫超大規模 (Citus 的防火牆規則) ](howto-hyperscale-manage-firewall-using-portal.md)
