---
title: 裝置身分識別和桌面虛擬化-Azure Active Directory
description: 瞭解如何搭配使用 VDI 和 Azure AD 裝置身分識別
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: c30ad26f079e6353dc4763b9ae968c33882d8ab6
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96029342"
---
# <a name="device-identity-and-desktop-virtualization"></a>裝置身分識別和桌面虛擬化

系統管理員通常會將虛擬桌面基礎結構部署 (VDI) 在其組織中裝載 Windows 作業系統的平臺。 系統管理員將 VDI 部署至：

- 簡化管理。
- 透過合併和集中資源來降低成本。
- 在任何裝置上隨時隨地提供終端使用者的行動性，以及隨時隨地存取虛擬桌面。

虛擬桌面有兩種主要類型：

- 持續性
- 非永續性

持續性版本會為每個使用者或使用者集區使用唯一的桌面映射。 這些獨特的桌面可以自訂及儲存，以供日後使用。 

非持續性版本會使用使用者可視需要存取的桌面集合。 這些非持續性的桌面會還原為其原始狀態，如果是 Windows 目前的<sup>1</sup> ，當虛擬機器通過關機/重新開機/OS 重設程式時，當使用者登出時，就會<sup>發生這種</sup> 情況。

因為遠端工作會持續成為新的標準，所以在非持續性的 VDI 部署中已經有逐漸增加的趨勢。 當客戶部署非持續性 VDI 時，請務必確保您管理裝置變換的原因，可能是因為經常註冊裝置，而沒有適當的裝置生命週期管理原則。

> [!IMPORTANT]
> 若無法管理裝置流失，可能會導致您的租使用者配額使用量耗用量增加壓力，以及當您的租使用者配額不足時可能會發生服務中斷的風險。 部署非持續性 VDI 環境時，您應該遵循下列指導方針，以避免發生這種情況。

本文將說明 Microsoft 對裝置身分識別和 VDI 支援的指引。 如需裝置身分識別的詳細資訊，請參閱文章 [是什麼是裝置身分識別](overview.md)。

## <a name="supported-scenarios"></a>支援的案例

在您的 VDI 環境 Azure AD 中設定裝置身分識別之前，請先熟悉支援的案例。 下表說明支援的布建案例。 在此內容中布建表示系統管理員可以大規模設定裝置身分識別，而不需要任何使用者互動。

| 裝置身分識別類型 | 身分識別基礎結構 | Windows 裝置 | VDI 平臺版本 | 支援 |
| --- | --- | --- | --- | --- |
| 已聯結的混合式 Azure AD | 同盟<sup>3</sup> | Windows 目前與舊版 Windows | 持續性 | 是 |
|   |   | Windows 目前 | 非持續性 | 是<sup>5</sup> |
|   |   | 舊版 Windows | 非持續性 | 是<sup>6</sup> |
|   | 受控<sup>4</sup> | Windows 目前與舊版 Windows | 持續性 | 是 |
|   |   | Windows 目前 | 非持續性 | 否 |
|   |   | 舊版 Windows | 非持續性 | 是<sup>6</sup> |
| 已聯結的 Azure AD | 同盟 | Windows 目前 | 持續性 | 否 |
|   |   |   | 非持續性 | 否 |
|   | 受控 | Windows 目前 | 持續性 | 否 |
|   |   |   | 非持續性 | 否 |
| 已註冊 Azure AD | 同盟/受管理 | Windows 目前/舊版 windows | 持續/非持續性 | 不適用 |

<sup>1</sup>個 **Windows 目前** 的裝置代表 Windows 10、windows server 2016 v1803 或更高版本，以及 windows server 2019。
<sup>2</sup> **個舊版 Windows** 裝置代表 windows 7、Windows 8.1、windows Server 2008 r2、Windows Server 2012 和 windows server 2012 R2。 如需 Windows 7 的支援資訊，請參閱 [windows 7 的支援即將結束](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support)。 如需 Windows Server 2008 R2 的支援資訊，請參閱 [為 Windows server 2008 終止支援做好準備](https://www.microsoft.com/cloud-platform/windows-server-2008)。

<sup>3</sup> 同盟 **身分識別基礎** 結構環境代表具有身分識別提供者（例如 AD FS 或其他協力廠商 IDP）的環境。

<sup>4</sup> **受控** 識別基礎結構環境代表一個環境，其 Azure AD 為使用 [密碼雜湊同步 (PHS)](../hybrid/whatis-phs.md) 或 [傳遞驗證 (](../hybrid/how-to-connect-pta.md) 使用 [無縫單一登入](../hybrid/how-to-connect-sso.md)PTA) 的身分識別提供者。

<sup>5</sup> **適用于 Windows 最新的5個非持續性支援** 需要其他考慮，如下列指引一節所述。 此案例需要 Windows 10 1803、Windows Server 2019 或 Windows Server (半年通道) 起始1803版

<sup>6</sup> **針對舊版 Windows 的非持續性支援** 需要其他考慮，如下列指引一節所述。


## <a name="microsofts-guidance"></a>Microsoft 指導方針

系統管理員應該根據其身分識別基礎結構參考下列文章，以瞭解如何設定混合式 Azure AD 聯結。

- [設定同盟環境的混合式 Azure Active Directory 聯結](hybrid-azuread-join-federated-domains.md)
- [設定受控環境的混合式 Azure Active Directory 聯結](hybrid-azuread-join-managed-domains.md)

部署非持續性 VDI 時，Microsoft 建議 IT 系統管理員執行下列指引。 若未這麼做，將會導致您的目錄有許多過時的混合式 Azure AD 加入的裝置，這些裝置已從您的非持續性 VDI 平臺註冊，因而增加了租使用者配額的壓力，以及因租使用者配額不足而導致服務中斷的風險。

- 如果您想要使用「系統準備工具」 ( # A0) ，而且如果您使用 Windows 之前的 10 1809 映射來進行安裝，請確定映射不是來自已註冊 Azure AD 的裝置，因為混合式 Azure AD 已加入。
- 如果您依賴虛擬機器 (VM) 快照集來建立額外的 Vm，請確定該快照集不是來自已向 Azure AD 註冊的 VM，混合式 Azure AD 聯結。
- 建立並使用顯示名稱的前置詞 (例如，將桌上型電腦指定為非持續 VDI 的電腦 ) 的 NPVDI。
- 針對舊版 Windows：
   - 將 **autoworkplacejoin/leave** 命令作為登出腳本的一部分來執行。 此命令應該在使用者的內容中觸發，且應在使用者完全登出且仍有網路連線時執行。
- 若為同盟環境中目前的 Windows (例如 AD FS) ：
   - 在 VM 開機順序中執行 **dsregcmd.exe/join** 。
   - **請勿** 在 VM 關機/重新開機過程中執行 dsregcmd.exe/leave。
- 定義和執行 [管理過時裝置](manage-stale-devices.md)的程式。
   - 一旦您有一項策略可以識別您的非持續混合式 Azure AD 聯結裝置 (例如使用電腦顯示名稱前置詞) ，您應該更積極地清除這些裝置，以確保您的目錄不會在許多過時的裝置上使用。
   - 針對目前和下層 Windows 上的非持續性 VDI 部署，您應該刪除 **ApproximateLastLogonTimestamp** 超過15天的裝置。
 
## <a name="next-steps"></a>後續步驟

[設定聯合環境的混合式 Azure Active Directory 聯結](hybrid-azuread-join-federated-domains.md)
