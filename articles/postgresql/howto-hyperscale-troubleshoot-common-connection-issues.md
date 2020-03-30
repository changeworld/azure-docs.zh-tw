---
title: 故障排除連接 - 超大規模（Citus） - 用於後格雷SQL的 Azure 資料庫
description: 瞭解如何解決與 Azure 資料庫的連接問題，以便發佈後 SQL - 超大規模 （Citus）
keywords: postgresql 連線, 連接字串, 連線問題, 暫時性錯誤, 連線錯誤
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/8/2019
ms.openlocfilehash: c064aca484f85c44dada9888012140784a96863f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74977500"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-postgresql---hyperscale-citus"></a>故障連接問題到 Azure 資料庫後SQL - 超大規模 （Citus）

連接問題可能由以下幾項原因引起：

* 防火牆設定
* 連線逾時
* 登錄資訊不正確
* 伺服器組達到的連接限制
* 服務基礎結構發生問題
* 維修維護
* 協調器節點容錯移轉到新硬體

通常，與超大規模的連接問題可以分類如下：

* 暫時性錯誤 (短期或間歇性)
* 持續性或非暫時性錯誤 (定期重複發生的錯誤)

## <a name="troubleshoot-transient-errors"></a>針對暫時性錯誤進行疑難排解

瞬態錯誤的發生原因有很多。 最常見的包括系統維護、硬體或軟體錯誤以及協調節點 vCore 升級。

為超大規模伺服器組節點啟用高可用性可以自動緩解這些類型的問題。 但是，您的應用程式仍應準備暫時失去其連接。 此外，其他事件可能需要更長時間才能緩解，例如大型事務導致長時間恢復時。

### <a name="steps-to-resolve-transient-connectivity-issues"></a>解決暫時性連線問題的步驟

1. 檢查[Microsoft Azure 服務儀表板](https://azure.microsoft.com/status)，瞭解在應用程式報告錯誤期間發生的任何已知中斷。
2. 連接到雲服務（如超大規模 （Citus） 的應用程式應預期瞬態錯誤並做出正常回應。 例如，應用程式應實現重試邏輯來處理這些錯誤，而不是將它們作為應用程式錯誤呈現給使用者。
3. 當伺服器組接近其資源限制時，錯誤可能看起來像是暫時連接問題。 增加節點 RAM 或添加輔助節點和重新平衡資料可能會有所説明。
4. 如果連接問題持續或持續超過 60 秒，或每天發生多次，請通過在[Azure 支援](https://azure.microsoft.com/support/options)網站上選擇 **"獲取支援**"來提交 Azure 支援請求。

## <a name="troubleshoot-persistent-errors"></a>針對持續性錯誤進行疑難排解

如果應用程式持續無法連接到 Hyperscale （Citus），最常見的原因是防火牆配置錯誤或使用者錯誤。

* 協調節點防火牆配置：確保超大規模伺服器防火牆配置為允許來自用戶端的連接，包括代理伺服器和閘道。
* 用戶端防火牆配置：用戶端上的防火牆必須允許連接到資料庫伺服器。 某些防火牆不僅要求允許按名稱的應用程式，而且允許伺服器的 IP 位址和埠。
* 使用者錯誤：仔細檢查連接字串。 您可能鍵入了伺服器名稱等參數。 您可以在 Azure 門戶中找到各種語言框架和 psql 的連接字串。 轉到超大規模 （Citus） 伺服器組中的連接**字串**頁面。 還要記住，超大規模（Citus）集群只有一個資料庫，其預定義的名稱是**citus。**

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>解決永久性連線問題的步驟

1. 設置[防火牆規則](howto-hyperscale-manage-firewall-using-portal.md)以允許用戶端 IP 位址。 (僅適用於臨時性的測試目的) 請使用 0.0.0.0 作為起始 IP 位址並使用 255.255.255.255 作為結束 IP 位址來設定防火牆規則。 該規則將伺服器打開到所有 IP 位址。 如果規則解決了連接問題，請將其刪除，並為適當受限的 IP 位址或位址範圍創建防火牆規則。
2. 在用戶端和 Internet 之間的所有防火牆上，請確保埠 5432 對出站連接開放。
3. 請確認您的連接字串和其他連線設定。
4. 檢查儀表板中的服務健康情況。

## <a name="next-steps"></a>後續步驟

* 在 Azure[資料庫中瞭解防火牆規則的概念，用於後格雷SQL - 超大規模 （Citus）](concepts-hyperscale-firewall-rules.md)
* 瞭解如何管理[Azure 資料庫的防火牆規則，適用于後格雷SQL - 超大規模 （Citus）](howto-hyperscale-manage-firewall-using-portal.md)
