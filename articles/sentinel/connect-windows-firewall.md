---
title: 將 Windows Defender 防火牆資料連線至 Azure Sentinel |Microsoft Docs
description: 啟用 Azure Sentinel 中的 Windows 防火牆連接器，輕鬆地從已安裝 Log Analytics 代理程式的 Windows 機器串流防火牆事件。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0e41f896-8521-49b8-a244-71c78d469bc3
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/05/2020
ms.author: yelevin
ms.openlocfilehash: 5804dcc840eb666c1d43ea7d7ed7640b8f7ff371
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89657440"
---
# <a name="connect-windows-defender-firewall-with-advanced-security-to-azure-sentinel"></a>將 Windows Defender 防火牆與 Advanced Security 連線至 Azure Sentinel

[具有 Advanced security](https://docs.microsoft.com/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) connector 的 Windows Defender 防火牆可讓 Azure Sentinel 輕鬆地從工作區中的任何 Windows 電腦，使用先進的安全性記錄內嵌 Windows Defender 防火牆。 此連線可讓您查看和分析活頁簿中的 Windows 防火牆事件、使用它們來建立自訂警示，以及將它們併入您的安全性調查中，讓您更深入瞭解組織的網路，以及改善安全性作業功能。 

解決方案會從已安裝 Log Analytics 代理程式的 Windows 電腦收集 Windows 防火牆事件。 

> [!NOTE]
> - 資料會儲存在您執行 Azure Sentinel 之工作區的地理位置。
>
> - 如果 Azure Sentinel 和 Azure Defender (先前 Azure 資訊安全中心) 收集至相同的工作區，就不需要透過此連接器啟用 Windows 防火牆解決方案。 如果您仍然啟用它，則不會造成重複的資料。 

## <a name="prerequisites"></a>必要條件

- 您必須具有您想要監視的機器所連接之工作區的讀取和寫入權限。

- 除了任何**Azure Sentinel**角色之外，您還必須在該工作區的 SecurityInsights 解決方案上指派**Log Analytics 參與者**角色。 [深入了解](../role-based-access-control/built-in-roles.md#log-analytics-contributor)

## <a name="enable-the-connector"></a>啟用連接器 

1. 在 Azure Sentinel 入口網站的導覽功能表中，選取 [ **資料連線器** ]。

1. 從連接器資源庫選取 [ **Windows 防火牆** ]，然後按一下 [ **開啟連接器] 頁面**。

### <a name="instructions-tab"></a>指示索引標籤

在 [ **說明** ] 索引標籤上，完成下列步驟。

- **如果您的 Windows 機器位於 Azure 中，請完成下列步驟：**

   1. 選取 [ **在 Azure Windows 虛擬機器上安裝代理程式**]。
   
   1. 在出現的 [ **Azure Windows 虛擬機器 & 安裝代理程式] >** 連結中，按一下 [下載]。
   
   1. 在 [ **虛擬機器** ] 清單中，選取您要串流至 Azure Sentinel 的 Windows 電腦。  (您可以在 OS 資料行篩選器中選取 **windows** ，以確保只會) 顯示 windows vm。
   
   1. 在針對該 VM 開啟的視窗中，按一下 **[連線]**。
   
   1. 返回 [ **虛擬機器** ] 窗格，然後針對您想要連線的任何其他 vm 重複先前的兩個步驟。 當您完成時，請返回 [ **Windows 防火牆** ] 窗格。

- **如果您的 Windows 電腦不是 Azure VM，請完成下列步驟：**
   
   1. 選取 [ **在非 Azure Windows 電腦上安裝代理程式**]。
   
   1. 按一下 [ **下載 & 非 Azure Windows 機器的 [安裝代理程式] >** 連結。
   
   1. 在 [ **代理程式管理** ] 窗格中，選取 [ **下載 windows 代理程式 (64 位) ** 或視需要 **下載 windows 代理程式 (32 位) **。
   
   1. 將 **工作區識別碼**、 **主要金鑰**和 **次要金鑰** 字串複製到文字檔。 將該檔案和下載的安裝檔案複製到您的 Windows 電腦。 執行安裝檔案，然後在出現提示時，于安裝期間輸入文字檔中的識別碼和金鑰字串。
   
   1. 返回 [ **Windows 防火牆** ] 窗格。

完成 [ **指示** ] 索引標籤上的步驟之後，按一下 [ **安裝方案**]。

### <a name="next-steps-tab"></a>後續步驟索引標籤

- 若要深入瞭解您的 Windows 防火牆記錄檔資料，請參閱隨附于 **Windows 防火牆** 資料連線器的可用建議活頁簿和查詢範例。

- 若要查詢 **記錄**檔中的 Windows 防火牆資料，請在查詢視窗中輸入 **windows 防火牆** 。


## <a name="validate-connectivity"></a>驗證連線能力
 
由於 Windows 防火牆記錄只會在本機記錄檔達到容量時傳送給 Azure Sentinel，因此保留記錄檔的預設大小 4096 KB，很可能會導致收集延遲很高。 您可以藉由減少記錄檔大小來降低延遲。 請參閱 [設定 Windows 防火牆記錄](https://docs.microsoft.com/windows/security/threat-protection/windows-firewall/configure-the-windows-firewall-log)檔的指示。 請注意，在定義可能的記錄檔大小下限時 (1 KB) 幾乎會消除收集延遲，但可能也會對本機電腦的效能造成負面影響。 

## <a name="next-steps"></a>接下來的步驟
在本檔中，您已瞭解如何將 Windows 防火牆連線到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。

