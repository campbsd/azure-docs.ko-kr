---
title: "원격 모니터링 솔루션에서 장치 관리 - Azure | Microsoft Docs"
description: "이 자습서에서는 원격 모니터링 솔루션에 연결된 장치를 관리하는 방법을 보여 줍니다."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 09/06/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: e0b16a30bfd3b7ed711bcb1cc955d4eccf09fac2
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/31/2017
---
# <a name="manage-and-configure-your-devices"></a>장치 관리 및 구성

이 자습서에서는 원격 모니터링 솔루션의 장치 관리 기능을 보여 줍니다. 이러한 기능을 소개하기 위해 자습서에서는 Contoso IoT 응용 프로그램에서 시나리오를 사용합니다.

Contoso는 출력 향상을 위해 해당 시설 중 하나를 확장하는 새로운 기계를 주문했습니다. 배달될 새 기계를 기다리는 동안 솔루션의 동작을 확인하는 시뮬레이션을 실행하려고 합니다. 운영자로서 원격 모니터링 솔루션에서 장치를 관리하고 구성하려고 합니다.

장치를 관리 및 구성하는 확장 가능한 방법을 제공하기 위해 원격 모니터링 솔루션은 [작업](../iot-hub/iot-hub-devguide-jobs.md) 및 [직접 메서드](../iot-hub/iot-hub-devguide-direct-methods.md)와 같은 IoT Hub 기능을 사용합니다. 장치 개발자가 물리적 장치에서 메서드를 구현하는 방법을 알아보려면 [미리 구성된 원격 모니터링 솔루션 사용자 지정](iot-suite-remote-monitoring-customize.md)을 참조하세요.

이 자습서에서는 다음 방법에 대해 알아봅니다.

>[!div class="checklist"]
> * 시뮬레이트된 장치 프로비전
> * 시뮬레이트된 장치 테스트
> * 솔루션에서 장치 메서드 호출
> * 장치 다시 구성

## <a name="prerequisites"></a>필수 조건

이 자습서를 수행하려면 Azure 구독에서 원격 모니터링 솔루션의 배포된 인스턴스가 필요합니다.

원격 모니터링 솔루션을 아직 배포하지 않은 경우 [미리 구성된 원격 모니터링 솔루션 배포](iot-suite-remote-monitoring-deploy.md) 자습서를 완료해야 합니다.

## <a name="provision-a-simulated-device"></a>시뮬레이트된 장치 프로비전

솔루션의 **장치** 페이지로 이동한 다음 **프로비전**을 선택합니다. **프로비전** 패널에서 **시뮬레이트함**을 선택합니다.

![시뮬레이트된 장치 프로비전](media/iot-suite-remote-monitoring-manage/devicesprovision.png)

**1**로 설정된 프로비전하는 장치의 수를 그대로 둡니다. **장치 모델**로 **엔진**을 선택한 다음 **적용**을 선택하여 시뮬레이트된 장치를 만듭니다.

![시뮬레이트된 엔진 장치 프로비전](media/iot-suite-remote-monitoring-manage/devicesprovisionengine.png)

*물리적* 장치를 프로비전하는 방법을 알아보려면 [미리 구성된 원격 모니터링 솔루션에 장치 연결](iot-suite-connecting-devices-node.md)을 참조하세요.

## <a name="test-the-simulated-device"></a>시뮬레이트된 장치 테스트

시뮬레이트된 새 장치의 세부 정보를 보려면 **장치** 페이지의 장치 목록에서 선택합니다. 장치에 대한 정보가 **장치 세부 정보** 패널에 표시됩니다.

![시뮬레이트된 새 엔진 장치 보기](media/iot-suite-remote-monitoring-manage/devicesviewnew.png)

**장치 세부 정보**에서 새 장치가 원격 분석에 전송되는지 확인합니다. 장치에서 여러 원격 분석 스트림을 보려면 **진동**과 같은 원격 분석 이름을 선택합니다.

![보려는 원격 분석 스트림 선택](media/iot-suite-remote-monitoring-manage/devicesvibration.png)

**장치 세부 정보** 패널은 태그 값, 지원하는 메서드 및 장치에서 보고되는 속성과 같은 장치에 대한 기타 정보를 표시합니다.

자세한 진단을 보려면 아래로 스크롤하여 **진단**을 봅니다.

## <a name="act-on-a-device"></a>장치에서 작동

장치에서 작동하려면 장치 목록에서 선택한 다음 **일정**을 선택합니다. **엔진** 장치 모델은 장치에서 지원해야 하는 4개의 메서드를 지정합니다.

![엔진 메서드](media/iot-suite-remote-monitoring-manage/devicesmethods.png)

**다시 시작**을 선택하고, 작업 이름을 **RestartEngine**으로 설정한 다음 **적용**을 선택합니다.

![다시 시작 메서드 예약](media/iot-suite-remote-monitoring-manage/devicesrestartengine.png)

**유지 관리** 페이지에서 작업의 상태를 추적하려면 **시스템 상태**를 선택합니다.

![예약된 작업 모니터링](media/iot-suite-remote-monitoring-manage/maintenancerestart.png)

### <a name="methods-in-other-devices"></a>다른 장치의 메서드

서로 다른 시뮬레이트된 장치 유형을 탐색하는 경우 다른 장치 유형이 다른 메서드를 지원하는 것을 확인할 수 있습니다. 물리적 장치로 배포에서 장치 모델은 장치에서 지원해야 하는 메서드를 지정합니다. 일반적으로 장치 개발자는 장치가 메서드 호출에 대한 응답으로 작동하도록 하는 코드 개발을 담당합니다.

메서드를 여러 장치에서 실행되도록 예약하기 위해 **장치** 페이지의 목록에서 여러 장치를 선택할 수 있습니다. **일정** 패널은 선택한 모든 장치에 공통적인 메서드의 유형을 표시합니다.

## <a name="reconfigure-a-device"></a>장치 다시 구성

장치의 구성을 변경하려면 **장치** 페이지의 장치 목록에서 선택한 다음 **다시 구성**을 선택합니다. 다시 구성 패널은 변경할 수 있는 선택한 장치에 대한 속성 값을 보여 줍니다.

![장치 다시 구성](media/iot-suite-remote-monitoring-manage/devicesreconfigure.png)

변경하려면 작업에 대한 이름을 추가하고, 속성 값을 업데이트하고, **적용**을 선택합니다.

![장치 속성 값 업데이트](media/iot-suite-remote-monitoring-manage/devicesreconfigurephysical.png)

**유지 관리** 페이지에서 작업의 상태를 추적하려면 **시스템 상태**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서는 다음 방법을 보여 줬습니다.

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * 시뮬레이트된 장치 프로비전
> * 시뮬레이트된 장치 테스트
> * 솔루션에서 장치 메서드 호출
> * 장치 다시 구성

이제 장치를 관리하는 방법을 배웠으므로 제안된 다음 단계는 다음 방법을 배우기 위한 것입니다.

* [장치 문제 해결 및 수정](iot-suite-remote-monitoring-maintain.md)
* [시뮬레이트된 장치로 솔루션 테스트](iot-suite-remote-monitoring-test.md)
* [미리 구성된 원격 모니터링 솔루션에 장치 연결](iot-suite-connecting-devices-node.md)

<!-- Next tutorials in the sequence -->