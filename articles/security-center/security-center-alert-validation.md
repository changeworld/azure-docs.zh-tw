---
title: Azure 安全中心的警報驗證（EICAR 測試檔案） |微軟文檔
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
ms.openlocfilehash: 5146878adf10e452f38fecb115ec40792ffa84f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139992"
---
# <a name="alert-validation-eicar-test-file-in-azure-security-center"></a>Azure 資訊安全中心中的警示驗證 (EICAR 測試檔案)
這份文件可協助您了解如何驗證您的系統是否已針對 Azure 資訊安全中心警示正確設定。

## <a name="what-are-security-alerts"></a>什麼是安全性警示：
警示是當資訊安全中心偵測到您資源受到威脅時，所產生的通知。 它確定警報的優先順序並列出快速調查問題所需的資訊。 資訊安全中心也提供如何修復攻擊的建議。
有關詳細資訊，請參閱[安全中心中的安全警報](security-center-alerts-overview.md)[以及管理和回應安全警報](security-center-managing-and-responding-alerts.md)

## <a name="alert-validation"></a>警示驗證

* [Windows](#validate-windows)
* [Linux](#validate-linux)
* [庫貝內特斯](#validate-kubernetes)

## <a name="validate-alerts-on-windows-vms"></a>驗證 Windows VM 上的警報<a name="validate-windows"></a>

在電腦上安裝安全中心代理後，請按照要成為警報受到攻擊的資源的電腦執行以下步驟：

1. 將可執行檔（例如**calc.exe）** 複製到電腦的桌面或其他方便的目錄，並將其重命名為**ASC_AlertTest_662jfi039N.exe**。
1. 打開命令提示符，使用參數（只是假參數名稱）執行此檔，例如：```ASC_AlertTest_662jfi039N.exe -foo```
1. 等待 5 到 10 分鐘，然後開啟安全性中心警示。 應顯示類似于以下[示例](#alert-validate)的警報：

> [!NOTE]
> 在查看 Windows 的此測試警報時，請確保欄位**參數審核為** **true**。 如果為**false，** 則需要啟用命令列參數審核。 要啟用它，請使用以下命令：
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## <a name="validate-alerts-on-linux-vms"></a>驗證 Linux VM 上的警報<a name="validate-linux"></a>

在電腦上安裝安全中心代理後，請按照要成為警報受到攻擊的資源的電腦執行以下步驟：
1. 將可執行檔案複製到方便的位置並將其重命名為 **./asc_alerttest_662jfi039n，** 例如：

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. 打開命令提示並執行此檔：

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. 等待 5 到 10 分鐘，然後開啟安全性中心警示。 應顯示類似于以下[示例](#alert-validate)的警報：

### <a name="alert-example"></a>警報示例<a name="alert-validate"></a>

![警報驗證示例](./media/security-center-alert-validation/security-center-alert-validation-fig2.png) 


## <a name="validate-alerts-on-kubernetes"></a>驗證庫伯奈斯上的警報<a name="validate-kubernetes"></a>

如果使用集成 Azure Kubernetes 服務的安全中心預覽功能，請運行以下庫布 ectl 命令以測試警報是否正常工作：

```kubectl get pods --namespace=asc-alerttest-662jfi039n```

有關 Azure 庫伯內斯服務和 Azure 安全中心集成的詳細資訊，請參閱[本文](azure-kubernetes-service-integration.md)。

## <a name="next-steps"></a>後續步驟
本文介紹警示驗證程序。 現在，您已熟悉此驗證，請嘗試下列文章︰

* [在 Azure 安全中心驗證 Azure 金鑰保存庫威脅檢測](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336)
* [管理和回應 Azure 安全中心中的安全警報](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)- 瞭解如何管理警報和回應安全中心中的安全事件。
* [Azure 資訊安全中心的安全性健全狀況監視](security-center-monitoring.md) - 了解如何監視 Azure 資源的健全狀況。
* [瞭解 Azure 安全中心中的安全警報](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)- 瞭解不同類型的安全警報。
