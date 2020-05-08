---
title: 針對連接進行疑難排解-超大規模資料庫（Citus）-適用於 PostgreSQL 的 Azure 資料庫
description: 瞭解如何對適用於 PostgreSQL 的 Azure 資料庫的連線問題進行疑難排解-超大規模資料庫（Citus）
keywords: postgresql 連線, 連接字串, 連線問題, 暫時性錯誤, 連線錯誤
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 10/8/2019
ms.openlocfilehash: abcc979bb36fe0acb663a511875a1e186c2c0739
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82583960"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-postgresql---hyperscale-citus"></a>對適用於 PostgreSQL 的 Azure 資料庫的連線問題進行疑難排解-超大規模資料庫（Citus）

連線問題可能是由數個專案所造成，例如：

* 防火牆設定
* 連線逾時
* 不正確的登入資訊
* 已達到伺服器群組的連接限制
* 服務基礎結構發生問題
* 服務維護
* 協調器節點容錯移轉至新硬體

一般來說，超大規模資料庫的連接問題可以分類如下：

* 暫時性錯誤 (短期或間歇性)
* 持續性或非暫時性錯誤 (定期重複發生的錯誤)

## <a name="troubleshoot-transient-errors"></a>針對暫時性錯誤進行疑難排解

暫時性錯誤發生的原因有很多。 最常見的包括系統維護、硬體或軟體錯誤，以及協調器節點 vCore 升級。

啟用超大規模資料庫伺服器群組節點的高可用性，可以自動減輕這些類型的問題。 不過，您的應用程式仍應準備好暫時失去其連接。 此外，其他事件可能需要較長的時間來緩和，例如當大型交易造成長時間執行的復原時。

### <a name="steps-to-resolve-transient-connectivity-issues"></a>解決暫時性連線問題的步驟

1. 檢查 [ [Microsoft Azure 服務儀表板](https://azure.microsoft.com/status)]，找出應用程式報告錯誤期間發生的任何已知中斷。
2. 連接到雲端服務（例如超大規模資料庫（Citus））的應用程式應該會預期發生暫時性錯誤，並正常做出反應。 例如，應用程式應該執行重試邏輯來處理這些錯誤，而不是將它們呈現為使用者的應用程式錯誤。
3. 當伺服器群組接近其資源限制時，錯誤看起來可能像是暫時性的連線問題。 增加節點 RAM，或新增背景工作節點和重新平衡資料可能會有説明。
4. 如果連線問題持續，或過去超過60秒，或每天發生一次以上，請在[Azure 支援](https://azure.microsoft.com/support/options)網站上選取 [**取得支援**] 以提出 Azure 支援要求。

## <a name="troubleshoot-persistent-errors"></a>針對持續性錯誤進行疑難排解

如果應用程式持續無法連線到超大規模資料庫（Citus），最常見的原因是防火牆設定不正確或使用者錯誤。

* 協調員節點防火牆設定：請確定超大規模資料庫伺服器防火牆已設定為允許來自用戶端的連線，包括 proxy 伺服器和閘道。
* 用戶端防火牆設定：用戶端上的防火牆必須允許連接到您的資料庫伺服器。 有些防火牆需要不只允許依名稱進行應用程式，而是允許伺服器的 IP 位址和埠。
* 使用者錯誤：再次檢查連接字串。 您可能會輸入錯誤的參數，例如伺服器名稱。 您可以在 Azure 入口網站中找到各種語言架構和 psql 的連接字串。 移至超大規模資料庫（Citus）伺服器群組中的 [**連接字串**] 頁面。 也請記住，超大規模資料庫（Citus）叢集只有一個資料庫，而其預先定義的名稱是**Citus**。

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>解決永久性連線問題的步驟

1. 設定[防火牆規則](howto-hyperscale-manage-firewall-using-portal.md)以允許用戶端 IP 位址。 (僅適用於臨時性的測試目的) 請使用 0.0.0.0 作為起始 IP 位址並使用 255.255.255.255 作為結束 IP 位址來設定防火牆規則。 該規則會開啟伺服器到所有 IP 位址。 如果規則會解決您的連線問題，請將它移除，並針對適當限制的 IP 位址或位址範圍建立防火牆規則。
2. 在用戶端與網際網路之間的所有防火牆上，請確定已針對輸出連線開啟埠5432。
3. 請確認您的連接字串和其他連線設定。
4. 檢查儀表板中的服務健康情況。

## <a name="next-steps"></a>後續步驟

* 瞭解[適用於 PostgreSQL 的 Azure 資料庫超大規模資料庫（Citus）中防火牆規則](concepts-hyperscale-firewall-rules.md)的概念
* 請參閱如何[管理適用於 PostgreSQL 的 Azure 資料庫超大規模資料庫（Citus）的防火牆規則](howto-hyperscale-manage-firewall-using-portal.md)
