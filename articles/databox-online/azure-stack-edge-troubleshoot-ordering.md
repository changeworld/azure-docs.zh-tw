---
title: 使用 Azure 入口網站針對 Azure Stack Edge Pro 訂購問題進行疑難排解 |Microsoft Docs
description: 說明如何針對 Azure Stack Edge Pro 訂購問題進行疑難排解。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 08/11/2020
ms.author: alkohli
ms.openlocfilehash: be7d477d08b025fd450bfa859e8eb677ba680143
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96446381"
---
# <a name="troubleshoot-your-azure-stack-edge-pro-ordering-issues"></a>針對您的 Azure Stack Edge Pro 訂購問題進行疑難排解

本文說明如何針對 Azure Stack Edge Pro 訂購問題進行疑難排解。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
>
> * 針對排序問題進行疑難排解

## <a name="unsupported-subscription-or-region"></a>不支援的訂用帳戶或區域

**錯誤描述：** 在 Azure 入口網站中，如果您收到錯誤：

*不支援選取的訂用帳戶或區域。選擇不同的訂用帳戶或區域。*

![不支援的訂用帳戶或區域](media/azure-stack-edge-troubleshoot-ordering/azure-stack-edge-troubleshoot-ordering-01.png)

**建議的解決方案：**  請確定您使用的是支援的訂用帳戶，例如 [Microsoft Enterprise 合約 (EA)](https://azure.microsoft.com/overview/sales-number/)、 [雲端解決方案提供者 (CSP)](/partner-center/azure-plan-lp)或 [Microsoft Azure 贊助](https://azure.microsoft.com/offers/ms-azr-0036p/)。 不支援隨用隨付訂用帳戶。 如需詳細資訊，請參閱 [Azure Stack Edge 資源必要條件](azure-stack-edge-deploy-prep.md#prerequisites)。

有可能是 Microsoft 可能允許依個案進行訂用帳戶類型升級。 請洽詢 [Microsoft 支援服務](https://azure.microsoft.com/support/options/) ，讓他們可以瞭解您的需求並適當地調整這些限制。

## <a name="selected-subscription-type-not-supported"></a>不支援選取的訂用帳戶類型

**錯誤：** 您有 EA、CSP 或贊助的訂用帳戶，而且您會收到下列錯誤：

*不支援選取的訂用帳戶類型。請確定您使用的是支援的訂用帳戶。 [深入瞭解](azure-stack-edge-deploy-prep.md#prerequisites)。如果使用支援的訂用帳戶類型，請確定已 `Microsoft.DataBoxEdge` 註冊該提供者。如需如何註冊的詳細資訊，請參閱 [註冊資源提供者](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers)*。

**建議的解決方案：** 請遵循下列步驟來註冊您的 Azure Stack Edge 資源提供者：

1. 在 Azure 入口網站中，移至 [**首頁**  >  **訂閱**]。

2. 選取您將用來訂購裝置的訂用帳戶。

3. 選取 **資源提供者** ，然後搜尋 **microsoft.databoxedge**。

    ![註冊資源提供者](media/azure-stack-edge-troubleshoot-ordering/azure-stack-edge-troubleshoot-ordering-02.png)

如果您沒有註冊資源提供者的擁有者或參與者存取權，您會看到下列錯誤： *訂 &lt; 用帳戶訂用帳戶名稱 &gt; 沒有註冊資源提供者 (s 的許可權) ： microsoft.databoxedge。*

如需詳細資訊，請參閱 [註冊資源提供者](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers)。

## <a name="microsoftdataboxedge-not-registered-for-subscription"></a>未註冊訂用帳戶的 Microsoft Microsoft.databoxedge

**錯誤：** 在 Azure 入口網站中，您可以選取要用於 Azure Stack Edge Pro 或資料箱閘道的訂用帳戶，並取得下列錯誤：

*資源提供者 (s) ： microsoft.databoxedge 未註冊訂用帳戶的 &lt; 名稱 &gt; ，而且您沒有註冊訂用帳戶訂用帳戶 &lt; 名稱 &gt; 資源提供者的許可權*。

**建議的解決方案：** 提升您的訂用帳戶存取權，或尋找具有擁有者或參與者存取權的人員，以註冊資源提供者。

## <a name="resource-disallowed-by-policy"></a>原則不允許的資源

**錯誤：** 在 Azure 入口網站中，您會嘗試註冊資源提供者，並取得下列錯誤：

*&lt; &gt; 原則不允許資源資源名稱。 (代碼： RequestDisallowedByPolicy) 。計畫：拒絕一般不需要的資源類型。原則：不允許的資源類型。*

**建議的解決方案：** 發生此錯誤的原因是現有的 Azure 原則封鎖了資源建立。 Azure 原則是由組織的系統管理員所設定，以確保在使用或建立 Azure 資源時的合規性。 如果有任何這類原則封鎖 Azure Stack Edge 建立資源，請洽詢您的系統管理員以編輯您的 Azure 原則。

## <a name="next-steps"></a>後續步驟

* 深入瞭解如何針對 [您的 Azure Stack Edge Pro 問題進行疑難排解](azure-stack-edge-troubleshoot.md)。