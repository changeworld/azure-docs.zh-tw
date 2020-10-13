---
title: 從現有伺服器為 Azure 自動化狀態設定建立設定
description: 本文說明如何從現有的伺服器為 Azure 自動化狀態設定建立設定。
keywords: dsc,powershell,設定,安裝
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8f376fb383e50a39f0f12d45cf9b5ae47ad6fcbb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86186498"
---
# <a name="create-configurations-from-existing-servers"></a>從現有的伺服器建立設定

> 適用於：Windows PowerShell 5.1

從現有的伺服器建立設定可能是一項頗具挑戰性的工作。
您可能不需要「所有」設定，只需要您在意的設定。
就算如此，您也必須知道設定的套用順序，才能成功套用設定。

> [!NOTE]
> 本文討論由開放原始碼社群所維護的解決方案。
> 支援僅以 GitHub 共同作業的形式提供，而不是來自 Microsoft。

## <a name="community-project-reversedsc"></a>社群專案：ReverseDSC

已建立的 [ReverseDSC](https://github.com/microsoft/reversedsc) 社群維護解決方案可在此區域中使用 (以 SharePoint 開始)。

此解決方案是以 [SharePointDSC 資源](https://github.com/powershell/sharepointdsc)為基礎來加以擴充，進而協調從現有 SharePoint 伺服器[收集資訊的方式](https://github.com/Microsoft/sharepointDSC.reverse#how-to-use)。
最新版本有多個[擷取模式](https://github.com/Microsoft/SharePointDSC.Reverse/wiki/Extraction-Modes)，可判斷要包含的資訊層級。

使用解決方案的結果是產生與 SharePointDSC 設定指令碼搭配使用的[設定資料](https://github.com/Microsoft/sharepointDSC.reverse#configuration-data)。

產生資料檔案後，您就可以將其與 [DSC 設定指令碼](/powershell/scripting/dsc/overview/overview)搭配使用來產生 MOF 檔案，並[將 MOF 檔案上傳到 Azure 自動化](./tutorial-configure-servers-desired-state.md#create-and-upload-a-configuration-to-azure-automation)。
然後從[內部部署](./automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines)或[在 Azure 中](./automation-dsc-onboarding.md#enable-azure-vms)註冊您的伺服器，以提取設定。

若要試用 ReverseDSC，請造訪 [PowerShell 資源庫](https://www.powershellgallery.com/packages/ReverseDSC/)並下載解決方案，或按一下 [專案網站] 以檢視[文件](https://github.com/Microsoft/sharepointDSC.reverse)。

## <a name="next-steps"></a>後續步驟

- 若要了解 PowerShell DSC，請參閱 [Windows PowerShell Desired State Configuration 概觀](/powershell/scripting/dsc/overview/overview)。
- 探索 [DSC 資源](/powershell/scripting/dsc/resources/resources)中的 PowerShell DSC 資源。
- 如需本機設定管理員設定的詳細資訊，請參閱[設定本機設定管理員](/powershell/scripting/dsc/managing-nodes/metaconfig)。
