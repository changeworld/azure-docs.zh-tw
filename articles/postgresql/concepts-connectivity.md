---
title: 處理暫時性連接錯誤-適用於 PostgreSQL 的 Azure 資料庫-單一伺服器
description: 瞭解如何處理適用於 PostgreSQL 的 Azure 資料庫單一伺服器的暫時性連線錯誤。
keywords: postgresql 連線, 連接字串, 連線問題, 暫時性錯誤, 連線錯誤
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 026a0edf24d349c4b445d6229d3b1ad73decf87d
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2020
ms.locfileid: "82097824"
---
# <a name="handling-transient-connectivity-errors-for-azure-database-for-postgresql---single-server"></a>處理適用於 PostgreSQL 的 Azure 資料庫的暫時性連線錯誤 - 單一伺服器

本文說明如何處理連接到適用於 PostgreSQL 的 Azure 資料庫的暫時性錯誤。

## <a name="transient-errors"></a>暫時性錯誤

暫時性錯誤 (也稱為暫時性故障) 係指會自行解決的錯誤。 這些錯誤最常顯現在與正在卸除之資料庫伺服器的連線。 此外，與伺服器的新連線也無法開啟。 舉例來說，當發生硬體或網路故障時，就可能發生暫時性錯誤。 另一個原因可能是即將推出的新版本 PaaS 服務。系統會在不到60秒的時間內自動減輕這些事件。 設計及開發雲端應用程式時，最佳做法是將暫時性錯誤納入考量。 假設這些錯誤可能隨時在任何元件發生，並備妥適當的邏輯來處理這些情況。

## <a name="handling-transient-errors"></a>處理暫時性錯誤

您應該使用重試邏輯來處理暫時性錯誤。 必須考量的情況：

* 您嘗試開啟連線時發生錯誤
* 伺服器端卸除閒置的連線。 當您嘗試發出命令時卻無法執行該命令
* 目前正在執行命令的作用中連線被卸除。

第一種和第二種情況的處理方式相當直接。 請嘗試重新開啟連線。 成功時，即表示系統已解決暫時性錯誤。 您可以再次使用「適用於 PostgreSQL 的 Azure 資料庫」。 建議您先稍候，再重試連線。 如果初始重試失敗，請先放棄。 如此一來，系統才能使用所有可用資源來解決錯誤情況。 建議採用的模式為：

* 先等候 5 秒，再進行第一次重試。
* 針對接下來的每次重試，依指數遞增等候時間，最多可達 60 秒。
* 設定應用程式將作業視為失敗的重試次數上限。

當具有作用中交易的連線失敗時，比較難正確地處理復原。 有兩種情況：如果交易本質上是唯讀的，您可以放心地重新開啟連線並重試交易。 不過，如果交易也對資料庫進行寫入，則必須判斷該交易是已被復原，還是在發生暫時性錯誤之前即已成功。 在此情況下，您可能只會收到來自資料庫伺服器的認可通知。

其中一種做法是，在用戶端上產生一個用於所有重試的唯一識別碼。 您需將這個唯一識別碼隨著交易一起傳遞給伺服器，並將它與一個唯一條件約束一起儲存在資料行中。 如此一來，您便可以放心地重試交易。 如果先前的交易已復原，且用戶端產生的唯一識別碼尚未存在於系統中，交易便會成功。 如果因先前的交易已順利完成而使得先前已儲存唯一識別碼，交易便會因發生重複索引鍵違規而失敗。

當您的程式會透過第三方中介軟體與「適用於 PostgreSQL 的 Azure 資料庫」進行通訊時，請詢問廠商中介軟體是否包含暫時性錯誤的重試邏輯。

請務必測試您的重試邏輯。 例如，相應增加或減少「適用於 PostgreSQL 的 Azure 資料庫」伺服器的計算資源時，請嘗試執行您的程式碼。 您的應用程式應該要能夠毫無問題地處理在此作業期間發生的短暫停機情況。

## <a name="next-steps"></a>後續步驟

* [針對適用於 PostgreSQL 的 Azure 資料庫的連線問題進行疑難排解](howto-troubleshoot-common-connection-issues.md)
