# 01 기본 GPIO 입출력
GPIO의 기본적인 디지털 입출력을 다루는 부분입니다.
자세한 내용은 [공식문서](https://www.st.com/content/ccc/resource/technical/document/user_manual/2f/71/ba/b8/75/54/47/cf/DM00105879.pdf/files/DM00105879.pdf/jcr:content/translations/en.DM00105879.pdf)의 `29 HAL GPIO Generic Driver`를 참고하시기 바랍니다.

## GPIO_InitTypeDef 구조체
GPIO 핀을 초기화하는 옵션을 담는 구조체입니다.
```cpp
uint32_t Pin; // 핀 번호. ex) GPIO_PIN_x (x = 0, 1, 2, …)
uint32_t Mode; // 입출력모드. ex) GPIO_MODE_x (x = INPUT, OUTPUT_PP, OUTPUT_OD, …)
uint32_t Pull; // 풀업, 풀다운. ex) GPIO_x (x = NOPULL, PULLUP, PULLDOWN)
uint32_t Speed; // GPIO 속도. ex) GPIO_SPEED_FREQ_x (x = LOW, MEDIUM, HIGH, VERY_HIGH)
uint32_t Alternate; // 대체기능 활성화. ex) GPIO_AFx_y (x = 0, 1, 2, … | y = …)
```

## 초기화
```cpp
void HAL_GPIO_Init(GPIO_TypeDef* GPIOx, GPIO_InitTypeDef* GPIO_Init);
```
- `GPIOx`에 GPIO 포트 입력
- `GPIO_Init` 구조체에 해당 포트 번호와 각종 설정들 입력
- STM32CubeMX에서 자동생성

## 디지털 출력
```cpp
void HAL_GPIO_WritePin(GPIO_TypeDef* GPIOx, uint16_t GPIO_Pin, GPIO_PinState PinState);
```
- `GPIOx`에 GPIO 포트 입력 (`GPIOx`, x = `A`, `B`, `C`, …)
- `GPIO_Pin`에 해당 GPIO 포트 번호 입력 (`GPIO_PIN_x`, x = `0`, `1`, `2`, …)
- `PinState`에 출력값 설정 (`GPIO_PIN_x`, x = `RESET`, `SET`)

ex)
```cpp
HAL_GPIO_WritePin(GPIOB, GPIO_PIN_7, GPIO_PIN_SET); // PB7을 HIGH 상태로
```

## 디지털 출력 토글
```cpp
void HAL_GPIO_TogglePin(GPIO_TypeDef* GPIOx, uint16_t GPIO_Pin);
```
- `GPIOx`에 GPIO 포트 입력 (`GPIOx`, x = `A`, `B`, `C`, …)
- `GPIO_Pin`에 해당 GPIO 포트 번호 입력 (`GPIO_PIN_x`, x = `0`, `1`, `2`, …)

ex) 
```cpp
HAL_GPIO_TogglePin(GPIOB, GPIO_PIN_7); // PB7의 상태를 토글
```

## 디지털 입력
```cpp
GPIO_PinState HAL_GPIO_ReadPin(GPIO_TypeDef* GPIOx, uint16_t GPIO_Pin);
```
- `GPIOx`에 GPIO 포트 입력 (`GPIOx`, x = `A`, `B`, `C`, …)
- `GPIO_Pin`에 해당 GPIO 포트 번호 입력 (`GPIO_PIN_x`, x = `0`, `1`, `2`, …)
- return: `GPIO_PIN_x` (x = `SET`, `RESET`)

ex) 
```cpp
GPIO_PinState s = HAL_GPIO_ReadPin(GPIOB, GPIO_PIN_3); // PB3을 읽어 s에 저장
```

## User Label이 있는 핀
STM32CubeMX에서 핀마다 이름을 지정해줄 수 있으며 해당 이름으로 define을 자동 생성합니다.
이름이 `x`인 경우, 
- GPIO 포트는 `x_GPIO_Port`
- 포트 번호는 `x_Pin`

ex)
```cpp
// PB7의 User Label이 'LD2'로 되어있다면
// 아래 코드는 완전히 같은 동작을 보입니다.
HAL_GPIO_WritePin(GPIOB, GPIO_PIN_7, GPIO_PIN_SET);
HAL_GPIO_WritePin(LD2_GPIO_Port, LD2_Pin, GPIO_PIN_SET);
```
