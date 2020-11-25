---
title: 客戶資料要求功能-Azure 時間序列深入解析 |Microsoft Docs
description: 瞭解 Azure 時間序列深入解析中的客戶資料要求功能。
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.topic: conceptual
ms.service: time-series-insights
services: time-series-insights
ms.date: 10/02/2020
ms.custom: seodec18
ms.openlocfilehash: b5ecbabe4a5c3458c77d145fbce1281550470e00
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "96019067"
---
# <a name="summary-of-customer-data-request-features"></a>客戶資料要求功能的摘要

Azure 時間序列深入解析是受控雲端服務，具有儲存、分析和視覺效果元件，可以輕鬆地同時擷取、儲存、探索和分析數十億個事件。

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

為了檢視、匯出及刪除可能會受限於資料主體要求的個人資料，Azure 時間序列深入解析租用戶系統管理員可以使用 Azure 入口網站或 REST API。 使用 Azure 入口網站來服務資料主體要求，是執行這些作業的較簡單方法，也是大多數使用者偏好的方法。

## <a name="identifying-customer-data"></a>識別客戶資料

Azure 時間序列深入解析會將個人資料視為與時間序列深入解析的系統管理員和使用者相關聯的資料。 時間序列深入解析會儲存使用者的 Azure Active Directory 物件識別碼及環境的存取權。 Azure 入口網站會顯示使用者的電子郵件地址，但這些電子郵件地址不會儲存在時間序列深入解析內，而是在 Azure Active Directory 中使用 Azure Active Directory 物件識別碼以動態方式查詢。

## <a name="deleting-customer-data"></a>刪除客戶資料

租用戶系統管理員可以使用 Azure 入口網站來刪除客戶資料。

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

不過，在您透過入口網站刪除客戶資料之前，應該在 Azure 入口網站中從時間序列深入解析環境移除使用者的存取原則。 如需詳細資訊，請 [使用 Azure 入口網站讀取授與時間序列深入解析環境的資料存取權](./concepts-access-policies.md)。

您也可以使用 REST API，在存取原則上執行刪除作業。 如需詳細資訊，請參閱 [存取原則-刪除](/rest/api/time-series-insights/management(gen1/gen2)/accesspolicies/delete)。

時間序列深入解析與 Azure 入口網站中的 [原則] 刀鋒視窗整合。 時間序列深入解析和 [原則] 刀鋒視窗兩者都可讓您檢視、匯出及刪除服務內儲存的使用者資料。 在 Azure 入口網站的 [原則] 刀鋒視窗內採取的任何刪除動作，也會刪除時間序列深入解析內的使用者資料。 例如，如果使用者有儲存的個人查詢，該查詢會從時間序列深入解析總管中永久刪除。 如果使用者有儲存的共用查詢，該查詢仍會存在，但是會永久刪除使用者資訊。 下列附註包含如何完成這些工作的指示。

## <a name="exporting-customer-data"></a>匯出客戶資料

與刪除資料類似，租用戶系統管理員可以從 Azure 入口網站的 [原則] 刀鋒視窗，檢視和匯出儲存在時間序列深入解析中的資料。

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

如果您是租用戶系統管理員，便可以在 Azure 入口網站中檢視時間序列深入解析環境內的資料存取原則。 如需詳細資訊，請 [使用 Azure 入口網站讀取授與時間序列深入解析環境的資料存取權](./concepts-access-policies.md)。

您也可以在提供的 REST API 中使用「依環境列出」作業，在存取原則上執行匯出作業。 如需詳細資訊，請參閱 [存取原則-依環境列出](/rest/api/time-series-insights/management(gen1/gen2)/accesspolicies/listbyenvironment)。

## <a name="to-delete-data-stored-within-time-series-insights"></a>刪除儲存在時間序列深入解析內的資料

個人資料可能會進入時間序列深入解析儲存體，其情況有別於使用者和系統管理員資料。 如果您將儲存在時間序列深入解析中的資料視為個人資料，則可以使用下列步驟匯出並刪除該資料：

### <a name="view-and-export-data"></a>檢視和匯出資料

若要檢視和匯出儲存在時間序列深入解析內的資料，您需要搜尋該資料。 您可以使用時間序列深入解析總管或時間序列深入解析查詢 API 來檢視和匯出資料。 若要使用時間序列深入解析總管檢視和匯出資料，請先搜尋以找出所需的使用者資料。 搜尋之後，以滑鼠右鍵按一下圖表，然後選取 [探索事件]。 此時會出現事件方格，並提供將資料匯出為 CSV 和 JSON 的選項。

如需詳細資訊，請參閱 [Azure 時間序列深入解析 explorer](time-series-insights-explorer.md)。

### <a name="delete-data"></a>刪除資料

時間序列深入解析目前不支援細微刪除資料。 不過，時間序列深入解析可設定保留原則，以便移除在時間序列深入解析內儲存的客戶資料。 您可以將整個時間序列深入解析環境的保留期限調整為任何天數，以支援您的刪除需求。

如需詳細資訊，請參閱設定 [時間序列深入解析中的保留](time-series-insights-how-to-configure-retention.md)。

## <a name="next-steps"></a>後續步驟

* 深入瞭解 [如何將資料存取權授與您的 Azure 時間序列深入解析環境](./concepts-access-policies.md)。

* 查看 [Azure 時間序列深入解析 explorer](time-series-insights-explorer.md)。

* 瞭解如何設定 [時間序列深入解析中的保留](time-series-insights-how-to-configure-retention.md)。