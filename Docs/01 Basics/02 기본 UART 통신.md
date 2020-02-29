# 02 기본 UART 통신
UART 시리얼 통신에 관련된 문서입니다.
자세한 내용은 [공식문서](https://www.st.com/content/ccc/resource/technical/document/user_manual/2f/71/ba/b8/75/54/47/cf/DM00105879.pdf/files/DM00105879.pdf/jcr:content/translations/en.DM00105879.pdf)의 `67 HAL UART Generic Driver`를 참고하시기 바랍니다.

STM32는 AVR 아두이노에 비해 메모리가 훨씬 넉넉하기 때문에 데이터를 전송하거나 수신할 때 필요한 버퍼를 넉넉히 지정해도 됩니다.
아두이노의 Serial 클래스와 사용방법이 다른 점이 많기 때문에 레퍼런스 내용을 최대한 담도록 하겠습니다.

## UART_InitTypeDef 구조체
UART 통신과 관련된 설정을 담는 구조체입니다.
```cpp
    uint32_t BaudRate // 통신속도. 
    uint32_t WordLength // 통신시 WORD 길이. UART_WORDLENGTH_xB (x = 8, 9)
    uint32_t StopBits // 스탑비트 갯수. UART_SOTPBITS_x (x = 1, 2)
    uint32_t Parity // 패리티비트 설정. UART_PARITY_x (x = NONE, EVEN, ODD)
    uint32_t Mode // 송수신 모드 설정. UART_MODE_x (x = RX, TX, RX_TX)
    uint32_t HwFlowCtl // 흐름제어 설정. UART_HWCONTROL_x (x = NONE, RTS, CTS, RTS_CTS)
    uint32_t OverSampling // 오버샘플링 설정. UART_OVERSAMPLING_x (x = 8, 16)
```

## UART 초기화
HAL에는 UART의 작동 모드에 따른 여러 가지 초기화 함수가 있으며, STM32CubeMX에서 자동생성됩니다.
일반적인 경우, UART 작동 모드를 바꾸는 일은 없기 때문에 자세한 설명은 생략하겠습니다.

```cpp
HAL_StatusTypeDef HAL_UART_Init(UART_HandleTypeDef* huart)
HAL_StatusTypeDef HAL_HalfDuplex_Init(UAT_HandleTypeDef* huart)
HAL_StatusTypeDef HAL_LIN_Init(UART_HandleTypeDef* huart, uint32_t BreakDetectLength)
HAL_StatusTypeDef HAL_MultiProcessor_Init(UART_HandleTypeDef* huart, uint8_t Address, uint32_t WakeUpMethod)
HAL_StatusTypeDef HAL_UART_MspInit(UART_HandleTypeDef* huart)
```

초기화를 해제하는 함수도 있습니다.

```cpp
HAL_StatusTypeDef HAL_UART_Deinit(UART_HandleTypeDef* huart)
HAL_StatusTypeDef HAL_UART_MspDeInit(UART_HandleTypeDef* huart)
```

## UART 동작모드
STM32에서 UART 하드웨어는 세 가지 동작모드가 있습니다.
- Polling Mode
- Interrupt Mode
- DMA Mode

동작모드에 따라 함수가 다르게 정의되어 있습니다.
각각의 경우에 대해 내용을 작성하겠습니다.

## Polling Mode
Polling Mode의 경우, UART를 통해 송수신하는동안 프로그램이 block 됩니다.

### HAL_UART_Transmit
UART를 통해 데이터를 전송합니다. 아두이노의 `Serial.writeBytes()` 함수와 비슷합니다.
전송하는동안 코드가 block 됩니다.
```cpp
HAL_StatusTypeDef HAL_UART_Transmit(UART_HandleTypeDef* huart, uint8_t* pData, uint16_t Size, uint32_t Timeout)
```
- `huart`에  UART 설정 정보를 담은 UART_HandleTypeDef 구조체 포인터를 입력합니다. STM32CubeMX에서 huartx이름으로 자동생성됩니다. (x = 0, 1, 2, ...)
- `pData`에 전송할 데이터 버퍼의 포인터를 입력합니다.
- `Size`에 전송할 데이터의 크기를 입력합니다.
- `Timeout`에 타임아웃 시간을 설정합니다. 제한을 두지 않을 경우, `HAL_MAX_DELAY`로 지정합니다.

### HAL_UART_Recieve
UART를 통해 데이터를 수신합니다. 아두이노의 `Serial.readBytes()` 함수와 비슷합니다.
수신하는동안 코드가 block 됩니다.
```cpp
HAL_StatusTypeDef HAL_UART_Receive(UART_HandleTypeDef* huart, uint8_t* pData, uint16_t Size, uint32_t Timeout)
```
- `huart`에  UART 설정 정보를 담은 UART_HandleTypeDef 구조체 포인터를 입력합니다. STM32CubeMX에서 huartx이름으로 자동생성됩니다. (x = 0, 1, 2, ...)
- `pData`에 수신할 데이터 버퍼의 포인터를 입력합니다.
- `Size`에 수신할 데이터의 크기를 입력합니다.
- `Timeout`에 타임아웃 시간을 설정합니다. 제한을 두지 않을 경우, `HAL_MAX_DELAY`로 지정합니다.

ex)
```cpp
char buf[10];
HAL_UART_Receive(&huart3, buf, strlen(buf), HAL_MAX_DELAY);
```


## Interrupt Mode
Interrupt Mode에서는 UART를 통해 송수신하는동안 프로그램이 block 되지 않습니다. (non-blocking)

## DMA Mode
DMA Mode에서는 CPU가 아닌 DMA가 UART를 통해 송수신합니다. 프로그램이 block 되지 않습니다. (non-blocking)