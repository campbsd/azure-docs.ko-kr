## <a name="create-an-iot-hub"></a>IoT Hub 만들기

1. [Azure Portal](https://portal.azure.com/)에서 **새로 만들기** > **사물 인터넷** > **IoT Hub**를 차례로 클릭합니다.

   ![Azure Portal에서 IoT Hub 만들기](../articles/iot-hub/media/iot-hub-create-hub-and-device/1_create-azure-iot-hub-portal.png)
2. **IoT Hub** 창에서 IoT Hub에 다음 정보를 입력합니다.

     **이름**: IoT Hub의 이름을 입력합니다. 입력한 이름이 올바른 경우 녹색 확인 표시가 나타납니다.

     **가격 및 크기 계층**: **F1 - 체험** 계층을 선택합니다. 이 데모의 경우 이 옵션이면 충분합니다. 자세한 내용은 [가격 및 크기 계층](https://azure.microsoft.com/pricing/details/iot-hub/)을 참조하세요.

     **리소스 그룹**: IoT Hub를 호스트할 리소스 그룹을 만들거나 기존 리소스 그룹을 사용합니다. 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../articles/azure-resource-manager/resource-group-portal.md)를 참조하세요.

     **위치**: IoT Hub가 생성된 곳에서 가장 가까운 위치를 선택합니다.

     **대시보드에 고정**: 대시보드에서 IoT Hub에 쉽게 액세스하려면 이 옵션을 선택합니다.

   ![IoT Hub를 만드는 데 필요한 정보를 입력합니다.](../articles/iot-hub/media/iot-hub-create-hub-and-device/2_fill-in-fields-for-azure-iot-hub-portal.png)

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

3. **만들기**를 클릭합니다. IoT Hub를 만드는 데 몇 분 정도 걸릴 수 있습니다. **알림** 창에서 진행률을 볼 수 있습니다.

   ![IoT Hub의 진행 알림을 참조하세요.](../articles/iot-hub/media/iot-hub-create-hub-and-device/3_notification-azure-iot-hub-creation-progress-portal.png)

4. IoT Hub를 만든 후 대시보드에서 클릭합니다. **호스트 이름**을 기록해둔 다음 **공유 액세스 정책**을 클릭합니다.

   ![IoT Hub의 호스트 이름 가져오기](../articles/iot-hub/media/iot-hub-create-hub-and-device/4_get-azure-iot-hub-hostname-portal.png)

5. **공유 액세스 정책** 창에서 **iothubowner** 정책을 클릭한 다음 IoT Hub의 **연결 문자열**을 복사하여 기록해 둡니다. 자세한 내용은 [IoT Hub에 대한 액세스 제어](../articles/iot-hub/iot-hub-devguide-security.md)를 참조하세요.

> [!NOTE] 
이 설치 자습서에는 이 iothubowner 연결 문자열이 필요 없습니다. 그러나 이 설치를 완료한 후 다른 IoT 시나리오의 일부 자습서에는 필요할 수 있습니다.

   ![IoT Hub 연결 문자열 가져오기](../articles/iot-hub/media/iot-hub-create-hub-and-device/5_get-azure-iot-hub-connection-string-portal.png)

## <a name="register-a-device-in-the-iot-hub-for-your-device"></a>장치의 IoT Hub에서 장치 등록

1. [Azure Portal](https://portal.azure.com/)에서 IoT Hub를 엽니다.

2. **장치 탐색기**를 클릭합니다.
3. 장치 탐색기 창에서 **추가**를 클릭하여 IoT Hub에 장치를 추가합니다. 그런 다음 아래 작업을 수행합니다.

   **장치 ID**: 새 장치의 ID를 입력합니다. 장치 ID는 대/소문자를 구분합니다.

   **인증 형식**: **대칭 키**를 선택합니다.

   **자동 생성 키**:이 확인란을 선택합니다.

   **IoT Hub에 장치 연결**: **사용**을 클릭합니다.

   ![IoT Hub의 Device Explorer에 장치를 추가합니다.](../articles/iot-hub/media/iot-hub-create-hub-and-device/6_add-device-in-azure-iot-hub-device-explorer-portal.png)

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

4. **Save**를 클릭합니다.
5. 장치를 만든 후에 **장치 탐색기** 창에서 장치를 엽니다.
6. 연결 문자열의 기본 키를 기록해 둡니다.

   ![장치 연결 문자열 가져오기](../articles/iot-hub/media/iot-hub-create-hub-and-device/7_get-device-connection-string-in-device-explorer-portal.png)
