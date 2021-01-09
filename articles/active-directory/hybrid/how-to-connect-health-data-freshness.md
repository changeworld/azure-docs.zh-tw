---
title: Azure AD Connect 健康情況 - 健康情況服務的資料並非最新警示 | Microsoft Docs
description: 本文說明「健康情況服務的資料並非最新」警示的成因，以及如何進行疑難排解。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00518eb91e57efaacb7abc63b6ad4531619be2ce
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98012864"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>健康情況服務的資料並非最新警示

## <a name="overview"></a>概觀

Azure AD Connect Health 監視的內部部署電腦上的代理程式會定期將資料上傳至 Azure AD Connect Health 服務。 如果服務未從代理程式接收資料，入口網站所提供的資訊將會過時。 若要反白顯示問題，服務會引發 **健全狀況服務資料不是最** 新的警示。 當服務在過去兩小時內未收到完整資料時，就會產生此警示。  

- 如果健全狀況服務在過去兩小時內只收到從伺服器傳送的 **部分** 資料類型，就會引發 **警告** 狀態警示。 警告狀態警示不會對設定的收件者觸發電子郵件通知。 
- 如果健全狀況服務在過去兩小時內未收到來自伺服器的任何資料類型，就會引發 **錯誤** 狀態警示。 錯誤狀態警示會觸發電子郵件通知給已設定的收件者。

服務會從在內部部署機器上執行的代理程式取得資料（視服務類型而定）。 下表列出在電腦上執行的代理程式、其執行的作業，以及服務所產生的資料類型。 在某些情況下，程式中牽涉到多個服務，因此其中任何一項都可能是問題。 

## <a name="understanding-the-alert"></a>瞭解警示

**警示詳細資料** 分頁會顯示警示發生的時間和上次偵測到的時間。 每兩個小時執行一次的背景進程會產生並重新評估警示。 在下列範例中，會在03/10 上午9:59 發生初始警示。 當再次評估警示時，警示仍存在於03/12 （上午10:00）。 此分頁也會詳細說明健全狀況服務上次收到特定資料類型的時間。 
 
 ![Azure AD Connect Health 警示詳細資料](./media/how-to-connect-health-data-freshness/data-freshness-details.png)
 
下表將服務類型對應至對應的必要資料類型：

| 服務類型 | 代理程式 (Windows 服務名稱)  | 目的 | 產生的資料類型  |
| --- | --- | --- | --- |  
| Azure AD Connect (同步處理)  | Azure AD Connect Health Sync Insights 服務 | 收集 AAD Connect 特定資訊 (連接器、同步處理規則等 )  | -AadSyncService-SynchronizationRules <br />  -AadSyncService-Connectors <br /> -AadSyncService-GlobalConfigurations  <br />  -AadSyncService-RunProfileResults <br /> -AadSyncService-ServiceConfigurations <br /> -AadSyncService-ServiceStatus   |
|  | Azure AD Connect Health Sync Monitoring 服務 | 收集 AAD Connect 特定的效能計數器、ETW 追蹤、檔案 | 效能計數器 |
| AD DS | Azure AD Connect Health AD DS Insights 服務 | 執行綜合測試，收集拓撲資訊，複寫中繼資料 |  -新增-TopologyInfo-Json <br /> -Common->testdata-Json (建立測試結果)    | 
|  | Azure AD Connect Health AD DS 監視服務 | 收集新增特定的效能計數器、ETW 追蹤、檔案 | -效能計數器  <br /> -Common->testdata-Json (上傳測試結果)   |
| AD FS | Azure AD Connect Health AD FS 診斷服務 | 執行綜合測試 | TestResult (會建立測試結果)  | 
| | Azure AD Connect Health AD FS Insights 服務  | 收集 ADFS 使用計量 | Adfs-UsageMetrics |
| | Azure AD Connect Health AD FS 監視服務 | 收集 ADFS 特定的效能計數器、ETW 追蹤、檔案 | TestResult (上傳測試結果)  |

## <a name="troubleshooting-steps"></a>疑難排解步驟 

以下提供診斷問題所需的步驟。 第一個是所有服務類型通用的一組基本檢查。 

> [!IMPORTANT] 
> 此警示會遵循 Connect Health 的[資料保留原則](reference-connect-health-user-privacy.md#data-retention-policy)

* 請確定已安裝最新版本的代理程式。 檢視[版本歷程記錄](reference-connect-health-version-history.md)。 
* 請確定 Azure AD Connect Health Agents 服務已在電腦上 **執行**。 例如，適用於 AD FS 的 Connect Health 應該要有三個服務。
  ![驗證 Azure AD Connect Health](./media/how-to-connect-health-agent-install/install5.png)

* 確實詳讀[需求小節](how-to-connect-health-agent-install.md#requirements)，並符合相關要求。
* 使用 [測試連線工具](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) 來探索連接問題。
* 如果您有 HTTP Proxy，請遵循這些設定 [步驟](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy)。 


## <a name="next-steps"></a>後續步驟
如果上述任何一個步驟識別出問題，請修正問題並等候警示解決。 警示背景進程每隔2小時會執行一次，因此最多需要2小時的時間來解決警示。 

* [Azure AD Connect Health 資料保留原則](reference-connect-health-user-privacy.md#data-retention-policy)
* [Azure AD Connect Health 常見問題集](reference-connect-health-faq.md)
