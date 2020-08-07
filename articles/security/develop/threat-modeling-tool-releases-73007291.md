---
title: Microsoft Threat Modeling Tool 版本 07/29/2020-Azure
description: 記載 Threat Modeling Tool 的版本資訊
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 07/29/2020
ms.openlocfilehash: 47ffe753d661bb8c9a181148ea235b2a0eb54400
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87839850"
---
# <a name="threat-modeling-tool-update-release-73007291---07292020"></a>Threat Modeling Tool 更新版本 7.3.00729.1-07/29/2020

 (TMT) 的 Microsoft Threat Modeling Tool 版本7.3.00729.1 已于 29 2020 年7月發行，並包含下列變更：

- 錯誤修正
 
## <a name="known-issues"></a>已知問題

### <a name="errors-related-to-tmt7application-file-deserialization"></a>與 TMT7 相關的錯誤。應用程式檔案還原序列化

#### <a name="issue"></a>問題

某些客戶在下載 Threat Modeling Tool 時回報收到下列錯誤訊息：

```
The threat model file '$PATH\TMT7.application' could not be deserialized. File is not an actual threat model or the threat model may be corrupted.
```

之所以會發生這個錯誤，是因為有些瀏覽器原本就不支援 ClickOnce 安裝。 在這些情況下，會將 ClickOnce 應用程式檔下載到使用者的硬碟。

#### <a name="workaround"></a>因應措施

如果您按兩下 TMT7 應用程式檔來啟動 Threat Modeling Tool，將會繼續顯示此錯誤。 不過，在略過錯誤之後，工具就會正常運作。 使用者應該使用在安裝期間于 Windows 功能表中建立的快捷方式來啟動 Threat Modeling Tool，而不是按兩下 TMT7 應用程式檔來啟動 Threat Modeling Tool。

## <a name="system-requirements"></a>系統需求

- 支援的作業系統
  - [Microsoft Windows 10 年度更新](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97)版（含）以後版本
- 必要的 .NET 版本
  - [.Net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262)或更新版本
- 其他需求
  - 必須有網際網路連線可接收工具和範本的更新。

## <a name="documentation-and-feedback"></a>文件和意見反應

- Threat Modeling Tool 的文件位於 [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)，其中包含[關於使用工具](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started)的資訊。

## <a name="next-steps"></a>後續步驟

下載最新版的 [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool)。
