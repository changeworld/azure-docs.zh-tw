---
title: 未來 Azure 資訊安全中心的重要變更
description: 您需要留意並提早規劃的各項 Azure 資訊安全中心變更
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/26/2020
ms.author: memildin
ms.openlocfilehash: 549a95b0b2ffc2b2d2bf5670a961e0454683e33a
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2020
ms.locfileid: "93026712"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>未來 Azure 資訊安全中心的重要變更

> [!IMPORTANT]
> 本頁中載明的資訊與預先發行產品或功能相關，且內容可能在公開上市之前修改。 Microsoft 對於此處提供的資訊不做任何明示或暗示的承諾或保證。

本頁內容可帶您了解為資訊安全中心規劃的各項變更， 並說明規劃的產品修改，且可能影響您的安全分數或工作流程等項目。

如果要尋找 6 個月之前的版本資訊，請前往[Azure 資訊安全中心的新功能](release-notes.md)。


## <a name="planned-changes"></a>規劃的變更

### <a name="recommendations-related-to-azure-security-benchmark-to-be-added-preview"></a>預計新增 Azure 安全性效能評定相關的建議 (預覽)

| 層面 | 詳細資料 |
|---------|---------|
|公告日期 | 2020 年 10 月 26 日  |
|此變更的日期  |  2020 年 11 月 |
|影響     | 可能會有更多要檢閱的建議。<br>對安全分數沒有立即性的影響 - 預覽建議不會影響您的安全分數。<br>對資源的健全狀態沒有立即性的影響 - 預覽建議不會將資源轉譯為「狀況不良」。|
|  |  |

Azure 安全性效能評定是 Microsoft 針對以通用合規性架構為基礎的安全性和合規性最佳做法所撰寫的一組 Azure 特定指導方針。 [深入了解 Azure 安全性效能評定](../security/benchmarks/introduction.md)。

接下來會在 Azure 資訊安全中心新增 18 個新的建議，以增加評定的涵蓋範圍。

預覽建議不會將資源轉譯為「狀況不良」，這些項目也不會納入您安全分數的計算範圍。 您可以盡可能加以補救，以在預覽期間結束時計入您的分數。 若要了解如何回應這些建議，請參閱[Azure 資訊安全中心的補救建議](security-center-remediate-recommendations.md)。

- 應該為虛擬機器啟用 Azure 備份
- 應為 MySQL 資料庫伺服器啟用 [強制執行 SSL 連線]
- 應為 PostgreSQL 資料庫伺服器啟用 [強制執行 SSL 連線]
- 應為適用於 MariaDB 的 Azure 資料庫啟用異地備援備份
- 應為適用於 MySQL 的 Azure 資料庫啟用異地備援備份
- 應為適用於 PostgreSQL 的 Azure 資料庫啟用異地備援備份
- API 應用程式的 Java 應該更新為最新版本
- 函式應用程式的 Java 應該更新為最新版本
- Web 應用程式的 Java 應該更新為最新版本
- API 應用程式的 PHP 應該更新為最新版本
- Web 應用程式的 PHP 應該更新為最新版本
- MariaDB 伺服器應啟用私人端點
- MySQL 伺服器應啟用私人端點
- PostgreSQL 伺服器應啟用私人端點
- API 應用程式的 Python 應該更新為最新版本
- 函式應用程式的 Python 應該更新為最新版本
- Web 應用程式的 Python 應該更新為最新版本
- Web 應用程式應該要求所有要求提供 SSL 憑證

相關連結：

- [深入了解 Azure 安全性效能評定](../security/benchmarks/introduction.md)
- [深入了解 Azure API 應用程式](../app-service/app-service-web-tutorial-rest-api.md)
- [深入了解 Azure 函式應用程式](../azure-functions/functions-overview.md)
- [深入了解 Azure Web 應用程式](../app-service/overview.md)
- [深入了解適用於 MariaDB 的 Azure 資料庫](../mariadb/overview.md)
- [深入了解適用於 MySQL 的 Azure 資料庫](../mysql/overview.md)
- [深入了解適用於 PostgreSQL 的 Azure 資料庫](../postgresql/overview.md)

## <a name="next-steps"></a>後續步驟

如需產品的所有最近變更，請參閱 [Azure 資訊安全中心有哪些新功能？](release-notes.md)。