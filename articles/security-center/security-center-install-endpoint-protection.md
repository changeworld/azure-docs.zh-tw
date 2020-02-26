---
title: 使用 Azure 資訊安全中心管理端點保護 | Microsoft Docs
description: 瞭解資訊安全中心的端點保護監視，以及如何修復任何發生的問題。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 1599ad5f-d810-421d-aafc-892e831b403f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2019
ms.author: memildin
ms.openlocfilehash: e1ed403babe66b465fb1800dc8c5a90c7a8f1a08
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77604105"
---
# <a name="manage-endpoint-protection-issues-with-azure-security-center"></a>使用 Azure 資訊安全中心管理端點保護
Azure 資訊安全中心會監視反惡意程式碼防護的狀態，並在 [Endpoint protection 問題] 頁面底下回報此情況。 資訊安全中心會強調問題所在，例如偵測到威脅和防護不足，這些問題會造成虛擬機器 (VM) 和電腦容易遭受反惡意程式碼軟體威脅。 您可以使用 [端點保護問題] 的資訊，找出可解決找出的問題的方案。

資訊安全中心會報告下列端點保護問題：

- Azure VM 上未安裝端點保護 – Azure VM 未安裝支援的反惡意程式碼軟體解決方案。
- 非 Azure 電腦上未安裝端點保護 – 非 Azure 電腦未安裝支援的反惡意程式碼軟體。
- 端點保護健康情況：

  - 簽章已過期 - VM 和電腦上已安裝反惡意程式碼軟體解決方案，但是該解決方案沒有最新的反惡意程式碼軟體簽章。
  - 沒有即時保護 - VM 和電腦上已安裝反惡意程式碼軟體解決方案，但是並未設定即時保護。 該服務可能已停用，或者資訊安全中心可能因為不支援該解決方案而無法取得狀態。 如需支援的解決方案清單，請參閱[夥伴整合](security-center-services.md#endpoint-supported)。
  - 未回報 – 已安裝反惡意程式碼軟體解決方案，但是未報告資料。
  - 未知 - 已安裝反惡意程式碼軟體解決方案，但是其狀態為未知或報告未知錯誤。

    > [!NOTE]
    > 如需與資訊安全中心之端點保護安全性解決方案的清單，請參閱[整合安全性解決方案](security-center-services.md#endpoint-supported)。
    >
    >

## <a name="implement-the-recommendation"></a>實作建議
端點保護會以建議的形式在資訊安全中心中呈現。 如果您的環境容易遭受反惡意程式碼軟體威脅，在 [建議] 下和 [計算] 下會顯示這項建議。 若要查看**端點保護問題儀表板**，您必須遵循計算工作流程。

在此範例中，我們使用 [計算]。  我們將探討如何在 Azure VM 和非 Azure 電腦上安裝反惡意程式碼軟體。

## <a name="install-antimalware-on-azure-vms"></a>在 Azure VM 上安裝反惡意程式碼軟體

1. 在 [資訊安全中心主功能表] 或 **[總覽**] 底下，選取 [**計算 & 應用程式**]。

   ![選取 [計算]][1]

2. 在 [計算] 下，選取 [端點保護問題]。 [端點保護問題] 儀表板隨即開啟。

   ![選取 [端點保護問題]][2]

   儀表板的頂端提供：

   - 已安裝的端點保護提供者 - 列出資訊安全中心識別的不同提供者。
   - 已安裝的端點保護健康情況狀態 - 顯示已安裝端點保護解決方案的電腦和 VM 健康情況狀態。 該圖表顯示健康的 VM 和電腦數量，以及保護不足的數量。
   - 偵測到惡意程式碼 – 顯示資訊安全中心報告偵測到惡意程式碼的 VM 和電腦數量。
   - 受攻擊的電腦-顯示資訊安全中心正向惡意程式碼報告攻擊的 Vm 和電腦數目。

   儀表板底部有端點保護問題的清單，包含下列資訊：  

   - **總計** - 受此問題影響的 VM 和電腦數量。
   - 受此問題影響的 VM 和電腦累計數量的橫條。 橫條的色彩表示優先順序：

      - 紅色 - 高優先順序，應立即處理
      - 橘色 - 中等優先順序，應儘速處理

3. 選取 [Azure VM 上未安裝端點保護]。

   ![選取 [Azure VM 上未安裝端點保護]][3]

4. 在 [Azure VM 上未安裝端點保護] 下，有未安裝反惡意程式碼軟體的 Azure VM 清單。  您可以在清單中選擇在所有 VM 上安裝反惡意程式碼軟體，也可以按一下特定的 VM，選取個別 VM 安裝反惡意程式碼軟體。
5. 在 [選取端點保護] 下，選取想要使用的端點保護解決方案。 在此範例中，選取 [Microsoft Antimalware]。
6. 將會顯示 Endpoint Protection 解決方案的其他相關資訊。 選取 [建立]。

## <a name="install-antimalware-on-non-azure-computers"></a>在非 Azure 電腦上安裝反惡意程式碼軟體

1. 返回 [端點保護問題]，並選取 [非 Azure 電腦上未安裝端點保護]。

   ![選取 [非 Azure 電腦上未安裝端點保護]][4]

2. 在 [非 Azure 電腦上未安裝端點保護] 下，選取工作區。 篩選出工作區的 Azure 監視器記錄搜尋查詢隨即開啟，並列出遺失反惡意程式碼的電腦。 從清單中選取電腦以了解更多資訊。

   ![Azure 監視器記錄檔搜尋][5]

會出現僅針對該電腦篩選出的其他搜尋結果。

  ![Azure 監視器記錄檔搜尋][6]

> [!NOTE]
> 建議為所有 VM 和電腦佈建端點保護，以便識別和移除病毒、間諜軟體及其他惡意軟體。
>
>

## <a name="next-steps"></a>後續步驟
本文說明了如何實作資訊安全中心建議的「安裝端點保護」。 若要深入了解如何在 Azure 中啟用反惡意程式碼軟體，請參閱下列文件：

* [適用於雲端服務和虛擬機器的 Microsoft Antimalware](../security/fundamentals/antimalware.md) -- 了解如何部署 Microsoft Antimalware。

若要深入了解資訊安全中心，請參閱下列文件：

* [設定 Azure 資訊安全中心的安全性原則](tutorial-security-policy.md) -- 了解如何設定安全性原則。
* [管理 Azure 資訊安全中心的安全性建議](security-center-recommendations.md) -- 了解建議如何協助您保護您的 Azure 資源。
* [Azure 資訊安全中心的安全性健全狀況監視](security-center-monitoring.md) -- 了解如何監視 Azure 資源的健全狀況。
* [管理與回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md) -- 了解如何管理與回應安全性警示。
* [使用 Azure 資訊安全中心監視合作夥伴解決方案](security-center-partner-solutions.md) -- 了解如何監視合作夥伴解決方案的健全狀況。

<!--Image references-->
[1]:./media/security-center-install-endpoint-protection/compute.png
[2]:./media/security-center-install-endpoint-protection/endpoint-protection-issues.png
[3]:./media/security-center-install-endpoint-protection/install-endpoint-protection.png
[4]:./media/security-center-install-endpoint-protection/endpoint-protection-issues-computers.png
[5]:./media/security-center-install-endpoint-protection/log-search.png
[6]:./media/security-center-install-endpoint-protection/info-filtered-to-computer.png
