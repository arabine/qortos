# QoRTOS

Quite Ok RTOS for microcontrollers.

- tickless
- minimal (500 lines of code)
- *No* mutexes and semaphores
- only static memory mailboxes

## Project goal

The aim of this project is to provide a very simple and basic RTOS that is easy to port and understand, especially for educational purpose. It was created to test software design of tickless RTOS and avoid typicall problems with mutexes and mis-understanding of semaphores.

A second goal is to be minimal, in the future maybe only one file to drop in your project.


## Project status / limitations

- First working version on Raspberry Pico
- No documentation
- Not heavily tested
- Feedback are welcome!
- Only GCC

## Compatible platforms

Only GCC is supported for now.

| CPU  | Status |
-----------------
| RP2040 (Raspberry Pico)  |   working  |

## TODO

- Move stacks out of qor.c
- Documentation (only on this README.txt)
- Design documentation (in a separated Markdown file in docs)
- Examples in `examples` directory
- Port on a RISC-V microcontroller

# Documentation

## Typical usage

```C
qor_tcb_t BlinkyTcb;
qor_tcb_t IdleTcb;

void BlinkyTask(void *args)
{
    while (1)
    {
        ost_hal_gpio_set(OST_GPIO_DEBUG_LED, 0);
        qor_sleep(500);
        ost_hal_gpio_set(OST_GPIO_DEBUG_LED, 1);
        qor_sleep(500);
    }
}

void IdleTaskFunction(void *args)
{
    while (1)
    {
        // Use the idle thread for instrumentation or power saving
	// Beware, OS functions won't work here
        // __asm volatile("wfi");
    }
}

int main()
{
    qor_init(THREADFREQ);
    qor_create_thread(&BlinkyTcb, BlinkTask, 3, "BlinkyTask");
    qor_start(&IdleTcb, IdleTaskFunction);

    return 0;
}
```

## How to use it in your project

1. Add qor.c and qor_armv6m.s files in your project build system
2. Initialize the RTOS, create threads and then start it

## Porting guide

For the moment, the RTOS is limited to the RP2040 MCU (Raspberry Pico), especially for the OS Timer used to wakeup the processor for task switching. It requires a 64-bit timer at us unit as a base time.
Porting to a 32-bit timer should be quite easy. Some macros should be used to make things agnostic.



