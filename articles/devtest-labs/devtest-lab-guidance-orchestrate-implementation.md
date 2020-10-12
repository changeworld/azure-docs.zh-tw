---
title: 協調 Azure DevTest Labs 的實作
description: 此文章提供在組織中協調 Azure DevTest Labs 實作的指導方針。
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 0438445f99a22ed9fd239156044d6b3f6b477edc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85480842"
---
# <a name="orchestrate-the-implementation-of-azure-devtest-labs"></a>協調 Azure DevTest Labs 的實作
此文章提供快速部署和實作 Azure DevTest Labs 的建議方法。 下圖強調作為規範性指南的整體程序，同時觀察支援各種產業需求和案例的彈性。

![實作 Azure DevTest Labs 的步驟](./media/devtest-lab-guidance-orchestrate-implementation/implementation-steps.png)

## <a name="assumptions"></a>假設
此文章假設您在實作 DevTest Labs 試驗之前，就已備妥下列項目：

- **Azure 訂用帳戶**：試驗小組具備將資源部署到 Azure 訂用帳戶的存取權。 如果工作負載只有開發和測試，建議選取企業版 DevTest 供應項目，以用於 Windows 虛擬機器上其他可用的映像和較優惠的費率。
- **內部部署存取**：必要時，已經設定內部部署存取。 您可以透過站對站 VPN 連線或 Express Route 來完成內部部署存取。 透過 Express Route 的連線通常需要數週的時間來建立，建議在開始專案之前先備妥 Express Route。
- **試驗小組**：已識別出最初使用 DevTest Labs 的開發專案小組，以及適用於開發或測試活動，並針對那些小組建立需求/目標/目的。

## <a name="milestone-1-establish-initial-network-topology-and-design"></a>里程碑 1：建立初始的網路拓撲和設計
部署 Azure DevTest Labs 解決方案時的第一個焦點領域是建立虛擬機器的計劃性連線。 下列步驟概述必要的程序：

1. 定義在 Azure 中指派給 DevTest Labs 訂用帳戶的**初始 IP 位址範圍**。 此步驟需要預測所有 VM 的預期使用量，讓您可提供足夠大的區塊以供未來擴充使用。
2. 將**所需存取的方法**識別到 DevTest Labs (例如，外部 / 內部存取)。 此步驟的重點是判斷虛擬機器是否具有公用 IP 位址 (也就是可從網際網路直接存取)。
3. 針對 Azure 雲端環境的其餘部分和內部部署識別並建立**連線的方法**。 如果已啟用透過 Express Route 強制執行路由，虛擬機器很可能需要適當的 Proxy 設定以周遊企業防火牆。
4. 如果 VM **已加入網域**，請判斷它們是否要加入雲端式網域 (例如 AAD 目錄服務) 或內部部署網域。 針對內部部署，判斷虛擬機器要加入 Active Directory 內的哪些組織單位 (OU)。 此外，確認使用者有權加入 (或建立能夠在網域中建立電腦記錄的服務帳戶)。

## <a name="milestone-2-deploy-the-pilot-lab"></a>里程碑 2：部署試驗實驗室
當網路拓撲就緒之後，就能採用下列步驟來建立第一個/試驗實驗室：

1. 建立初始的 DevTest Labs 環境。
2. 判斷允許的 VM 映像和大小，以便與實驗室搭配使用。 決定是否可將自訂映像上傳至 Azure，以便與 DevTest Labs 搭配使用。
3. 藉由為實驗室 (實驗室擁有者和實驗室使用者) 建立初始角色型存取控制 (RBAC)，來保護對實驗室的存取。 我們建議搭配 Azure Active Directory 使用同步處理的 Active Directory 帳戶，以使用 DevTest Labs 進行身分識別。
4. 設定 DevTest Labs 來使用原則，例如排程、成本管理、可認領 VM、自訂映像或公式。
5. 建立 Azure Repos/Git 之類的線上存放庫。
6. 決定使用公用或私人存放庫或兩者的組合。 組織 JSON 範本以用於部署和長期維持流程。
7. 視需要建立自訂成品。 此為選用步驟。 

## <a name="milestone-3-documentation-support-learn-and-improve"></a>里程碑 3：文件、支援、了解及改善
最初的試驗小組可能需要深入支援以便快速入門。 使用他們的體驗來確保正確的文件及支援均已就緒，可繼續推出 Azure DevTest Labs。

1. 將試驗小組介紹給其新的 DevTest Labs 資源 (示範、文件)
2. 根據試驗小組的體驗，視需要規劃並提供文件
3. 將用來使新小組上線的程序定形 (建立及設定實驗室、提供存取等等)
4. 根據最初的理解，確認 IP 位址空間的原始預測仍然合理且準確
5. 確定已完成適當的合規性與安全性檢閱

## <a name="next-steps"></a>接下來的步驟
請參閱本系列的下一篇文章：[治理 Azure DevTest Labs 基礎結構](devtest-lab-guidance-governance-resources.md)
