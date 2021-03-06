---
title: "여러 Azure 가상 컴퓨터에 대한 업데이트 관리 | Microsoft Docs"
description: "이 항목에서는 Azure 가상 컴퓨터에 대한 업데이트를 관리하는 방법을 설명합니다."
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/31/2017
ms.author: magoedte;eslesar
ms.openlocfilehash: 80a6caff51631637825d560d270198be0336e806
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/01/2017
---
# <a name="manage-updates-for-multiple-azure-virtual-machines"></a>여러 Azure 가상 컴퓨터에 대한 업데이트 관리

업데이트 관리를 사용하면 Azure 가상 컴퓨터에 대한 업데이트 및 패치를 관리할 수 있습니다.
[Azure Automation](automation-offering-get-started.md) 계정에서 가상 컴퓨터를 신속하게 등록하고, 사용 가능한 업데이트의 상태를 빠르게 평가하고, 필수 업데이트의 설치를 예약하고, 배포 결과를 검토하여 업데이트가 업데이트 관리가 활성화된 모든 가상 컴퓨터에 성공적으로 적용되었는지 확인할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

업데이트 관리를 사용하려면 다음이 필요합니다.

* Azure Automation 계정. Azure Automation 실행 계정을 만드는 방법에 대한 지침은 [Azure Automation 시작](automation-offering-get-started.md)을 참조하세요.

* 지원되는 운영 체제 중 하나가 설치된 가상 컴퓨터 또는 컴퓨터

## <a name="supported-operating-systems"></a>지원되는 운영 체제

업데이트 관리를 지원하는 운영 체제는 다음과 같습니다.

### <a name="windows"></a>Windows

* Windows Server 2008 이상 및 Windows Server 2008 R2 SP1 이상에 대한 업데이트 배포 -  Server Core 및 Nano 서버 설치 옵션은 지원되지 않습니다.

    > [!NOTE]
    > Windows Server 2008 R2 SP1에 업데이트를 배포하려면 .NET Framework 4.5 및 WMF 5.0 이상이 필요합니다.
    > 
* Windows 클라이언트 운영 체제는 지원되지 않습니다.

Windows 에이전트는 WSUS(Windows Server Update Services) 서버와 통신하도록 구성되거나 Microsoft Update에 대한 액세스 권한이 있어야 합니다.

> [!NOTE]
> System Center Configuration Manager에서 동시에 Windows 에이전트를 관리할 수 없습니다.
>

### <a name="linux"></a>Linux

* CentOS 6(x86/x64) 및 7(x64)  
* Red Hat Enterprise 6(x86/x64) 및 7(x64)  
* SUSE Linux Enterprise Server 11(x86/x64) 및 12(x64)  
* Ubuntu 12.04 LTS 및 최신 x86/x64   

