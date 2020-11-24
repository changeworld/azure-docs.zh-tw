---
title: Red Hat Enterprise Linux 擴充生命週期支援
description: 深入瞭解新增 Red Hat Enterprise 擴充生命週期支援新增至
author: mathapli
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 04/16/2020
ms.author: mathapli
ms.reviewer: cynthn
ms.openlocfilehash: 1f31cc982e5e7f92cfe7e8adf588dd96628f2c6f
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95744036"
---
# <a name="red-hat-enterprise-linux-rhel-extended-lifecycle-support"></a>Red Hat Enterprise Linux (RHEL) 延伸生命週期支援
本文提供 Red Hat Enterprise 映射的延伸生命週期支援相關資訊：
* Red Hat Enterprise Linux 6  

## <a name="red-hat-enterprise-linux-6-lifecycle"></a>Red Hat Enterprise Linux 6 生命週期
自2020年11月30日起，Red Hat Enterprise Linux 6 將達到維護階段的結束。 維護階段會接著延長存留期。 當 Red Hat Enterprise Linux 6 從完整/維護階段轉換時，強烈建議您升級至 Red Hat Enterprise Linux 7 或8。 如果客戶必須維持 Red Hat Enterprise Linux 6，建議您 (ELS) 附加元件，新增 Red Hat Enterprise Linux 的延伸生命週期支援。

## <a name="steps-to-add-extended-lifecycle-support-on-marketplace-pay-as-you-go-vms"></a>在 Marketplace 隨用隨付 Vm 上新增延伸生命週期支援的步驟
1. 使用您想要為其新增 ELS 支援的 Vm 您的連絡人詳細資料和訂用帳戶資訊，填滿 [這裡提供的 els 表單](https://aka.ms/els-form) 。 您也可以在表單中取得定價詳細資料的新增功能。
1. Azure Red Hat Enterprise Linux 小組會與您聯繫，以在1-2 工作天內新增適用于 ELS 支援的 Vm 清單。 請檢查清單，並同意「附加于定價」的回應。
1. Azure Red Hat Enterprise Linux 團隊將會共用將 ELS 用戶端套件新增至 Vm 的步驟。 遵循電子郵件中提供的步驟，以繼續接收軟體維護 (錯誤和安全性修正) 和 Red Hat Enterprise Linux 6 的支援。

> [!Note]
> 請勿與您組織外部的任何人共用使用 RHEL ELS add on 的步驟。 聯繫以 AzureRedHatELS@microsoft.com 取得支援或任何其他問題。

## <a name="frequently-asked-questions"></a>常見問題集

#### <a name="im-running-red-hat-enterprise-linux-6-and-cant-migrate-to-a-later-version-at-this-time-what-options-do-i-have"></a>我正在執行 Red Hat Enterprise Linux 6，目前無法遷移至更新版本。 我有哪些選項？
* 繼續 Red Hat Enterprise Linux 6 執行，並購買延長生命週期支援 (ELS) Add-On 存放庫，以繼續獲得有限的軟體維護和技術支援 (請參閱以下) 的升級程式和定價詳細資料。
* 您可以儘快遷移至 Red Hat Enterprise Linux 7 或8。

#### <a name="what-is-the-additional-charge-for-using-red-hat-enterprise-linux-extended-life-cycle-support-els-add-on"></a>使用 Red Hat Enterprise Linux 延長生命週期支援 (ELS) 附加元件的額外費用為何？
您可以使用[ELS 表單](https://aka.ms/els-form)來找到與延長生命週期支援相關的成本


## <a name="next-steps"></a>後續步驟

* 若要在 Azure 中查看 RHEL 映射的完整清單，請參閱 [Red Hat Enterprise Linux (azure 中提供的 rhel) 映射](./redhat-imagelist.md)。
* 若要深入瞭解 Azure Red Hat 更新基礎結構，請參閱 [適用于 azure 中隨選 RHEL vm 的 Red Hat 更新基礎結構](./redhat-rhui.md)。
* 若要深入瞭解 RHEL BYOS 供應專案，請參閱 [在 Azure 中 Red Hat Enterprise Linux 自備訂用帳戶黃金映射](./byos.md)。
* 如需所有 RHEL 版本的 Red Hat 支援原則的相關資訊，請參閱 [Red Hat Enterprise Linux 生命週期](https://access.redhat.com/support/policy/updates/errata)。