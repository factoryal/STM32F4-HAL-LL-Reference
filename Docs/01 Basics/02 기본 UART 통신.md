# 02 기본 UART 통신
UART 시리얼 통신에 관련된 문서입니다.
자세한 내용은 [공식문서](https://www.st.com/content/ccc/resource/technical/document/user_manual/2f/71/ba/b8/75/54/47/cf/DM00105879.pdf/files/DM00105879.pdf/jcr:content/translations/en.DM00105879.pdf)의 `67 HAL UART Generic Driver`를 참고하시기 바랍니다.

STM32는 AVR 아두이노에 비해 메모리가 훨씬 넉넉하기 때문에 데이터를 전송하거나 수신할 때 필요한 버퍼를 넉넉히 지정해도 됩니다.

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

## UART 동작모드
STM32에서 UART 하드웨어는 세 가지 동작모드가 있습니다.
- Polling Mode
- Interrupt Mode
- DMA Mode

동작모드에 따라 함수가 다르게 정의되어 있습니다.
각각의 경우에 대해 내용을 작성하겠습니다.

## Polling Mode

## Interrupt Mode

## DMA Mode