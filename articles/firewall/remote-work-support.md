---
title: Azure 防火牆遠端工作支援
description: 本文說明 Azure 防火牆可如何支援您的遠端工作強制需求。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: victorh
ms.openlocfilehash: 68789d3b8a4be51a381e95d6e6f840331b46b4e9
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2020
ms.locfileid: "91400106"
---
# <a name="azure-firewall-remote-work-support"></a>Azure 防火牆遠端工作支援

Azure 防火牆是受控的雲端式網路安全性服務，可保護您的 Azure 虛擬網路資源。 它是完全具狀態的防火牆即服務，具有內建的高可用性和不受限制的雲端延展性。

## <a name="virtual-desktop-infrastructure-vdi-deployment-support"></a>虛擬桌面基礎結構 (VDI) 部署支援

家用原則的工作需要許多 IT 組織來解決容量、網路、安全性和治理的基本變更。 當員工在家工作時，不會受到與內部部署服務相關聯的多層式安全性原則所保護的員工。 Azure 上的虛擬桌面基礎結構 (VDI) 部署可協助組織快速回應此變更的環境。 不過，您需要一種方法來保護這些 VDI 部署的傳入/傳出網際網路存取。 您可以使用 Azure 防火牆 [DNAT 規則](rule-processing.md) 及其以 [威脅情報](threat-intel.md) 為基礎的篩選功能，來保護您的 VDI 部署。

## <a name="azure-windows-virtual-desktop-support"></a>Azure Windows 虛擬桌面支援

Windows 虛擬桌面是在 Azure 中執行的全方位桌面與應用程式虛擬化服務。 它是唯一 (VDI) 的虛擬桌面基礎結構，可提供簡化的管理、多重會話 Windows 10、適用于企業的 Microsoft 365 應用程式優化，以及遠端桌面服務 (RDS) 環境的支援。 您可以在短短幾分鐘內，在 Azure 上部署及調整您的 Windows 桌面和應用程式，並取得內建的安全性和合規性功能。 Windows 虛擬桌面不需要您開啟虛擬網路的任何輸入存取權。 不過，您必須針對在虛擬網路中執行的 Windows 虛擬桌面虛擬機器，允許一組輸出網路連線。 如需詳細資訊，請參閱[使用 Azure 防火牆來保護 Windows 虛擬桌面部署](protect-windows-virtual-desktop.md)。

## <a name="next-steps"></a>後續步驟

深入瞭解 [Windows 虛擬桌面](https://docs.microsoft.com/azure/virtual-desktop/)。