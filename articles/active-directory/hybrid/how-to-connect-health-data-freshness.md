---
title: Azure AD Connect 健康情況 - 健康情況服務的資料並非最新警示 | Microsoft Docs
description: 本文說明「健康情況服務的資料並非最新」警示的成因，以及如何進行疑難排解。
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: SamuelD
editor: ''
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a94bd07cf5020981cdf028ec0eccfa8fa531d240
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76897176"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>健康情況服務的資料並非最新警示

## <a name="overview"></a>總覽

Azure AD 連接運行狀況監視的本地電腦上代理定期將資料上載到 Azure AD 連接運行狀況服務。 如果服務未從代理接收資料，則門戶提供的資訊將過時。 為了突出顯示此問題，該服務將引發**運行狀況服務資料不是最新的**警報。 當服務在過去兩小時內未收到完整的資料時，將生成此警報。  

- 如果運行狀況服務在過去兩小時內僅收到從伺服器發送**的部分**資料類型，則觸發**警告**狀態警報。 警告狀態警報不會觸發發送給已配置收件者的電子郵件通知。 
- 如果運行狀況服務在過去兩小時內未從伺服器收到任何資料類型，則將觸發**錯誤**狀態警報。 錯誤狀態警報觸發電子郵件通知給已配置的收件者。

該服務從在本地電腦上運行的代理獲取資料，具體取決於服務類型。 下表列出了在電腦上運行的代理、它們做什麼以及服務生成的資料類型。 在某些情況下，該過程涉及多個服務，因此其中任何一個可能是罪魁禍首。 

## <a name="understanding-the-alert"></a>瞭解警報

"**警報詳細資訊"** 邊欄選項卡顯示警報發生和上次檢測到的時。 每兩小時運行一次的後臺進程將生成並重新評估警報。 在下面的示例中，初始警報發生在 03/10 上午 9：59。 再次評估警報時，警報在 03/12 上午 10：00 仍然存在。 邊欄選項卡還詳細介紹了運行狀況服務上次接收特定資料類型的時間。 
 
 ![Azure AD 連接運行狀況警報詳細資訊](./media/how-to-connect-health-data-freshness/data-freshness-details.png)
 
下表將服務類型映射到相應的所需資料類型：

| 服務類型 | 代理（Windows 服務名稱） | 目的 | 生成的資料類型  |
| --- | --- | --- | --- |  
| Azure AD 連接（同步） | Azure AD Connect Health Sync Insights 服務 | 收集 AAD 連接特定資訊（連接器、同步規則等） | - AadSync服務同步規則 <br />  - AadSync服務連接器 <br /> - AadSync服務-全球配置  <br />  - Aadsync服務-回合組態檔結果 <br /> - AadSync服務-服務配置 <br /> - AadSync服務-服務狀態   |
|  | Azure AD Connect Health Sync Monitoring 服務 | 收集 AAD 連接特定的 perf 計數器、ETW 跟蹤、檔 | 效能計數器 |
| AD DS | Azure AD Connect Health AD DS Insights 服務 | 執行綜合測試、收集拓撲資訊、複製中繼資料 |  - 添加拓撲資訊-日森 <br /> - 通用測試資料-Json（創建測試結果）   | 
|  | Azure AD Connect Health AD DS 監視服務 | 收集特定于 ADDS 的 perf 計數器、ETW 跟蹤、檔 | - 效能計數器  <br /> - 通用測試資料-Json（上傳測試結果）  |
| AD FS | Azure AD Connect Health AD FS 診斷服務 | 執行綜合測試 | 測試結果（創建測試結果） | 
| | Azure AD Connect Health AD FS Insights 服務  | 收集 ADFS 使用指標 | Adfs-UsageMetrics |
| | Azure AD Connect Health AD FS 監視服務 | 收集特定于 ADFS 的 perf 計數器、ETW 跟蹤、檔 | 測試結果（上傳測試結果） |

## <a name="troubleshooting-steps"></a>疑難排解步驟 

診斷問題所需的步驟如下所示。 第一個是一組所有服務類型共有的基本檢查。 

> [!IMPORTANT] 
> 此警示會遵循 Connect Health 的[資料保留原則](reference-connect-health-user-privacy.md#data-retention-policy)

* 確保安裝了最新版本的代理。 查看[發佈歷史記錄](reference-connect-health-version-history.md)。 
* 確保 Azure AD 連接運行狀況代理服務在電腦上**運行**。 例如，適用於 AD FS 的 Connect Health 應該要有三個服務。
  ![驗證 Azure AD Connect Health](./media/how-to-connect-health-agent-install/install5.png)

* 確實詳讀[需求小節](how-to-connect-health-agent-install.md#requirements)，並符合相關要求。
* 使用[測試連線工具](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)找出連線問題。
* 如果您有 HTTP Proxy，請遵循這些[設定步驟](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy)。 


## <a name="next-steps"></a>後續步驟
如果上述任何步驟發現問題，請修復它並等待警報解決。 警報後臺進程每 2 小時運行一次，因此最多需要 2 小時才能解決警報。 

* [Azure AD 連接運行狀況資料保留原則](reference-connect-health-user-privacy.md#data-retention-policy)
* [Azure AD Connect Health 常見問題集](reference-connect-health-faq.md)
