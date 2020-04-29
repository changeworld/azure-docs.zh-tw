---
title: 從現有的伺服器建立設定-Azure 自動化
description: 瞭解如何從現有的伺服器建立 Azure 自動化的設定。
keywords: dsc,powershell,設定,安裝
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: dff9b8f52207a38cf7eaddefa178aff262ddc546
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80585556"
---
# <a name="create-configurations-from-existing-servers"></a>從現有的伺服器建立設定

> 適用于： Windows PowerShell 5。1

從現有的伺服器建立設定可能是一項頗具挑戰性的工作。
您可能不想要*所有*的設定，只是您關心的設定。
就算如此，您也必須知道設定的套用順序，才能成功套用設定。

> [!NOTE]
> 本文是指由開放原始碼社區維護的解決方案。
> 支援僅以 GitHub 共同作業的形式提供，而不是來自 Microsoft。

## <a name="community-project-reversedsc"></a>社區專案： ReverseDSC

已建立名為[ReverseDSC](https://github.com/microsoft/reversedsc)的社區維護解決方案，以在此區域中開始使用 SharePoint。

此解決方案會以[SharePointDSC 資源](https://github.com/powershell/sharepointdsc)為基礎，並加以擴充，以協調現有 SharePoint 伺服器的[收集資訊](https://github.com/Microsoft/sharepointDSC.reverse#how-to-use)。
最新版本有多個[提取模式](https://github.com/Microsoft/SharePointDSC.Reverse/wiki/Extraction-Modes)，可決定要包含的資訊層級。

使用此解決方案的結果是產生與 SharePointDSC 設定腳本搭配使用的設定[資料](https://github.com/Microsoft/sharepointDSC.reverse#configuration-data)。

一旦產生資料檔案之後，您就可以使用它們來搭配[DSC 設定腳本](/powershell/scripting/dsc/overview/overview)來產生 mof 檔案，並將[mof 檔案上傳到 Azure 自動化](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation)。
然後從[內部部署](/azure/automation/automation-dsc-onboarding#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances)或[在 Azure 中](/azure/automation/automation-dsc-onboarding#onboarding-azure-vms)註冊您的伺服器，以提取設定。

若要試用 ReverseDSC，請造訪[PowerShell 資源庫](https://www.powershellgallery.com/packages/ReverseDSC/)並下載解決方案，或按一下 [專案網站] 以查看[檔](https://github.com/Microsoft/sharepointDSC.reverse)。

## <a name="next-steps"></a>後續步驟

- [Windows PowerShell 預期狀態設定概觀](/powershell/scripting/dsc/overview/overview)
- [DSC 資源](/powershell/scripting/dsc/resources/resources)
- [設定本機 Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaconfig)
