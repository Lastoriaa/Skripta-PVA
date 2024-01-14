## Co je to mikrokontroler?

Mikrokontroler je malý integrovaný obvod, který kombinuje funkce procesoru (CPU), paměti a periferních zařízení v jednom čipu. Je navržen tak, aby poskytoval řízení a správu elektronických systémů, a to zejména v případech, kdy je potřeba **nízká spotřeba energie, malé rozměry a nízká cena**. 

Oproti normálnímu počítači, který je obvykle složen z několika samostatných a specializovaných komponent, z nichž každý má svou vlastní úlohu mikrokontrolery jsou jednouchým integrovanýn obvodem, který obsahuje všechny tyto klíčové komponenty v jediném čipu nebo balení. To umožňuje vytvářet systémy s menším počtem součástek.

Široká škála využití: od spotřebičů po průmyslové řízení

### Některé známější rodiny mokrokontrolerů

- Arduino
- Raspberry Pi 
- ESP8233, ESP32
- STM32

## Co to jsou RTOS?

Pod zkratrkou RTOS se zkrývá pojem **Real Time Operating System**. Jedná se o speciální typ operačního systému navrženého tak, aby poskytoval **předvídatelný** a **deterministický** průběh pro zpracování tasků. To je možné díky tomu že předem známe jak dlouho se bude daný task vykonávat.

Důležitou vlastností RTOS je že opakování stejného vstupu povede vždy ke stejnému výstupu.

Použití RTOS umožňuje psát softwarovou aplikaci jako soubor nezávislých úkolů.

Klíčové vlastnosti RTOS:
- Deterministický
- Predvidatelný
- Nízká latence

Narozdíl od GPOS (General purpose Operating systems) který musí být schopný vykonávat cokoli od nich zrovna uživatel bude chtít tak RTOS vyoknávají pouze pár tasků (ty které jim jsou zrovna přiděleny) jak nejrychleji a nejefektivněji to jde. Velikost RTOS může být pouze několik megabajtů, na rozdíl od více než 20 gigabajtů u běžných OS. RTOS má jednoduché grafické rozhraní a často chybí některé běžné funkce, jako je například webový prohlížeč.

### Kde se RTOS využívají?

V systémech, kde je kladen důraz na rychlé a spolehlivé zpracování událostí v reálném čase.

- Lékařská zařízení jako jsou srdeční stimulátory (Pacemakery)
- Obranné systémy, například radary
- Kontrolní systémy leteckého provozu a letadel
- SmartHome
- Řízení průmyslových procesů

### Rozdíl mezi soft a hard real-time systémy

**Soft real-time systémy**
- pracují v rozmezí několika set milisekund, na úrovni lidské reakce.
- například mikrokontrolery v laserových tiskárnách 
- snaha o maximální možnou spolehlivost
- nedodrží deadline -> snížení kvality

**Hard real-time systémy** 
- poskytují odezvu, která je předvídatelná v řádu desítek milisekund nebo méně.
- například technologie v letadlech
- nulová tolerance k chybám
- nedodrží deadline -> systém selže

### Známé RTOS

- FreeRTOS 
	- opensource
	- poskytuje jádro operačního systému pro správu úloh, časovače, synchronizaci a další.
	- často se používá v IoT projektech
	- škálovatelný a přenositelný na různé architektury
	- design zaměřený na malou paměťovou náročnost

- AzureRTOS/ThreadX
	- od Microsoftu
	- nabízí komplexní sadu komponent pro vývoj embedded aplikací
	- obsahuje komponenty jako ThreadX (RTOS), FileX (souborový systém), NetX (TCP/IP stack) a další
	- zaměřeno na spolehlivost a předvídatelnost
	- integrace s dalšími Azure službami pro vývoj IoT (Internet of Things) aplikací

- Mbed OS​
	- vyvíjený firmou SEGGER Microcontroller
	- zaměřen na vysokou efektivitu a nízkou latenci
	- poskytuje podporu pro multitasking, synchronizaci a správu paměti
	- optimalizován pro použití v embedded systémech s omezenými prostředky
	- nízká latence a krátký časový overhead

### Task

Každý task reprezentuje nějakou činnost nebo operaci, kterou má systém vykonat. O pořadí v kterém jsou tasky vykonávány rozhoduje **scheduler**. 
Právě běžící task může být suspendován nebo blokován čímž dá příležitost jiným taskům co jsou ready to run běžet.

