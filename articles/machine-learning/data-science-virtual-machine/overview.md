---
title: 什麼是 Azure 資料科學虛擬機器
titleSuffix: Azure Data Science Virtual Machine
description: 適用於 Windows 和 Linux 資料科學虛擬機器的重要分析案例與元件。
keywords: 資料科學工具、資料科學虛擬機器、資料科學工具、linux 資料科學
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 12/31/2019
ms.openlocfilehash: ea53ee3f3c1d39652982b6343b1e634dac1fbc75
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/21/2020
ms.locfileid: "77525918"
---
# <a name="what-is-the-azure-data-science-virtual-machine-for-linux-and-windows"></a>適用於 Linux 和 Windows 的 Azure 資料科學虛擬機器是什麼？

資料科學虛擬機器 (DSVM) 是 Azure 雲端平台上的自訂 VM 映像，專為進行資料科學建置。 它已預先安裝和預先設定許多常用的資料科學工具，以開始建置智慧應用程式進行進階分析。 

下列項目有提供 DSVM：
+ **Windows Server 2019 (預覽)**
+ **Ubuntu 18.04 LTS (預覽)**
+ Windows Server 2016
+ Ubuntu 16.04 LTS 和 CentOS 7.4


> [!NOTE]
> 所有適用於深度學習的 VM 工具都已併入資料科學虛擬機器中。 


## <a name="why-choose-the-dsvm"></a>為何選擇 DSVM？
資料科學虛擬機器的目標在於提供無衝突、預先設定的資料科學環境，讓所有技能等級和各個企業的資料專業人員使用。 您可以布建 DSVM，而不是自行推出可比較的工作區。 該選擇可以為您省下幾天或甚至是「幾周」  的安裝、設定和套件管理程序。 配置 DSVM 之後，您可以立即開始進行資料科學專案。

## <a name="sample-use-cases"></a>範例使用案例

以下說明 DSVM 客戶的一些常見使用案例。

### <a name="moving-data-science-workloads-to-the-cloud"></a>將資料科學工作負載移至雲端

DSVM 可為想將其本機桌面取代為受控雲端桌面的資料科學小組提供基準組態，以確保小組中的資料科學家會使用一致的設定來驗證實驗和提升共同作業能力。 它也可減輕系統管理員的負擔，進而降低成本。 此負擔減輕後，評估、安裝及維護各種軟體套件以執行進階分析所需的時間，也得以縮短。

### <a name="data-science-training-and-education"></a>資料科學訓練和教育
教導資料科學課程的企業訓練者和教育者通常會提供虛擬機器映像。 此映像可確保他們的學生擁有一致的設定且範例如預期般運作。 

DSVM 會建立具有一致設定的隨選環境，緩解支援和不相容性挑戰。 在必須經常建置這些環境的案例中，特別是針對較短的訓練課程，可以有大幅獲益。

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>大型專案的隨選彈性容量
資料科學黑客松 (Hackathon)/競賽或大型資料模型化和探索需要相應放大硬體容量，通常是很短的期間。 DSVM 可幫助在相應放大的伺服器上快速隨選複寫資料科學環境，允許可執行強大運算資源的實驗。

### <a name="custom-compute-power-for-azure-notebooks"></a>自訂 Azure Notebooks 的計算能力
[Azure Notebooks](../../notebooks/azure-notebooks-overview.md) 是免費的託管服務，無需任何安裝程序，就能用來在雲端中開發、執行和共用 Jupyter Notebook。 免費服務層級的限制為 4 GB 記憶體和 1 GB 資料。 

若要解除所有的限制，您可以將 Notebooks 專案附加至 DSVM 或在 Jupyter 伺服器上執行的任何其他 VM。 如果您以使用 Azure Active Directory 的帳戶 (例如公司帳戶) 登入 Azure Notebooks，Notebooks 會自動顯示該帳戶關聯訂用帳戶中的任何資料科學 DSVM。 您可以[將 DSVM 連結至 Azure Notebooks](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier) 以擴充可用的計算能力。

### <a name="short-term-experimentation-and-evaluation"></a>短期實驗和評估
您可以使用 DSVM 來評估或學習新的資料科學[工具](./tools-included.md)，特別是透過我們已發佈的一些[範例和逐步解說](./dsvm-samples-and-walkthroughs.md)。


### <a name="deep-learning-with-gpus"></a>使用 GPU 的深度學習
在 DSVM 中，您的訓練模型可以在以圖形處理器 (GPU) 為基礎的硬體上使用深度學習演算法。 藉由利用 Azure 平台的 VM 規模調整功能，DSVM 可協助您根據需求在雲端中使用 GPU 型硬體。 當訓練大型模型或需要高速計算同時保留相同的 OS 磁碟時，您可以切換到 GPU 型 VM。 您可以選擇任何已啟用 GPU、且含有 DSVM 的 N 系列虛擬機器 SKU。 請注意，Azure 免費帳戶不支援已啟用 GPU 的虛擬機器 SKU。

Windows Server 2016 版的 DSVM 預先安裝了 GPU 驅動程式、架構和 GPU 版本的深入學習架構。 在 Linux 版本上，GPU 的深入學習於 CentOS 和 Ubuntu DSVM 上啟用。 

您可以將 Ubuntu、CentOS 或 Windows-2016 版 DSVM 部署到非 GPU 型的 Azure 虛擬機器。 在此情況下，所有深度學習架構都會回復為 CPU 模式。
 
[深入了解可用的深度學習和 AI 架構](dsvm-tools-deep-learning-frameworks.md)。

<a name="included"></a>

## <a name="whats-included-on-the-dsvm"></a>DSVM 包含哪些內容？

如需 Windows 和 Linux DSVM 的完整工具清單，請參閱[這裡](tools-included.md)。

## <a name="next-steps"></a>後續步驟

請透過下列文章來深入了解：

+ Windows：
  + [設定 Windows DSVM](provision-vm.md)
  + [您可以在 Windows DSVM 上進行的十件事](vm-do-ten-things.md)

+ Linux：
  + [設定 Linux DSVM (Ubuntu)](dsvm-ubuntu-intro.md)
  + [設定 Linux DSVM (CentOS)](linux-dsvm-intro.md)
  + [Linux DSVM 上的資料科學](linux-dsvm-walkthrough.md)
