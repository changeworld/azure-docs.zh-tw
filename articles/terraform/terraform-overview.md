---
title: 搭配 Azure 使用 Terraform
description: 使用 Terraform 設定 Azure 基礎結構版本和部署的簡介。
ms.topic: overview
ms.date: 10/26/2019
ms.openlocfilehash: 05b92fdf8c0a0f84d2f29b4aa7479850b2721441
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "77472157"
---
# <a name="terraform-with-azure"></a>搭配 Azure 使用 Terraform

[Hashicorp Terraform](https://www.terraform.io/) (英文) 是開放原始碼工具，可用來佈建和管理雲端基礎結構。 它會在組態檔中制訂基礎結構，以說明雲端資源的拓撲。 這些資源包括虛擬機器、儲存體帳戶和網路介面。 Terraform CLI 提供了簡單的機制，以將組態檔部署至 Azure 及進行其版本管理。

本文說明使用 Terraform 來管理 Azure 基礎結構的優點。

## <a name="automate-infrastructure-management"></a>將基礎結構管理自動化。

Terraform 的範本型設定檔讓您能夠以可重複且可預測的方式定義、佈建和設定 Azure 資源。 自動化基礎結構有幾項優點：

- 部署和管理基礎結構時，降低發生人為錯誤的可能性。
- 可多次部署同樣的範本，以建立完全相同的開發、測試和生產環境。
- 可依需求建立開發和測試環境，降低建立這些環境的成本。

## <a name="understand-infrastructure-changes-before-being-applied"></a>在套用基礎結構變更前先加以了解

隨著資源拓撲變得更為複雜，了解基礎結構變更的意義和影響也會變得困難。

Terraform CLI 可讓使用者先驗證和預覽基礎結構變更，再加以應用。 以安全的方式預覽基礎結構變更有幾個好處：
- 小組成員可快速地了解預計的變更和其影響，因而更有效地進行共同作業。
- 在開發過程中能提早攔截非預期的變更

## <a name="deploy-infrastructure-to-multiple-clouds"></a>將基礎結構部署到多個雲端

Terraform 非常適合用來部署多個雲端提供者間的基礎結構。 它可讓開發人員使用一致的工具來管理每個基礎結構定義。

## <a name="next-steps"></a>後續步驟

既然您已了解 Terraform 的概觀和其優點，以下是建議的後續步驟：

- 了解如何[安裝 Terraform 並設定它以使用 Azure](terraform-install-configure.md)。
- [使用 Terraform 建立 Azure 虛擬機器](terraform-create-complete-vm.md)
- 探索[適用於 Terraform 的 Azure Resource Manager 模組](https://www.terraform.io/docs/providers/azurerm/) \(英文\) 