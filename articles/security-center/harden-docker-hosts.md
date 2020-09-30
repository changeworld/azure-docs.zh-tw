---
title: 使用 Azure 資訊安全中心強化您的 Docker 主機並保護容器
description: 如何保護您的 Docker 主機，並確認它們符合 CIS Docker 基準測試的規範
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 3f59bae9864e533270b0bfea829f64ccf541a8a3
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91301426"
---
# <a name="harden-your-docker-hosts"></a>強化 Docker 主機

Azure 資訊安全中心識別裝載于 IaaS Linux Vm 上的非受控容器，或執行 Docker 容器的其他 Linux 機器。 安全性中心會持續評估這些容器的設定。 然後，它會將它們與 [網際網路安全性 (CIS) Docker 基準測試的中心](https://www.cisecurity.org/benchmark/docker/)進行比較。

如果您的容器無法滿足任何控制項，則 [安全性中心] 會包含 CIS Docker 基準測試的完整規則集，併發出警示。 當它找到錯誤配置時，安全性中心會產生安全性建議。 使用 [資訊中心的 **建議] 頁面** 來查看建議和補救問題。

當發現弱點時，就會在單一建議中分組。

>[!NOTE]
> 這些 CIS 基準測試檢查不會在 AKS 管理的實例或 Databricks 管理的 Vm 上執行。

## <a name="availability"></a>可用性

|層面|詳細資料|
|----|:----|
|釋放狀態：|正式上市 (GA)|
|定價：|需要 [適用于伺服器的 Azure Defender](defender-for-servers-introduction.md)|
|必要的角色和許可權：|主機所連接之工作區的**讀取器**|
|雲端：|![是](./media/icons/yes-icon.png) 商業雲端<br>![否](./media/icons/no-icon.png) 全國/主權 (US Gov、中國 Gov、其他 Gov) |
|||

## <a name="identify-and-remediate-security-vulnerabilities-in-your-docker-configuration"></a>找出並修復 Docker 設定中的安全性弱點

1. 在 [安全性中心] 功能表中，開啟 [ **建議** ] 頁面。

1. 您 **應補救容器安全性設定中** 的建議弱點，並選取建議。

    建議頁面會顯示 (Docker 主機) 受影響的資源。 

    :::image type="content" source="./media/monitor-container-security/docker-host-vulnerabilities-found.png" alt-text="補救容器安全性設定中弱點的建議 ":::

1. 若要查看及補救特定主機失敗的 CIS 控制項，請選取您要調查的主機。 

    > [!TIP]
    > 如果您在 [資產清查] 頁面上啟動，並從該處到達此建議，請 select 建議頁面上的 [ **採取動作** ] 按鈕。
    >
    > :::image type="content" source="./media/monitor-container-security/host-security-take-action-button.png" alt-text="補救容器安全性設定中弱點的建議 ":::

    Log Analytics 隨即開啟，並已準備好執行自訂作業。 預設的自訂查詢包含已評估之所有失敗規則的清單，以及協助您解決問題的指導方針。

    :::image type="content" source="./media/monitor-container-security/docker-host-vulnerabilities-in-query.png" alt-text="補救容器安全性設定中弱點的建議 ":::

1. 必要時，請調整查詢參數。

1. 當您確定命令適合您的主機時，請選取 [ **執行**]。


## <a name="next-steps"></a>後續步驟

Docker 強化只是「安全性中心」的容器安全性功能的其中一個層面。 

深入瞭解資訊 [安全中心的容器安全性](container-security.md)。