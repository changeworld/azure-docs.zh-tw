---
title: Microsoft Threat Modeling Tool 版本 07/29/2020-Azure
description: 記載威脅模型化工具 release 7.3.00729.1 的版本資訊。
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 07/29/2020
ms.openlocfilehash: 3e6fcd52ad9cb6c127c14bac2f33223fb921519e
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94516368"
---
# <a name="threat-modeling-tool-update-release-73007291---07292020"></a>Threat Modeling Tool 更新版本 7.3.00729.1-07/29/2020

Microsoft Threat Modeling Tool (TMT) 的版本7.3.00729.1 已于 29 2020 年7月發行，並包含下列變更：

- Bug 修正
 
## <a name="known-issues"></a>已知問題

### <a name="errors-related-to-tmt7application-file-deserialization"></a>與 TMT7 相關的錯誤。應用程式檔案還原序列化

#### <a name="issue"></a>問題

某些客戶回報在下載 Threat Modeling Tool 時收到下列錯誤訊息：

```
The threat model file '$PATH\TMT7.application' could not be deserialized. File is not an actual threat model or the threat model may be corrupted.
```

發生此錯誤的原因是某些瀏覽器原本就不支援 ClickOnce 安裝。 在這些情況下，ClickOnce 應用程式檔會下載到使用者的硬碟上。

#### <a name="workaround"></a>因應措施

如果 Threat Modeling Tool 是按兩下 TMT7 的應用程式檔來啟動，則會繼續出現這個錯誤。 不過，在略過錯誤之後，工具將會正常運作。 使用者應該使用在安裝期間在 Windows 功能表中建立的快捷方式，來啟動 Threat Modeling Tool，而不是藉由按兩下 TMT7 應用程式來啟動 Threat Modeling Tool。

## <a name="system-requirements"></a>系統需求

- 支援的作業系統
  - [Microsoft Windows 10 年度更新版](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) 或更新版本
- 必要的 .NET 版本
  - [.Net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) 或更新版本
- 其他需求
  - 必須有網際網路連線可接收工具和範本的更新。

## <a name="documentation-and-feedback"></a>文件和意見反應

- Threat Modeling Tool 的文件位於 [docs.microsoft.com](./threat-modeling-tool.md)，其中包含[關於使用工具](./threat-modeling-tool-getting-started.md)的資訊。

## <a name="next-steps"></a>後續步驟

下載最新版的 [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool)。