---
title: Azure DevTest Labs 中的公司原則與合規性
description: 此文章提供治理適用於 Azure DevTest Labs 基礎結構之公司原則和合規性的相關指導方針。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 81ca2a90b1940d70e170cab3f8d18144a5d5e5a8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74560511"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---company-policy-and-compliance"></a>治理 Azure DevTest Labs 基礎結構 - 公司原則和合規性
此文章提供治理適用於 Azure DevTest Labs 基礎結構之公司原則和合規性的相關指導方針。 

## <a name="public-vs-private-artifact-repository"></a>公用與私人成品存放庫

### <a name="question"></a>問題
組織在 DevTest Labs 中何時應使用公用成品存放庫，而何時應使用私人成品存放庫？

### <a name="answer"></a>Answer
[公用成品存放庫](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) \(英文\) 提供一組最常用的初始軟體套件。 它有助於快速部署，而不必投入時間重現常見的開發人員工具和增益集。您可以選擇部署自己的私人存放庫。 您可以平行方式各使用一個公用和私人存放庫。 您也可以選擇停用公用存放庫。 部署私人存放庫的準則應透過下列問題和考量來衍生：

- 組織是否需要使公司授權的軟體成為其 DevTest Labs 供應項目的一部分？ 如果答案是肯定的，則應該建立私人存放庫。
- 組織是否要開發自訂軟體來提供特定作業，且需將其當作整體佈建程序的一部分？ 如果答案是肯定的，則應該部署私人存放庫。
- 如果組織的治理原則需要隔離，而且組織不會對外部存放庫進行直接組態管理，就應該部署私人成品存放庫。 在此程序的過程中，可複製公用存放庫的初始複本，並與私人存放庫整合在一起。 接著，可以停用公用存放庫，如此一來，組織內的所有人都不能再存取該存放庫。 此方法強制組織內的所有使用者只能具有組織所核准的單一存放庫，並將設定漂移降到最低。

### <a name="single-repository-or-multiple-repositories"></a>單一存放庫或多個存放庫 

### <a name="question"></a>問題
組織應該規劃單一存放庫，還是允許多個存放庫？

### <a name="answer"></a>Answer
作為貴組織整體治理和組態管理策略的一部分，我們建議您使用集中式存放庫。 當您使用多個存放庫時，它們可能會在經過一段時間之後成為非受控軟體的定址接收器。 使用中央存放庫，多個小組就能針對他們的專案，從此存放庫中取用成品。 它會強制執行標準化、安全性、簡化管理，並排除重複的工作。 在集中化過程中，下列動作為適用於長期管理和持續性的建議做法：

- 將 Azure Repos 與 Azure 訂用帳戶用來進行驗證和授權的相同 Azure Active Directory 租用戶產生關聯。
- 在集中管理的 Azure Active Directory 中，建立名為**所有 DevTest Labs 開發人員**的群組。 任何有助於成品開發的開發人員都應該放置於此群組中。
- 相同的 Azure Active Directory 群組可用來提供存取 Azure Repos 存放庫及實驗室。
- 在 Azure Repos 中，應該使用分支或派生，將開發中的存放庫與主要生產環境存放庫分隔開來。 只會在適當的程式碼檢閱之後，使用 Pull 要求來將內容新增至主要分支。 當程式碼檢閱者核准變更之後，負責維護主要分支的首席開發人員就會合併更新的程式碼。 

## <a name="corporate-security-policies"></a>公司安全性原則

### <a name="question"></a>問題
組織如何確保公司安全性原則已就緒？

### <a name="answer"></a>Answer
組織可能藉由執行下列動作來達到此目的：

1. 開發和發佈完善的安全性原則。 該原則會闡述與使用軟體、雲端資產相關聯的可接受使用規則。 它也會定義哪些明顯違反了該原則。 
2. 開發自訂映像、自訂成品及部署程序，允許在使用 Active Directory 定義的安全性領域內進行協調流程。 此方法會強制執行公司界限，並設定一組常用的環境控制項。 這些控制項會以開發人員在開發期間可考量的環境為依據，並遵循安全開發生命週期作為其整體程序的一部分。 此外，目標在於提供未過度限制的環境，其可能會妨礙開發，但卻是一組合理的控制項。 位於包含實驗室虛擬機器之組織單位 (OU) 的群組原則，可能是在生產環境中找到的總計群組原則子集。 或者，它們可以是一組額外的集合，可正確地降低任何已識別出的風險。

## <a name="data-integrity"></a>資料完整性

### <a name="question"></a>問題
組織如何確保資料完整性，以確保遠端開發人員無法移除程式碼，或無法引進惡意程式碼或未經准的軟體？

### <a name="answer"></a>Answer
有數個控制層，可用來為從遠端在 DevTest Labs 中共同作業的外部顧問、約聘人員或員工降低此威脅。 

如先前所述，第一個步驟必須起草並定義一個可接受的使用原則，清晰地概述有人違反原則時的後果。 

適用於遠端存取的第一個控制層是透過未直接連線至實驗室的 VPN 連線來套用遠端存取原則。 

第二個控制層是套用一組群組原則物件，以避免透過遠端桌面進行複製與貼上。 您可以實作網路原則，不允許環境的輸出服務，例如離開該環境的 FTP 和 RDP 服務。 使用者定義的路由可以強制所有 Azure 網路流量回到內部部署，但除非透過 Proxy 來控制掃描內容與工作階段，否則路由無法負責可能允許上傳資料的所有 URL。 公用 IP 可能會在支援 DevTest Labs 的虛擬網路內受到限制，不允許對外部網路資源進行橋接。

最後，需要在整個組織中套用相同類型的限制，其也必須負責可能會接受內容貼文之抽取式媒體或外部 URL 的所有可能方法。 請洽詢您的安全性專業人員來檢閱並實作安全性原則。 如需其他建議，請參閱 [Microsoft 網路安全性](https://www.microsoft.com/security/default.aspx?&WT.srch=1&wt.mc_id=AID623240_SEM_sNYnsZDs)。


## <a name="next-steps"></a>後續步驟
請參閱[應用程式移轉與整合](devtest-lab-guidance-governance-application-migration-integration.md)。
