---
title: Azure 資訊安全中心中的警示驗證（EICAR 測試檔案） |Microsoft Docs
description: 本文件可協助您在 Azure 資訊安全中心驗證安全性警示。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: f8f17a55-e672-4d86-8ba9-6c3ce2e71a57
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: cf732b92c1a208dd4c312ae442969ef958a021b4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84791175"
---
# <a name="alert-validation-eicar-test-file-in-azure-security-center"></a>Azure 資訊安全中心中的警示驗證 (EICAR 測試檔案)
這份文件可協助您了解如何驗證您的系統是否已針對 Azure 資訊安全中心警示正確設定。

## <a name="what-are-security-alerts"></a>什麼是安全性警示：
警示是當資訊安全中心偵測到您資源受到威脅時，所產生的通知。 它會排定優先順序並列出警示，以及快速調查問題所需的資訊。 資訊安全中心也會提供如何修復攻擊的建議。
如需詳細資訊，請參閱[資訊安全中心中的安全性警示](security-center-alerts-overview.md)和[管理與回應安全性警示](security-center-managing-and-responding-alerts.md)

## <a name="alert-validation"></a>警示驗證

* [Windows](#validate-windows)
* [Linux](#validate-linux)
* [Kubernetes](#validate-kubernetes)

## <a name="validate-alerts-on-windows-vms"></a>驗證 Windows Vm 上的警示<a name="validate-windows"></a>

在電腦上安裝資訊安全中心代理程式之後，請從您想要成為警示受攻擊資源的電腦執行下列步驟：

1. 將可執行檔（例如**calc.exe**）複製到電腦的桌面或其他方便的目錄，並將它重新命名為**ASC_AlertTest_662jfi039N.exe**。
1. 開啟命令提示字元，並使用引數（只是假的引數名稱）執行此檔案，例如：```ASC_AlertTest_662jfi039N.exe -foo```
1. 等待 5 到 10 分鐘，然後開啟安全性中心警示。 應該會出現警示。

> [!NOTE]
> 當您針對 Windows 檢查此測試**警示時，** 請確定**已啟用欄位引數審核**功能。 如果為**false**，則您需要啟用命令列引數的審核。 若要啟用它，請使用下列命令：
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## <a name="validate-alerts-on-linux-vms"></a>驗證 Linux Vm 上的警示<a name="validate-linux"></a>

在電腦上安裝資訊安全中心代理程式之後，請從您想要成為警示受攻擊資源的電腦執行下列步驟：
1. 將可執行檔案複製到方便存取的位置，並將它重新命名為 **./asc_alerttest_662jfi039n**，例如：

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. 開啟命令提示字元，然後執行此檔案：

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. 等待 5 到 10 分鐘，然後開啟安全性中心警示。 應該會出現警示。


## <a name="validate-alerts-on-kubernetes"></a>在 Kubernetes 上驗證警示<a name="validate-kubernetes"></a>

如果您要使用整合 Azure Kubernetes Service 的資訊安全中心預覽功能，請執行下列 kubectl 命令來測試您的警示是否正常運作：

```kubectl get pods --namespace=asc-alerttest-662jfi039n```

如需 Azure Kubernetes Service 與 Azure 資訊安全中心整合的詳細資訊，請參閱[這篇文章](azure-kubernetes-service-integration.md)。

## <a name="next-steps"></a>後續步驟
本文介紹警示驗證程序。 現在，您已熟悉此驗證，請嘗試下列文章︰

* [驗證 Azure 資訊安全中心中的 Azure Key Vault 威脅偵測](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336)
* [管理和回應 Azure 資訊安全中心中的安全性警示](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)-瞭解如何管理警示，以及回應資訊安全中心中的安全性事件。
* [Azure 資訊安全中心中的安全性健全狀況監視](security-center-monitoring.md)-瞭解如何監視 Azure 資源的健康情況。
* [瞭解 Azure 資訊安全中心中的安全性警示](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)-瞭解不同類型的安全性警示。