> [!NOTE]  
> Ubuntu에서 유지 관리 기간 외에 업데이트가 적용되지 않도록 하려면 자동 업데이트를 사용하지 않도록 Unattended-Upgrade 패키지를 다시 구성합니다. 구성 방법에 대한 자세한 내용은 [Ubuntu Server 가이드의 자동 업데이트 항목](https://help.ubuntu.com/lts/serverguide/automatic-updates.html)을 참조하세요.

Linux 에이전트에는 업데이트 리포지토리에 대한 액세스 권한이 있어야 합니다.

> [!NOTE]
> 여러 OMS 작업 영역에 보고하도록 구성된 Linux용 OMS 에이전트는 이 솔루션에서 지원되지 않습니다.  
>

## <a name="enable-update-management-for-azure-virtual-machines"></a>Azure 가상 컴퓨터에 대한 업데이트 관리 사용

1. Azure Portal에서 Automation 계정을 엽니다.
2. 화면 왼쪽에서 **업데이트 관리**를 선택합니다.
3. 화면 맨 위에서 **Azure VM 추가**를 클릭합니다.
    ![VM 등록](./media/manage-update-multi/update-onboard-vm.png)
4. 등록할 가상 컴퓨터를 선택합니다. **업데이트 관리 사용** 화면이 나타납니다.
5. **사용**을 클릭합니다.

   ![업데이트 관리 사용](./media/manage-update-multi/update-enable.png)

가상 컴퓨터에 대한 업데이트 관리를 사용합니다.

## <a name="enable-update-management-for-non-azure-virtual-machines-and-computers"></a>비Azure 가상 컴퓨터 및 컴퓨터에 대한 업데이트 관리 사용

비Azure Windows 가상 컴퓨터 및 컴퓨터에 대한 업데이트 관리를 사용하도록 설정하는 방법에 대한 지침은 [Azure에서 Log Analytics 서비스에 Windows 컴퓨터 연결](../log-analytics/log-analytics-windows-agents.md)을 참조하세요.

비Azure Linux 가상 컴퓨터 및 컴퓨터에 대한 업데이트 관리를 사용하도록 설정하는 방법에 대한 지침은 [OMS(Operations Management Suite)에 Linux 컴퓨터 연결](../log-analytics/log-analytics-agent-linux.md)을 참조하세요.

## <a name="view-update-assessment"></a>업데이트 평가 보기

**업데이트 관리**를 사용하도록 설정하면 **업데이트 관리** 화면이 나타납니다. **누락된 업데이트** 탭에서 누락된 업데이트 목록을 볼 수 있습니다.

## <a name="data-collection"></a>데이터 수집

가상 컴퓨터 및 컴퓨터에 설치된 에이전트에서 업데이트에 대한 데이터를 수집하여 Azure 업데이트 관리로 보냅니다.

### <a name="supported-agents"></a>지원되는 에이전트

다음 표는 이 솔루션이 지원하는 연결된 소스를 설명합니다.

| 연결된 소스 | 지원됨 | 설명 |
| --- | --- | --- |
| Windows 에이전트 |예 |업데이트 관리에서 Windows 에이전트로부터 시스템 업데이트에 대한 정보를 수집하고 필요한 업데이트를 설치하기 시작합니다. |
| Linux 에이전트 |예 |업데이트 관리에서 Linux 에이전트로부터 시스템 업데이트에 대한 정보를 수집하고 지원되는 배포판에서 필요한 업데이트를 설치하기 시작합니다. |
| Operations Manager 관리 그룹 |예 |업데이트 관리에서 연결된 관리 그룹의 에이전트로부터 시스템 업데이트에 대한 정보를 수집합니다. |
| Azure Storage 계정 |아니요 |Azure Storage는 시스템 업데이트에 대한 정보를 포함하지 않습니다. |

### <a name="collection-frequency"></a>수집 빈도

관리되는 Windows 컴퓨터 각각의 경우 검색은 하루에 두 번 수행됩니다. 15분마다 Windows API가 호출되어 마지막 업데이트 시간을 쿼리하여 상태가 변경되었는지, 상태가 변경되었다면 준수 검사가 시작되었는지 확인합니다.  관리되는 Linux 컴퓨터 각각의 경우 검색은 세 시간마다 수행됩니다.

관리되는 컴퓨터의 업데이트 데이터가 대시보드에 표시될 때까지 30분에서 6시간이 걸릴 수 있습니다.

## <a name="schedule-an-update-deployment"></a>업데이트 배포 예약

업데이트를 설치하려면 릴리스 일정 및 서비스 기간 이후로 배포를 예약합니다.
배포에 포함할 업데이트 형식을 선택할 수 있습니다. 예를 들어 중요 업데이트나 보안 업데이트를 포함하고 업데이트 롤업은 제외할 수 있습니다.

**업데이트 관리** 화면 맨 위에서 **업데이트 배포 예약**을 클릭하여 하나 이상의 가상 컴퓨터에 대한 새 업데이트 배포를 예약합니다. **새 배포 업데이트** 화면에서 다음을 지정합니다.

* **이름** - 업데이트 배포를 식별하는 고유 이름을 제공합니다.
* **OS 유형** - Windows 또는 Linux를 선택합니다.
* **업데이트할 컴퓨터** - 업데이트하려는 가상 컴퓨터를 선택합니다.

  ![업데이트할 가상 컴퓨터 선택](./media/manage-update-multi/update-select-computers.png)

* **업데이트 분류** - 배포에 포함되는 업데이트 배포 소프트웨어의 종류를 선택합니다. 분류 형식은 다음과 같습니다.
  * 중요 업데이트
  * 보안 업데이트
  * 업데이트 롤업
  * 기능 팩
  * 서비스 팩
  * 정의 업데이트
  * 도구
  * 업데이트
* **일정 설정** - 현재 시간부터 30분 이후에 해당하는 기본 날짜 및 시간을 그대로 적용하거나 다른 시간을 지정할 수 있습니다.
   배포가 한 번만 수행될지 여부를 지정하거나 되풀이 일정을 설정할 수도 있습니다. 되풀이 일정을 설정하려면 되풀이에서 되풀이 옵션을 클릭합니다.

   ![업데이트 일정 설정 화면](./media/manage-update-multi/update-set-schedule.png)

* **유지 관리 기간(분)** - 업데이트 배포가 수행될 기간을 지정합니다.  이 기간을 지정하면 정해진 서비스 기간 내에 변경이 수행됩니다.

일정 구성을 완료한 후 **만들기** 단추를 클릭하여 상태 대시보드로 돌아갑니다.
**예약됨** 표에는 만든 배포 일정이 표시됩니다.

> [!WARNING]
> 재부팅이 필요한 업데이트의 경우 가상 컴퓨터는 자동으로 다시 시작됩니다.

## <a name="view-results-of-an-update-deployment"></a>업데이트 배포의 결과 보기

예약된 배포가 시작된 후에 **업데이트 관리** 화면의 **업데이트 배포** 탭에서 해당 배포에 대한 상태를 확인할 수 있습니다.
현재 실행 중인 경우 상태가 **진행 중**으로 표시됩니다. 성공적으로 완료되면 **성공**으로 바뀝니다.
배포에서 하나 이상의 업데이트가 실패하면 상태는 **부분적으로 실패**로 나타납니다.

![배포 상태 업데이트 ](./media/manage-update-multi/update-view-results.png)

완료된 배포 업데이트를 클릭하여 해당 업데이트 배포에 대한 대시보드를 확인합니다.

**업데이트 결과** 타일에는 가상 컴퓨터의 총 업데이트 수 및 배포 결과 요약이 표시됩니다.
오른쪽의 표에는 각 업데이트에 대한 자세한 분석과 다음 값 중 하나로 표시되는 설치 결과가 제공됩니다.

* 시도 안 함 - 정의된 유지 관리 기간에 따라 사용할 수 있는 시간이 충분하지 않기 때문에 업데이트가 설치되지 않았습니다.
* 성공 - 업데이트했습니다.
* 실패 -업데이트하지 못했습니다.

배포를 통해 생성된 항목에 대한 모든 로그를 보려면 **모든 로그**를 클릭합니다.

**출력** 타일을 클릭하여 대상 가상 컴퓨터의 업데이트 배포를 관리하는 runbook의 작업 스트림을 확인합니다.

배포의 모든 오류에 대한 자세한 정보를 보려면 **오류**를 클릭합니다.

로그, 출력 및 오류 정보에 대한 자세한 내용은 [업데이트 관리](../operations-management-suite/oms-solution-update-management.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* 업데이트 관리에 대한 자세한 내용은 [업데이트 관리](../operations-management-suite/oms-solution-update-management.md)를 참조하세요.