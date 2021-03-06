---
title: "Log Analytics 로그 검색의 컴퓨터 그룹 | Microsoft Docs"
description: "Log Analytics의 컴퓨터 그룹을 사용하여 로그 검색 범위를 특정 컴퓨터 집합으로 한정할 수 있습니다.  이 문서에서는 컴퓨터 그룹을 만드는 데 사용할 수 있는 몇 가지 방법과 로그 검색에서의 사용 방법을 설명합니다."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: 
ms.assetid: a28b9e8a-6761-4ead-aa61-c8451ca90125
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: bwren
ms.openlocfilehash: f27f038e0507270c0bfe200cb8c86622ebac5372
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2017
---
# <a name="computer-groups-in-log-analytics-log-searches"></a>Log Analytics 로그 검색의 컴퓨터 그룹 

Log Analytics의 컴퓨터 그룹을 사용하여 [로그 검색](log-analytics-log-search-new.md) 범위를 특정 컴퓨터 집합으로 한정할 수 있습니다.  각 그룹에는 사용자가 정의를 사용하거나 여러 원본에서 그룹을 가져와 컴퓨터가 채워집니다.  그룹이 로그 검색에 포함된 경우 결과는 그룹의 컴퓨터에 일치하는 레코드로 한정됩니다.

## <a name="creating-a-computer-group"></a>컴퓨터 그룹 만들기
Log Analytics에서 다음 표의 방법 중 하나를 통해 컴퓨터 그룹을 만들 수 있습니다.  각 방법에 대한 자세한 내용은 아래 섹션에서 설명합니다. 

| 메서드 | 설명 |
|:--- |:--- |
| 로그 검색 |컴퓨터 목록을 반환하는 로그 검색 만듭니다. |
| 로그 검색 API |로그 검색 API를 사용하여 프로그래밍 방식으로 로그 검색 결과에 따라 컴퓨터 그룹을 만듭니다. |
| Active Directory |Active Directory 도메인의 구성원인 에이전트 컴퓨터의 그룹 구성원을 자동으로 검색하고 각 보안 그룹에 대해 Log Analytics에 그룹을 만듭니다. |
| 구성 관리자 | System Center Configuration Manager에서 컬렉션을 가져오고 Log Analytics에서 각 컬렉션에 대한 그룹을 만듭니다. |
| Windows Server 업데이트 서비스 |대상 그룹에 대해 자동으로 WSUS 서버나 클라이언트를 검색하고 각각에 대해 Log Analytics에 그룹을 만듭니다. |

### <a name="log-search"></a>로그 검색
Log Search에서 생성된 컴퓨터 그룹은 사용자가 정의한 검색 쿼리가 반환한 모든 컴퓨터를 포함합니다.  이 쿼리는 컴퓨터 그룹이 사용될 때마다 실행되므로 그룹이 만들어진 이후의 모든 변경 내용이 반영됩니다.  

컴퓨터 그룹에 대해 어떤 쿼리라도 사용할 수 있지만, `distinct Computer`을 사용하여 별개의 컴퓨터 집합을 반환해야 합니다.  다음은 컴퓨터 그룹으로 사용할 수 있는 일반적인 검색 예입니다.

    Heartbeat | where Computer contains "srv" | distinct Computer

다음 표는 컴퓨터 그룹을 정의하는 속성을 설명합니다.

| 속성 | 설명 |
|:---|:---|
| 표시 이름   | 포털에 표시할 검색의 이름입니다. |
| Category       | 포털에서 검색을 구성하는 범주입니다. |
| 쿼리          | 컴퓨터 그룹에 대한 쿼리입니다. |
| 함수 별칭 | 쿼리에서 컴퓨터 그룹을 식별하는 데 사용되는 고유한 별칭입니다. |

다음 프로시저를 사용하여 Azure Portal의 로그 검색에서 컴퓨터 그룹을 만듭니다.

2. **Log Search**을 시작한 후 화면 맨 위에 있는 **저장된 검색**을 클릭합니다.
3. **추가**를 클릭하고 컴퓨터 그룹의 각 속성에 대한 값을 제공합니다.
4. **이 쿼리를 컴퓨터 그룹으로 저장**을 선택하고 **확인**을 클릭합니다.


다음 프로시저를 사용하여 OMS Portal의 로그 검색에서 컴퓨터 그룹을 만듭니다.

