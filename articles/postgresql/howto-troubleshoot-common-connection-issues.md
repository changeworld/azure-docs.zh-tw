---
title: 針對連接進行疑難排解-適用於 PostgreSQL 的 Azure 資料庫-單一伺服器
description: 瞭解如何針對適用於 PostgreSQL 的 Azure 資料庫單一伺服器的連接問題進行疑難排解。
keywords: postgresql 連線, 連接字串, 連線問題, 暫時性錯誤, 連線錯誤
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: c0011889e59470d94e650a19c6713074155c63fa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85106546"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-postgresql---single-server"></a>針對適用於 PostgreSQL 的 Azure 資料庫的連接問題進行疑難排解-單一伺服器

連線問題可能由各種因素造成，包括：

* 防火牆設定
* 連線逾時
* 不正確的登入資訊
* 某些「適用於 PostgreSQL 的 Azure 資料庫」資源已達上限
* 服務基礎結構發生問題
* 正在服務中執行維護
* 藉由調整虛擬核心數目或移至不同的服務層級，變更伺服器的計算配置

一般而言，「適用於 PostgreSQL 的 Azure 資料庫」的連線問題可分類如下：

* 暫時性錯誤 (短期或間歇性)
* 持續性或非暫時性錯誤 (定期重複發生的錯誤)

## <a name="troubleshoot-transient-errors"></a>針對暫時性錯誤進行疑難排解

當執行維護、系統遇到硬體或軟體錯誤，或是您變更伺服器的虛擬核心或服務層級時，就會發生暫時性錯誤。 「適用於 PostgreSQL 的 Azure 資料庫」服務內建高可用性，並已設計為可自動解決這些類型的問題。 不過，您的應用程式會有一小段時間與伺服器中斷連線，通常最多不超過 60 秒。 有些事件可能偶爾需要更長的時間才能解決，例如當有大型交易導致長時間執行的復原時。

### <a name="steps-to-resolve-transient-connectivity-issues"></a>解決暫時性連線問題的步驟

1. 檢查 [Microsoft Azure 服務儀表板](https://azure.microsoft.com/status) ，以了解是否有在應用程式回報錯誤期間發生的任何已知中斷。
2. 連線到雲端服務 (例如「適用於 PostgreSQL 的 Azure 資料庫」) 的應用程式應該預期會發生暫時性錯誤，並實作重試邏輯來處理這些錯誤，而不是將這些錯誤當作應用程式錯誤呈現給使用者。 請檢閱[處理適用於 PostgreSQL 的 Azure 資料庫的暫時性連線錯誤](concepts-connectivity.md)，以了解處理暫時性錯誤的最佳做法和設計指導方針。
3. 當伺服器接近其資源限制時，錯誤可能似乎是暫時性連線問題。 請參閱[適用於 PostgreSQL 的 Azure 資料庫中的限制](concepts-limits.md)。
4. 如果連線問題繼續發生，或如果您的應用程式發生錯誤的持續時間超過 60 秒，或如果您在一天當中，看到錯誤多次發生，請在 [Azure 支援](https://azure.microsoft.com/support/options)網站上選取 [取得支援]****，來提出 Azure 支援要求。

## <a name="troubleshoot-persistent-errors"></a>針對持續性錯誤進行疑難排解

如果應用程式持續無法連線到「適用於 PostgreSQL 的 Azure 資料庫」，通常表示是下列其中一項發生問題︰

* 伺服器防火牆設定：確定已將適用於 PostgreSQL 的 Azure 資料庫伺服器防火牆設為允許來自用戶端的連線，包括 proxy 伺服器和閘道。
* 用戶端防火牆設定：用戶端上的防火牆必須允許連接到您的資料庫伺服器。 也必須允許您無法連到的伺服器 IP 位址和連接埠，在某些防火牆中，還要允許應用程式名稱，例如 PostgreSQL。
* 使用者錯誤：您可能輸入錯誤的連接參數，例如連接字串中的伺服器名稱，或使用者名稱中遺漏的* \@ servername*尾碼。
* 如果您看到錯誤_伺服器未設定為允許 ipv6_連線，請注意基本層不支援 VNet 服務端點。 您必須從嘗試連接到基本伺服器的子網移除 Microsoft Sql 端點。
* 如果您在_SSL 支援未在錯誤中編譯時看到連線錯誤 sslmode 值 "* * *" 無效_，這表示您的于 postgresql 用戶端不支援 SSL。 最可能的是，用戶端 libpq 尚未使用 "--with-openssl" 旗標進行編譯。 請嘗試連接具有 SSL 支援的于 postgresql 用戶端。 

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>解決永久性連線問題的步驟

1. 設定[防火牆規則](howto-manage-firewall-using-portal.md)以允許用戶端 IP 位址。 (僅適用於臨時性的測試目的) 請使用 0.0.0.0 作為起始 IP 位址並使用 255.255.255.255 作為結束 IP 位址來設定防火牆規則。 這樣會開放伺服器供所有 IP 位址存取。 若這樣可解決您的連線問題，請移除此規則並針對已適當限制的 IP 位址或位址範圍建立防火牆規則。
2. 在用戶端與網際網路之間的所有防火牆上，請確定已針對輸出連線開啟埠5432。
3. 請確認您的連接字串和其他連線設定。
4. 檢查儀表板中的服務健康情況。 如果您認為有區域性中斷情況，請參閱[使用適用於 PostgreSQL 的 Azure 資料庫的商務持續性概觀](concepts-business-continuity.md)，以了解復原到新區域的步驟。

## <a name="next-steps"></a>後續步驟

* [處理適用於 PostgreSQL 的 Azure 資料庫的暫時性連線錯誤](concepts-connectivity.md)
