HW04
===
This is the hw04 sample. Please follow the steps below.

# Build the Sample Program

1. Fork this repo to your own github account.

2. Clone the repo that you just forked.

3. Under the hw04 dir, use:

	* `make` to build.

	* `make flash` to flash the binary file into your STM32F4 device.

	* `make clean` to clean the ouput files.

# Build Your Own Program

1. Edit or add files if you need to.

2. Make and run like the steps above.

3. Please avoid using hardware dependent C standard library functions like `printf`, `malloc`, etc.

# HW04 Requirements

1. Please practice to reference the user manuals mentioned in [Lecture 04], and try to use the user button (the blue one on the STM32F4DISCOVERY board).

2. After reset, the device starts to wait until the user button has been pressed, and then starts to blink the blue led forever.

3. Try to define a macro function `READ_BIT(addr, bit)` in reg.h for reading the value of the user button.

4. Push your repo to your github. (Use .gitignore to exclude the output files like object files, executable files, or binary files)

5. The TAs will clone your repo, build from your source code, and flash to see the result.

[Lecture 04]: http://www.nc.es.ncku.edu.tw/course/embedded/04/

--------------------

- [x] **If you volunteer to give the presentation (demo) next week, check this.**

--------------------

LAB4
===
## 本周作業
開機後無窮等待，等按鈕按下後，藍色 LED 燈不斷閃爍。
### 實驗過程:

#### Step1:
在上課中我們已經完成LED的設置，以下只針對BUTTON的設置做說明:
由[Discovery kit with STM32F407VG MCU](http://www.nc.es.ncku.edu.tw/course/embedded/pdf/STM32F4DISCOVERY.pdf)目錄中的 **6-4 Push buttons**(p.16)裡面有說明**User button** 為 PA0。
![](https://i.imgur.com/yMJkN0O.png)


#### Step2:
在使用 GPIO 前，必須啟用 RCC (Reset and clock control) 中對應的 bit。
* GPIOA為AHB1匯流排
![](https://i.imgur.com/mL6X3cj.png)


#### Step3:
設定 GPIOA 運作的方式，依序設置 MODER、OTYPER、OSPEEDR、PUPDR 四個 register 。

![](https://i.imgur.com/CsbYdu0.png)

* MODER(0) = 00 

* OTYPER(0) = default

* OSPEEDER(0) = default

* PUPDR(0) = 00

#### Step4:
利用GPIOx_IDR 讀取BUTTON(PA0)的狀態。
![](https://i.imgur.com/ojWqegq.png)

#### 程式碼:
```C
#include <stdint.h>
#include "blink.h"
#include "reg.h"

int main(void)
{   
	SET_BIT(RCC_BASE + RCC_AHB1ENR_OFFSET, GPIO_EN_BIT(GPIO_PORTA));

	//MODER0 = 00 => General purpose input mode
        CLEAR_BIT(GPIO_BASE(GPIO_PORTA) + GPIOx_MODER_OFFSET, MODERy_1_BIT(GPIO_PORTA));
	CLEAR_BIT(GPIO_BASE(GPIO_PORTA) + GPIOx_MODER_OFFSET, MODERy_0_BIT(GPIO_PORTA));

	//PUPDR0 = 00 => No pull-up, pull-down
        CLEAR_BIT(GPIO_BASE(GPIO_PORTA) + GPIOx_PUPDR_OFFSET, PUPDRy_1_BIT(GPIO_PORTA));
	CLEAR_BIT(GPIO_BASE(GPIO_PORTA) + GPIOx_PUPDR_OFFSET, PUPDRy_0_BIT(GPIO_PORTA));
	
        //無窮等待直到按鈕按下
	while(!READ_BIT(GPIO_BASE(GPIO_PORTA) + GPIOx_IDR_OFFSET, IDR_0_BIT(GPIO_PORTA)));
	blink(LED_BLUE);

}
```

Hint:

![](https://i.imgur.com/7cYmw2C.png)
查看Discovery kit with STM32F407VG MCU
得知user button為上拉電阻。
