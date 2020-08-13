---
title: 使用 Azure 入口網站對 Azure Stack Edge 訂購問題進行疑難排解 |Microsoft Docs
description: 說明如何針對 Azure Stack 邊緣順序問題進行疑難排解。
services: databox
author: priestlg
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 08/11/2020
ms.author: v-grpr
ms.openlocfilehash: d403fb0da27e3f91e00f3fd8a2fbea77e915898b
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/12/2020
ms.locfileid: "88136732"
---
# <a name="troubleshoot-your-azure-stack-edge-ordering-issues"></a>針對您的 Azure Stack 邊緣訂購問題進行疑難排解

本文說明如何針對 Azure Stack 的邊緣順序問題進行疑難排解。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
>
> * 針對排序問題進行疑難排解

## <a name="unsupported-subscription-or-region"></a>不支援的訂用帳戶或區域

**錯誤描述：** 在 Azure 入口網站中，如果您收到錯誤：

*不支援選取的訂用帳戶或區域。請選擇不同的訂用帳戶或區域。*

![不支援的訂用帳戶或區域](media/azure-stack-edge-troubleshoot-ordering/azure-stack-edge-troubleshoot-ordering-01.png)

**建議的解決方案：** 請確定您使用的是[Microsoft Enterprise 合約 (EA) ](https://azure.microsoft.com/overview/sales-number/)、[雲端解決方案提供者 (CSP) ](https://docs.microsoft.com/partner-center/azure-plan-lp)或[Microsoft Azure 贊助](https://azure.microsoft.com/offers/ms-azr-0036p/)等支援的訂用帳戶。 不支援隨用隨付訂用帳戶。 如需詳細資訊，請參閱[Azure Stack Edge 資源必要條件](azure-stack-edge-deploy-prep.md#prerequisites)。

Microsoft 可能會以個案為基礎，允許訂用帳戶類型升級。 請洽詢[Microsoft 支援服務](https://azure.microsoft.com/support/options/)，讓他們能夠瞭解您的需求，並適當地調整這些限制。

## <a name="selected-subscription-type-not-supported"></a>不支援選取的訂用帳戶類型

**錯誤：** 您有 EA、CSP 或贊助的訂用帳戶，並收到下列錯誤：

*不支援選取的訂用帳戶類型。請確定您使用的是支援的訂用帳戶。[深入瞭解](azure-stack-edge-deploy-prep.md#prerequisites)。如果使用支援的訂用帳戶類型，請確定已 `Microsoft.DataBoxEdge` 註冊提供者。如需如何註冊的資訊，請參閱[註冊資源提供者](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers)*。

**建議的解決方案：** 請遵循下列步驟來註冊您的 Azure Stack Edge 資源提供者：

1. 在 Azure 入口網站中，移至 [**首頁**] [訂用帳戶]  >  ** **。

2. 選取您要用來訂購裝置的訂用帳戶。

3. 選取 [**資源提供者**]，然後搜尋**DataBoxEdge**。

    ![註冊資源提供者](media/azure-stack-edge-troubleshoot-ordering/azure-stack-edge-troubleshoot-ordering-02.png)

如果您沒有「擁有者」或「參與者」存取權來註冊資源提供者，您會看到下列錯誤：*訂 &lt; 用帳戶訂用帳戶名稱沒有 &gt; (s) 註冊資源提供者的許可權： DataBoxEdge。*

如需詳細資訊，請參閱[註冊資源提供者](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers)。

## <a name="microsoftdataboxedge-not-registered-for-subscription"></a>DataBoxEdge 未註冊訂用帳戶

**錯誤：** 在 Azure 入口網站中，您可以選取要用於 Azure Stack Edge 或資料箱閘道的訂用帳戶，並取得下列錯誤：

*資源提供者 (s) ：未註冊訂 &lt; 用帳戶訂用帳戶名稱的 DataBoxEdge &gt; ，而且您沒有許可權可註冊訂用帳戶 &lt; 訂用 &gt; 帳戶名稱的資源提供者*。

**建議的解決方案：** 提升您的訂用帳戶存取權，或尋找具有擁有者或參與者存取權的人員，以註冊資源提供者。

## <a name="resource-disallowed-by-policy"></a>原則不允許的資源

**錯誤：** 在 Azure 入口網站中，您會嘗試註冊資源提供者並收到下列錯誤：

*&lt; &gt; 原則不允許資源資源名稱。 (碼： RequestDisallowedByPolicy) 。計畫：拒絕通常不需要的資源類型。原則：不允許的資源類型。*

**建議的解決方案：** 發生此錯誤的原因是現有的 Azure 原則會封鎖資源建立。 Azure 原則是由組織的系統管理員所設定，以確保在使用或建立 Azure 資源時的合規性。 如果有任何這類原則封鎖 Azure Stack 建立 Edge 資源，請洽詢您的系統管理員以編輯您的 Azure 原則。

## <a name="next-steps"></a>後續步驟

* 深入瞭解如何針對[您的 Azure Stack Edge 問題進行疑難排解](azure-stack-edge-troubleshoot.md)。