![](/images/rtos-task.jpg)

### Prioritizace a scheduler

Typ operačního systému je definován jakým způsobem scheduler rozhoduje, který program kdy spustí. Scheduler v RTOS je navržen tak aby poskytl předvídatelné (deterministické) chování. To je dosaženo tak, že každému tasku je předem přidělena priorita je zodpovědností RTOS zajistit, že úkol s nejvyšší prioritou, který může běžet, je ten, který je aktuálně prováděn. Pokud je více tasků se stejnou prioritou ready to run tak je čas mezi nimi spravedlivě rozdělen a běží střídavě.

![](/images/rtos-priority.jpg)

V GPOS jsou tasky plánovány tak, aby každá získala svůj podíl na procesorovém čase bez přísného časového omezení, čímž se výrazně liší od RTOS (těm je jedno že nějaká taska dlouho neběžela protože má malou prioritu).

### Multitasking 

Jak ale docílíme toho aby nám běželo více tasků na jednom procesoru? Na vícejádrovém procesoru je to fyzicky možné, problém ale nastavá na jednodárovém. Běžný jednojádrový procesor může totiž provádět pouze jednu úlohu najednou. Pomocí rychlého přepínání mezi úlohami však může vytvořit **dojem**, že každá úloha běží souběžně. A tomu říkáme **iluze multitaskingu**.

![](rtos-multitasking.png)

### Protekce sdílených resourců

Níže jsou popsané způsoby jak chránit data aby nenastala situace kdy více tasků pracuje se stejnými daty. Například řekněme že task 1 se snaží data číst a task 2 ta stejná data přepisuje. Task 1 by pak přečetl část dat původních a část přepsaných. Nebo ještě hůř kdybychom měli task 3 který ta stejná data taky přepisuje. Výsledná data by pak nedávala smysl. 

- **Mutexy (Mutual Exclusion)** 
	- když má jeden task mutex, může provádět operace nad sdílenými daty, a žádný jiný úkol nemůže získat tento mutex do doby, než ho první úkol uvolní.
	- je to v podstatě semafor se dvěmi stavy - odemčený/uzamčený
- **Semafory (Semaphores)**
	- datům je přidělena číselná hodnota která narozdíl od mutexů nabývá vyšších hodnot než pouze nuly nebo jedničky - pokud je hodnota semaforu vetsi nez 0 tak k datum muze pristupovat dalsi task
	- je to zpusob jak vice tasku pristupuje k datum zaroven
	- byvaji pouzivany jako signalizujici mechanismus pro ostatni tasky ze sdileny resource je ready k pristupu
- **Fronty (Queues)** 
	- princip FIFO - First In, First Out


### Příklad

Následující přiklad je jednoduchý program na mikrokontroleru ESP32.

Program má dva úkoly (oba mají stejnou prioritu)
1. Vypsat "Hello World" na seriový port 
2. Rozsvítit LED světýlko

Tento diagram ukazuje pořadí v jakém budou běžet:

![](/images/diagram.png)

```c
#include <stdio.h>
#include "freertos/FreeRTOS.h"
#include "freertos/task.h"
#include "esp_system.h"
#include "driver/gpio.h"

#define BLINK_GPIO 2


void hello_task(void *pvParameter)
{
	while(1)
	{
	    printf("Hello world!\n");
	    vTaskDelay(100 / portTICK_RATE_MS);
	}
}
void blinky(void *pvParameter)
{
    gpio_pad_select_gpio(BLINK_GPIO);
    /* Set the GPIO as a push/pull output */
    gpio_set_direction(BLINK_GPIO, GPIO_MODE_OUTPUT);
    while(1) {
        /* Blink off (output low) */
        gpio_set_level(BLINK_GPIO, 0);
        vTaskDelay(1000 / portTICK_RATE_MS);
        /* Blink on (output high) */
        gpio_set_level(BLINK_GPIO, 1);
        vTaskDelay(1000 / portTICK_RATE_MS);
    }
}
void app_main()
{
    xTaskCreate(&hello_task, "hello_task", 2048, NULL, 5, NULL);
    xTaskCreate(&blinky, "blinky", 512,NULL,5,NULL );
}
`````