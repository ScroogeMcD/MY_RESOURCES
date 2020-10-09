## NON BLOCKING IO (NIO)

<details>
<summary>  Level and Edge Triggered Interrupts </summary>

reference : https://electronics.stackexchange.com/a/175885  

Let's assume that our CPU can execute code in two modes : normal mode and interrupted mode. To go from normal mode to interrupted mode, an interrupt must happen,
while to come back, the IRET instruction must be executed. Let's also assume that if an interrupt happens while the CPU is already in interrupt mode, it gets somehow saved (usually a bit is set in some register), but is not immediately serviced, i.e. when in interrupt mode, the CPU cannot be interrupted.

When the **Interrupt Service Routine (ISR)** on the **level triggered interrupt** is executed, we proably clear the interrupt bit as the first thing : if the level stays low, the hardware immediately triggers another interrupt that will be serviced right after we are finished with the current ISR.
In the **egde triggered interrupt**, we need the pin to go high and then low once again.

![level_triggered](https://user-images.githubusercontent.com/13499858/95583628-0c9ecc00-0a5a-11eb-8192-b7d86722ee0f.png)

</details>
