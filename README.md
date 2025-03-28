# Actividad de seguimiento - Simulación 1

|Integrante|correo|usuario github|
|---|---|---|
|Jonathan David Fernandez Vargas|jonathand.fernandez@udea.edu.co|jonathand77|
|Valeria Alvarez Fernandez|valeria.alvarezf@udea.edu.co|vaf88|

## Instrucciones

Antes de empezar a realizar esta actividad haga un **fork** de este repositorio y sobre este trabaje en la solución de las preguntas planteadas en la actividad de simulación. Las respuestas deben ser respondidas en español o si lo prefiere en ingles en el lugar señalado para ello (La palabra **answer** muestra donde).

**Importante**:
* Como la actividad es en las parejas del laboratorio, solo uno de los integrantes tiene que hacer el fork; y sobre repositorio bifurcado que se genera, la modificación se realiza en equipo.
* Como la entrega se debe hacer modificando el archivo READNE, se recomienda que consulte mas sobre el lenguaje **Markdown**. En el repo adjuntan dos cheatsheet ([cheat sheet 1](Markdown_Cheat_Sheet.pdf), [cheatsheet 2](markdown-cheatsheet.pdf)) para consulta rapida.
* Entre mas creativo mejor.

## Homework (Simulation)

This program, [`process-run.py`](process-run.py), allows you to see how process states change as programs run and either use the CPU (e.g., perform an add instruction) or do I/O (e.g., send a request to a disk and wait for it to complete). See the [README](https://github.com/remzi-arpacidusseau/ostep-homework/blob/master/cpu-intro/README.md) for details.

### Questions

1. Run `process-run.py` with the following flags: `-l 5:100,5:100`. What should the CPU utilization be (e.g., the percent of time the CPU is in use?) Why do you know this? Use the `-c` and `-p` flags to see if you were right.
   
   <details>
   <summary>Answer</summary>
   1. What should the CPU utilization be?
   The CPU utilization should be 100% because both processes have instructions that use only the CPU and do not perform I/O operations.

   2. Why do you know this?
   Each process has 5 instructions (5:100), which means that 100% of its instructions use the CPU. Since there are two processes, each executes its 5 instructions sequentially, without pauses or input/output (I/O) operations.

   3. Did the simulation results confirm this?
   Yes, the execution showed that the CPU was busy 100% of the time, as indicated by the statistics at the end:

   Total time: 10 units
   CPU busy: 10 units (100%)
   I/O busy: 0 units (0%)

   This confirms that the CPU was in use the entire time and was never idle.
   </details>
   <br>

2. Now run with these flags: `./process-run.py -l 4:100,1:0`. These flags specify one process with 4 instructions (all to use the CPU), and one that simply issues an I/O and waits for it to be done. How long does it take to complete both processes? Use `-c` and `-p` to find out if you were right. 
   
   <details>
   <summary>Answer</summary>
   1. How long will it take to complete both processes?
   The total execution time is 11 time units, according to the simulation results.

   2. Why does it take this long?
   Process 0 executes 4 CPU instructions (4:100), occupying the first 4 times.
   Then, process 1 executes and performs an I/O operation (1:0) at time 5.
   While waiting for the I/O to complete, the process is blocked from time 6 to 10.
   Finally, at time 11, the I/O completes, and process 1 also completes.

   3. Did the simulation results confirm this?
   Yes, the execution showed that the total time was 11, with:

   CPU occupied by 6 units (54.55% utilization).
   I/O occupied by 5 units (45.45% utilization).

   This confirms that process 1 was blocked while waiting for its I/O operation, which increased the total execution time.
   </details>
   <br>

3. Switch the order of the processes: `-l 1:0,4:100`. What happens now? Does switching the order matter? Why? (As always, use `-c` and `-p` to see if you were right)
   
   <details>
   <summary>Answer</summary>
   1. What happens when the process order is changed?
   The total execution time changes to 7 time units, compared to 11 units in the previous execution.

   2. Why does this happen?
   Process 0 executes first and performs an I/O operation at time 1, entering the blocked state.
   While process 0 waits, process 1 begins executing at time 2 and uses the CPU for 4 time units.
   During this entire time, process 0 remains blocked until its I/O completes at time 6.
   Finally, at time 7, process 0 completes its execution with the io_done operation.

   3. Does the process order matter?
   Yes, changing the order reduces the total execution time from 11 to 7 units.
   In the original execution (4:100, 1:0), process 1 had to wait for the I/O to complete, causing a delay.
   In this case (1:0, 4:100), the I/O starts immediately and is processed in parallel with the execution of process 1, reducing the total time.

   4. Did the simulation results confirm this?
   Yes, the simulation shows that:

   The total time is 7 units (instead of 11).
   The CPU was occupied 6 units (85.71% utilization).
   The I/O was occupied 5 units (71.43% utilization).

   This confirms that changing the process order improved efficiency by allowing the CPU to work while the I/O is completed in the background.
   </details>
   <br>

4. We'll now explore some of the other flags. One important flag is `-S`, which determines how the system reacts when a process issues an I/O. With the flag set to SWITCH ON END, the system will NOT switch to another process while one is doing I/O, instead waiting until the process is completely finished. What happens when you run the following two processes (`-l 1:0,4:100 -c -S SWITCH ON END`), one doing I/O and the other doing CPU work?
   
   <details>
   <summary>Answer</summary>
   1. What happens when both processes are executed with -S SWITCH_ON_END?
   Process 0 starts and performs an I/O operation at time 1, entering a blocked state. However, due to the SWITCH_ON_END option, the system does not switch to another process while the I/O is in progress. Instead, it waits 5 time units until the I/O completes.
   Only at time 7 is the io_done operation executed, allowing process 1 to begin execution at time 8.
   Process 1 executes its 4 CPU instructions at times 8, 9, 10, and 11, finishing at time 11.

   2. How does this affect performance?
   The total execution time is 11 units, which is slower than the case without SWITCH_ON_END (7 units).
   The CPU was in use for only 6 time units, while the other 5 were idle waiting for I/O.

   3. Why does this happen?
   With SWITCH_ON_END, the system does not switch to process 1 while process 0 is waiting for I/O, wasting time.
   If SWITCH_ON_IO had been used, the system would have executed process 1 while process 0 was blocked, improving efficiency.

   4. Do the results confirm this?
   Yes, the data shows that the CPU was not used efficiently, as it spent 5 idle time units before executing process 1.

   The SWITCH_ON_END option makes the system less efficient when some processes can run while others are in I/O.
   </details>
   <br>

5. Now, run the same processes, but with the switching behavior set to switch to another process whenever one is WAITING for I/O (`-l 1:0,4:100 -c -S SWITCH ON IO`). What happens now? Use `-c` and `-p` to confirm that you are right.
   
   <details>
   <summary>Answer</summary>
   1. What happens when processes are executed with -S SWITCH_ON_IO?
   This time, the system switches to another process when one is waiting for I/O. Execution occurs as follows:
   Time 1: Process 0 starts and performs an I/O operation, entering a blocked state.
   Times 2-5: Instead of waiting, process 1 begins executing and uses the CPU for 4 time units.
   Time 6: Process 1 terminates, and process 0 remains blocked waiting for I/O.
   Time 7: Process 0 completes its I/O (io_done) and terminates.

   2. How does this affect performance?
   The total execution time is 7 units, which is more efficient than with SWITCH_ON_END (11 units).
   The CPU was busy the entire time, without wasting cycles, achieving better utilization.

   3. Why does this happen?
   With SWITCH_ON_IO, the system doesn't waste time waiting for process 0 to complete its I/O.
   Instead of idling, it executes process 1 while process 0 is blocked.
   This reduces the total execution time and improves CPU efficiency.

   4. Do the results confirm this?
   Yes, running with SWITCH_ON_IO shows that:
   ✅ Shorter total time (7 vs. 11 units)
   ✅ Higher CPU efficiency (100% utilization in 7 time units)
   ✅ The system doesn't waste time waiting for I/O

   Conclusion:
   Using SWITCH_ON_IO is more efficient when there are processes that can run while others are waiting for I/O.
   </details>
   <br>

6. One other important behavior is what to do when an I/O completes. With `-I IO RUN LATER`, when an I/O completes, the process that issued it is not necessarily run right away; rather, whatever was running at the time keeps running. What happens when you run this combination of processes? (`./process-run.py -l 3:0,5:100,5:100,5:100 -S SWITCH ON IO -c -p -I IO RUN LATER`) Are system resources being effectively utilized?
   
   <details>
   <summary>Answer</summary>
   1. What happens when processes are run with -I IO_RUN_LATER?
   This setting changes the system's behavior when a process completes an I/O operation:
   Instead of running immediately, the process remains in the READY state and does not immediately reclaim the CPU.
   This means that the already running process continues running, delaying the resumption of the process that completed the I/O.
   Example execution:
   Time 1: PID 0 starts and executes an I/O, while the others are in READY state.
   Times 2-6: PID 1 takes the CPU and executes 5 CPU cycles while PID 0 remains blocked on I/O.
   Time 7: PID 1 terminates, and PID 2 begins executing. Although PID 0's I/O has completed, it does not immediately reclaim the CPU.
   Times 12-16: PID 2 terminates, and PID 3 takes the CPU. PID 0 continues waiting.
   Time 17: Finally, PID 0 executes io_done, but does not take the CPU.
   Times 18-31: This behavior is repeated for subsequent I/Os from PID 0.

   2. Are resources being used efficiently?
   Not entirely.
   The CPU was only busy 67.74% of the time, meaning there were unused CPU cycles.
   The system does not prioritize processes that complete their I/O, which can cause delays in their execution.
   The total execution time was 31 time units, which is slower than if priority had been given to processes ready after I/O.

   3. How could efficiency be improved?
   If -I IO_RUN_IMMEDIATE were used (i.e., giving immediate priority to processes that complete their I/O):
   ✅ PID 0 could continue running immediately after completing its I/O.
   ✅ The total execution time would be reduced.
   ✅ The CPU and system resources would be better utilized.

   Conclusion
   With -I IO_RUN_LATER, the system does not prioritize processes that complete I/O, which causes delays and less efficient CPU utilization. This is not optimal for efficient resource utilization.
   </details>
   <br>

7. Now run the same processes, but with `-I IO RUN IMMEDIATE` set, which immediately runs the process that issued the I/O. How does this behavior differ? Why might running a process that just completed an I/O again be a good idea?
   
   <details>
   <summary>Answer</summary>
   1. How does this behavior differ from IO_RUN_LATER?
   With -I IO_RUN_IMMEDIATE, a process that completes its I/O operation immediately reclaims the CPU. This significantly changes system efficiency:

   Time 1: PID 0 starts an I/O operation, while the others are READY.
   Times 2-6: PID 1 executes its 5 CPU cycles while PID 0 is blocked on I/O.
   Times 7: PID 0's I/O completes, and PID 0 now executes immediately (RUN:io_done).
   Times 8: PID 0 starts another I/O operation.
   Times 9-13: PID 2 executes its 5 CPU cycles while PID 0 is blocked.
   Times 14: PID 0 completes its I/O and reclaims the CPU immediately.
   Time 15: PID 0 initiates another I/O.
   Times 16-20: PID 3 executes its 5 CPU cycles while PID 0 is blocked.
   Time 21: Finally, PID 0 completes its last I/O operation and executes io_done.

   2. Why is it a good idea to immediately execute a process that completes its I/O?
   ✅ Better CPU utilization: In this run, the CPU was busy 100% of the time, compared to only 67.74% in IO_RUN_LATER.
   ✅ Lower total execution time: 21 time units instead of 31, which means the system processes tasks faster.
   ✅ Less waiting for processes with I/O: They don't remain READY without executing, reducing response latency.

   3. Conclusion
   Running a process immediately after completing its I/O improves system efficiency by keeping the CPU busy and reducing wait times. IO_RUN_IMMEDIATE is the best option for systems where I/O is frequent and latency is desired.
   </details>
   <br>


### Criterios de evaluación
- [x] Despligue de los resultados y analisis claro de los resultados respecto a lo visto en la teoria.
- [x] Creatividad y orden.