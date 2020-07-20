---
title: 包含檔案
description: 包含檔案
services: data-factory
author: memildin
ms.service: data-factory
ms.topic: include
ms.date: 03/17/2020
ms.author: memildin
ms.custom: include file
ms.openlocfilehash: 7d81799f7fbdb2b41db421daa1a85ec8cde511eb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "79538827"
---
### <a name="the-data-model-of-the-schema"></a>架構的資料模型

|欄位|說明|
|----|----|
|**AlertName**|警示顯示名稱|
|**AlertType**|唯一的警示識別碼|
|**ConfidenceLevel**|選擇性此警示的信賴等級（高/低）|
|**ConfidenceScore**|選擇性安全性警示的數值信賴指標|
|**描述**|警示的描述文字|
|**DisplayName**|警示的顯示名稱|
|**EndTime**|警示的影響結束時間（最後一個事件參與警示的時間）|
|**實體**|與警示相關的實體清單。 這份清單可以混合不同類型的實體|
|**ExtendedLinks**|選擇性與警示相關的所有連結包。 這個袋可以保存各種類型的連結|
|**ExtendedProperties**|與警示相關的其他欄位包|
|**IsIncident**|判斷警示為事件還是定期警示。 事件是將多個警示匯總成一個安全性事件的安全性警示|
|**ProcessingEndTime**|建立警示的 UTC 時間戳記|
|**ProductComponentName**|選擇性產生警示之產品內的元件名稱。|
|**ProductName**|常數（' Azure 資訊安全中心 '）|
|**ProviderName**|unused|
|**RemediationSteps**|要採取以補救安全性威脅的手動動作專案|
|**ResourceId**|受影響資源的完整識別碼|
|**嚴重性**|警示嚴重性（高/中/低/資訊）|
|**SourceComputerId**|受影響伺服器的唯一 GUID （如果在伺服器上產生警示）|
|**SourceSystem**|unused|
|**StartTime**|警示的影響開始時間（引發警示的第一個事件時間）|
|**SystemAlertId**|此安全性警示實例的唯一識別碼|
|**TenantId**|所掃描資源所在之訂用帳戶的父系 Azure Active directory 租使用者識別碼|
|**TimeGenerated**|評估發生的 UTC 時間戳記（資訊安全中心掃描時間）（與 DiscoveredTimeUTC 相同）|
|**型別**|常數（' SecurityAlert '）|
|**VendorName**|提供警示的廠商名稱（例如 ' Microsoft '）|
|**VendorOriginalId**|unused|
|**WorkspaceResourceGroup**|如果在 VM、伺服器、虛擬機器擴展集或回報至工作區的 App Service 實例上產生警示，則會包含該工作區資源組名|
|**WorkspaceSubscriptionId**|如果在 VM、伺服器、虛擬機器擴展集或向工作區報告的 App Service 實例上產生警示，則會包含該工作區 subscriptionId|
|||