1. **Log Search**을 시작하여 컴퓨터 그룹에 대한 로그 검색을 만듭니다.  
2. 화면 위쪽에 있는 **저장** 단추를 클릭합니다.
3. **예**를 선택하여 **이 쿼리를 컴퓨터 그룹으로 저장**합니다.
5. 컴퓨터 그룹의 각 속성에 대한 값을 제공합니다. 


>[!NOTE]
> 작업 영역에서 [레거시 Log Analytics 쿼리 언어](log-analytics-log-search-upgrade.md)를 여전히 사용한다면 컴퓨터 그룹을 만들기 위해 동일한 프로시저를 사용하지만, 레거시 쿼리 언어 구문을 사용해야 합니다.


### <a name="log-search-api"></a>로그 검색
로그 검색 API를 사용하여 만들어진 컴퓨터 그룹은 로그 검색으로 만든 검색과 동일합니다.  로그 검색 API를 사용하여 컴퓨터 그룹을 만드는 것에 대한 자세한 내용은 [Log Analytics 로그 검색 REST API의 컴퓨터 그룹](log-analytics-log-search-api.md#computer-groups)을 참조하세요.

### <a name="active-directory"></a>Active Directory
Active Directory 그룹 멤버 자격을 가져오도록 Log Analytics를 구성하면 OMS 에이전트가 있는 도메인 연결 컴퓨터의 그룹 멤버 자격을 분석합니다.  컴퓨터 그룹은 Log Analytics에서 Active Directory의 각 보안 그룹에 대해 만들어지며 각 컴퓨터는 자신이 속산 보안 그룹에 해당하는 컴퓨터 그룹에 추가됩니다.  이 멤버 자격은 4시간 간격으로 계속 업데이트됩니다.  

Azure Portal의 Log Analytics **고급 설정**에서 Active Directory 보안 그룹을 가져오도록 Log Analytics를 구성합니다.  **컴퓨터 그룹**, **Active Directory**, 및 **컴퓨터에서 Active Directory 그룹 멤버 자격을 가져오기**를 차례로 선택합니다.  추가 구성은 필요 없습니다.

![Active Directory의 컴퓨터 그룹](media/log-analytics-computer-groups/configure-activedirectory.png)

그룹을 가져올 때는 검색된 그룹 멤버 자격 및 가져온 그룹 수와 함께 컴퓨터 수가 메뉴에 나열됩니다.  이 링크 중 하나를 클릭하여 **ComputerGroup** 레코드와 이 정보를 반환할 수 있습니다.

### <a name="windows-server-update-service"></a>Windows Server 업데이트 서비스
WSUS 그룹 멤버 자격을 가져오도록 Log Analytics를 구성하면 OMS 에이전트가 있는 컴퓨터의 대상 그룹 멤버 자격을 분석합니다.  클라이언트 쪽 대상을 사용하는 경우 OMS에 연결되고 WSUS 대상 그룹에 속한 모든 컴퓨터의 그룹 멤버 자격을 Log Analytics로 가져옵니다. 서버 쪽을 대상으로 사용하는 경우 그룹 멤버 자격 정보를 OMS로 가져오도록 OMS 에이전트를 WSUS 서버에 설치해야 합니다.  이 멤버 자격은 4시간 간격으로 계속 업데이트됩니다. 

Azure Portal의 Log Analytics **고급 설정**에서 WSUS 그룹을 가져오도록 Log Analytics를 구성합니다.  **컴퓨터 그룹**, **WSUS**, 및 **WSUS 그룹 멤버 자격을 가져오기**를 차례로 선택합니다.  추가 구성은 필요 없습니다.

![WSUS의 컴퓨터 그룹](media/log-analytics-computer-groups/configure-wsus.png)

그룹을 가져올 때는 검색된 그룹 멤버 자격 및 가져온 그룹 수와 함께 컴퓨터 수가 메뉴에 나열됩니다.  이 링크 중 하나를 클릭하여 **ComputerGroup** 레코드와 이 정보를 반환할 수 있습니다.

### <a name="system-center-configuration-manager"></a>System Center Configuration Manager
Configuration Manager 컬렉션 멤버 자격 가져오도록 Log Analytics를 구성하면 각 컬렉션에 대한 컴퓨터 그룹을 만듭니다.  컬렉션 멤버 자격 정보는 컴퓨터 그룹을 최신 상태로 유지하기 위해 3시간 마다 검색됩니다. 

Configuration Manager 컬렉션을 가져올 수 있으려면 먼저 [Configuration Manager를 Log Analytics에 연결](log-analytics-sccm.md)해야 합니다.  그런 후 Azure Portal의 Log Analytics **고급 설정**에서 가져오기를 구성할 수 있습니다.  **컴퓨터 그룹**, **SCCM**, 및 **구성 관리자 컬렉션 멤버 자격 가져오기**를 차례로 선택합니다.  추가 구성은 필요 없습니다.

![SCCM의 컴퓨터 그룹](media/log-analytics-computer-groups/configure-sccm.png)

컬렉션이 들여오기 될 때 검색된 그룹 멤버 자격 및 가져온 그룹 수와 함께 컴퓨터 수가 메뉴에 나열됩니다.  이 링크 중 하나를 클릭하여 **ComputerGroup** 레코드와 이 정보를 반환할 수 있습니다.

## <a name="managing-computer-groups"></a>컴퓨터 그룹 관리
Azure Portal의 Log Analytics **고급 설정**에서 로그 검색 또는 Log Search API에서 생성된 컴퓨터 그룹을 볼 수 있습니다.  **컴퓨터 그룹**을 선택한 후 **저장된 그룹**을 선택합니다.  

**제거** 열에서 **x**를 클릭하여 컴퓨터 그룹을 삭제합니다.  그룹에 대한 **멤버 보기** 아이콘을 클릭하여 멤버를 반환하는 그룹의 로그 검색을 실행합니다.  컴퓨터 그룹을 수정할 수는 없지만 대신 삭제한 다음 수정된 설정으로 다시 만들어야 합니다.

![저장된 컴퓨터 그룹](media/log-analytics-computer-groups/configure-saved.png)


## <a name="using-a-computer-group-in-a-log-search"></a>로그 검색에서 컴퓨터 그룹 사용
일반적으로 다음 구문을 사용하여 그 별칭을 함수로 취급함으로써 컴퓨터 그룹을 쿼리에 사용합니다.

  `Table | where Computer in (ComputerGroup)`

예를 들어 mycomputergroup이라는 컴퓨터 그룹에서 컴퓨터에 대해서만 UpdateSummary 레코드를 반환하기 위해 다음을 사용할 수 있습니다.
 
  `UpdateSummary | where Computer in (mycomputergroup)`

>[!NOTE]
> 작업 영역에서 여전히 [레거시 Log Analytics 쿼리 언어](log-analytics-log-search-upgrade.md)를 사용한다면, 다음 구문을 사용하여 로그 검색에 컴퓨터 그룹을 참조합니다.  **범주** 지정은 선택 사항이며 다른 카테고리에 이름이 같은 컴퓨터 그룹이 있는 경우에만 필수입니다. 
>
>    `$ComputerGroups[Category: Name]`
>
>일반적으로 컴퓨터 그룹은 다음 예제에서처럼 로그 검색에서 **IN** 절에서 사용됩니다.
>
>    `Type=UpdateSummary Computer IN $ComputerGroups[My Computer Group]`



## <a name="computer-group-records"></a>컴퓨터 그룹 레코드
Active Directory 또는 WSUS로 만든 각각의 컴퓨터 그룹 멤버 자격에 대해 OMS 저장소에 레코드가 만들어집니다.  이 레코드의 형식은 **ComputerGroup**이며 다음 표의 속성을 갖습니다.  로그 검색 기반의 컴퓨터 그룹에 대해서는 레코드가 만들어지지 않습니다.

| 속성 | 설명 |
|:--- |:--- |
| 형식 |*ComputerGroup* |
| SourceSystem |*SourceSystem* |
| 컴퓨터 |멤버 컴퓨터의 이름입니다. |
| 그룹 |그룹의 이름입니다. |
| GroupFullName |원본 및 원본 이름을 포함하는 그룹에 대한 전체 경로입니다. |
| GroupSource |그룹을 수집해 온 원본입니다. <br><br>ActiveDirectory<br>WSUS<br>WSUSClientTargeting |
| GroupSourceName |그룹을 수집해 온 원본의 이름입니다.  Active Directory의 경우 도메인 이름이 됩니다. |
| ManagementGroupName |SCOM 에이전트의 경우 관리 그룹의 이름.  다른 에이전트의 경우 AOI-\<작업 영역 ID\>입니다. |
| TimeGenerated |컴퓨터 그룹이 만들어졌거나 업데이트된 날짜 및 시간입니다. |

## <a name="next-steps"></a>다음 단계
* 데이터 원본 및 솔루션에서 수집한 데이터를 분석하기 위해 [로그 검색](log-analytics-log-searches.md) 에 대해 알아봅니다.  

