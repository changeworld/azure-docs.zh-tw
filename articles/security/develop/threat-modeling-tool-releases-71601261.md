---
title: Microsoft Threat Modeling Tool 版本1/29/2019
titleSuffix: Azure
description: 記載 Threat Modeling Tool 的版本資訊
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 01/25/2019
ms.openlocfilehash: 7d0be8d7243331264c10a407e3d78370ea798928
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "78269781"
---
# <a name="threat-modeling-tool-update-release-71601261---1292019"></a>Threat Modeling Tool 更新版本 7.1.60126.1 - 1/29/2019

Microsoft Threat Modeling Tool 7.1.60126.1 版已於 2019 年 1 月 29 日發行，其中包含下列變更：

- .NET 的最低必要版本已增加至[.net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262)。
- Windows 的最低必要版本已提高到 [Windows 10 年度更新版](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97)，以因應 .NET 相依性的需求。
- 工具的 [選項] 功能表已新增模型驗證切換功能。
- 已更新威脅屬性中的幾個連結。
- 工具的主畫面進行了些許 UX 變更。
- Threat Modeling Tool 現在會承襲主機作業系統的 TLS 設定，而且必須在支援 TLS 1.2 或更新版本的環境中才能執行。

## <a name="feature-changes"></a>功能變更

### <a name="model-validation-option"></a>模型驗證選項

根據客戶的意見反應，工具中已新增啟用或停用模型驗證的選項。 先前，如果您的範本在兩個物件之間使用單一單向資料流程，您可能會在訊息框架中收到錯誤訊息，指出： ObjectsName 至少需要一個 ' Any '。 停用模型驗證會防止在檢視中顯示這些警告。

將模型驗證切換為開啟和關閉的選項可在 [檔案]-> [設定]-> [選項] 功能表中找到。 此設定的預設值為 [停用]。

![模型驗證選項](./media/threat-modeling-tool-releases-71601261/tmt_model_validation_option.png)

## <a name="system-requirements"></a>系統需求

- 支援的作業系統
  - [Microsoft Windows 10 年度更新](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97)版（含）以後版本
- 必要的 .NET 版本
  - [.Net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262)或更新版本
- 其他需求
  - 必須有網際網路連線可接收工具和範本的更新。

## <a name="known-issues"></a>已知問題

### <a name="unsupported-systems"></a>不支援的系統

#### <a name="issue"></a>問題

無法安裝 .NET 4.7.1 或更新版本的 Windows 10 系統使用者，例如 Windows 10 Enterprise LTSB (1507 版)，將無法在升級之後開啟工具。 這些舊版的 Windows 已不是 Threat Modeling Tool 的支援平台，不應安裝最新的更新。

#### <a name="workaround"></a>因應措施

Windows 10 Enterprise LTSB (版本 1507 版) 的使用者若已安裝最新的更新，可以透過 [應用程式與功能] 中的 [解除安裝] 對話方塊還原為舊版的 Threat Modeling Tool。

## <a name="documentation-and-feedback"></a>文件和意見反應

- Threat Modeling Tool 的文件位於 [docs.microsoft.com](threat-modeling-tool.md)，其中包含[關於使用工具](threat-modeling-tool-getting-started.md)的資訊。

## <a name="next-steps"></a>後續步驟

下載最新版的 [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool)。
