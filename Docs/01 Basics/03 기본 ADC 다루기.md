# 03 기본 ADC 다루기
ADC에 관련된 문서입니다.
모든 자세한 내용은 [공식문서](https://www.st.com/content/ccc/resource/technical/document/user_manual/2f/71/ba/b8/75/54/47/cf/DM00105879.pdf/files/DM00105879.pdf/jcr:content/translations/en.DM00105879.pdf)의 `6 HAL ADC Generic Driver`를 참고하시기 바랍니다.

## ADC_InitTypeDef 구조체
ADC 하드웨어와 관련된 설정을 담는 구조체입니다.
```cpp
uint32_t ClockPrescaler; // clock 분할. ADC_CLOCK_SYNC_PCLK_DIVx (x = 2, 4, 6, 8)
uint32_t Resolution; // 해상도. ADC_RESOLUTION_xB (x = 6, 8, 10, 12)
uint32_t DataAlign; // 비트 정렬. ADC_DATAALIGN_x (x = RIGHT, LEFT)
uint32_t ScanConvMode;
uint32_t EOCSelection;
uint32_t ContinuousConvMode;
uint32_t NbrOfConversion;
uint32_t DiscontinuousConvMode;
uint32_t NbrOfDiscConversion;
uint32_t ExternalTrigConv;
uint32_t ExternalTrigConvEdge;
uint32_t DMAContinuousRequests;
```

## ADC 초기화
HAL에는 ADC 작동 모드에 따른 여러 가지 초기화 함수가 있으며, STM32CubeMX에서 자동생성됩니다.  
일반적인 경우, ADC 작동 모드를 바꾸는 일은 없기 때문에 자세한 설명은 생략하겠습니다.

```cpp
HAL_StatusTypeDef HAL_ADC_Init(ADC_HandleTypeDef* hadc);
HAL_StatusTypeDef HAL_ADC_MspInit(ADC_HandleTypeDef* hadc);
```

초기화를 해제하는 함수도 있습니다.

```cpp
HAL_StatusTypeDef HAL_ADC_DeInit(ADC_HandleTypeDef* hadc);
HAL_StatusTypeDef HAL_ADC_MspDeInit(ADC_HandleTypeDef* hadc);
```

## ADC 동작모드
STM32에서 ADC 하드웨어는 세 가지 동작모드가 있습니다.
- Polling Mode
- Interrupt Mode
- DMA Mode

동작모드에 따라 함수가 다르게 정의되어 있습니다.  
각각의 경우에 대해 내용을 작성하겠습니다.

## Polling Mode
Polling Mode의 경우, ADC Conversion을 하는 동안 코드가 block 됩니다.  
사용하는 함수는 다음과 같으며 순서대로 사용해야 합니다.
```cpp
HAL_ADC_Start();
HAL_ADC_PollForConversion();
HAL_ADC_GetValue();
HAL_ADC_Stop();
```

### 샘플링 및 변환 시작
ADC의 전압을 읽는 것을 시작하는 함수입니다.  
```cpp
HAL_StatusTypeDef HAL_ADC_Start(ADC_HandleTypeDef* hadc);
```
- `hadc`에 ADC 설정 정보를 담은 `ADC_HandleTypeDef` 구조체 포인터를 입력합니다. STM32CubeMX에서 hadcx 이름으로 자동 생성됩니다. (x = 0, 1, ...);


### 변환이 끝날 때까지 기다리기
ADC가 전압을 읽어 디지털로 변환하기까지 시간이 걸립니다.  
이 함수를 이용하여 변환이 끝날 때까지 대기합니다. (block)
```cpp
HAL_StatusTypeDef HAL_ADC_PollForConversion(ADC_HandleTypeDef* hadc, uint32_t Timeout);
```
- `hadc`에 ADC 설정 정보를 담은 `ADC_HandleTypeDef` 구조체 포인터를 입력합니다. STM32CubeMX에서 hadcx 이름으로 자동 생성됩니다. (x = 0, 1, ...);
- `Timeout`에 타임아웃 시간을 설정합니다. 제한을 두지 않을 경우, `HAL_MAX_DELAY`를 입력합니다.

### 디지털 데이터 저장하기
변환이 완료되면 디지털 값으로 저장됩니다.
이 함수를 사용하여 값을 얻어올 수 있습니다.
```cpp
uint32_t HAL_ADC_GetValue(ADC_HandleTypeDef* hadc);
```
- `hadc`에 ADC 설정 정보를 담은 `ADC_HandleTypeDef` 구조체 포인터를 입력합니다. STM32CubeMX에서 hadcx 이름으로 자동 생성됩니다. (x = 0, 1, ...);
- return: 변환된 값

### ADC 멈추기
측정이 끝나면 이 함수로 ADC 변환작업을 끝내고 멈추게 합니다.
```cpp
HAL_StatusTypeDef HAL_ADC_Stop(ADC_HandleTypeDef* hadc);
```
- `hadc`에 ADC 설정 정보를 담은 `ADC_HandleTypeDef` 구조체 포인터를 입력합니다. STM32CubeMX에서 hadcx 이름으로 자동 생성됩니다. (x = 0, 1, ...);