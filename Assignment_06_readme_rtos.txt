
Part-I  - Assignment-06

    --->now, refer to Assignment-02 and problem-7 - we need to measure performance
        parameters of tasks, in this problem-7 and different scenarios mentioned ??
//
//---->refer to slides 39,45-46,52-58 and 60-   of  17_desd_rtos_March2023.pdf
// --->periodic-jobs are well illustrated and different
//       real-time parameters are highlighted
//---->also, refer to 4_rtos_internals_ftros.pdf/ slides 1-4
//
//for resolutions of hw-timers, refer to 3_rtos_lab_instructions_Sep2021_Mar2022.txt
//
//-->specifically, refer to slides 62- and map the following :
//
//for Task1, 1 represents systick-events
//for Task1, 2 represents periodic, task-release times
//
//for Task2, 1 represents systick-events
//fpr Task2, 2' represents periodic, task-release times
//
//
//for assignment-7, we need to capture time-stamps, at
//task-release points of periodic-tasks-  we need to
//capture these time-stamps and find difference between
//time-stamps of two, consecutive periodic iterations -
//these values must match the expected task-period and
//there can be latencies - however, latencies must be
//acceptable and deterministic ??
//
//initiallyl, test a single periodic-task, only - once
//we have reliable results, we can add further tasks and
//do more testing ??
//
//
~                                                                                                                                             


Part-II - Assignment-06

----->in this part of assignment, we are measuring certain real-time parameters, 
      like interrupt-latency 
      and task-scheduling latency 

      ---->refer to 4_rtos_updated_Sep2022.txt 
      ---->refer to slides 52-59 of 17_desd_rtos_March2023.pdf
           --->go through the slides and understand the comments
           --->visualize and interpret the details  

 
      ---->there are certain latency parameters highlighted, in these slides
      ---->refer to 4_rtos_updated_sep2022.txt, for more details on 
           various latencies
    --->refer to 4_rtos_assignment_hints_15.06.2019.txt, for details on 
        systick's internal-counters' resolutions ??

 
           -->refer to Topic-10 and Topic-14, Real-time perspective-1 ---> read and understand 
                                                    the entire section
           -->refer to Topic-14, Real-time perspective-2 ---> read and understand 
                                                    the entire section 


      ---->ta' - ta ----> is known as interrupt-latency 
                         --->for details, refer to the above document
                         --->for instance, due to vector fetching and 
                             hw context-saving  

      ---->tb - ta ----->is known as task-scheduling latency 
                         ---->also, known as task release-time, latency 
                         ---->this is due to "isr/timer-isr processing", 
                               and task-switching done, 
                              due to unblocking of a higher-priority task - 
                              also, includes a few-cycles, for invoking pendsv 
                              handler, after current ISR/handler completes - 
                              last part is known as scheduler-latency - 
                              also, involves scheduler-overhead, which is 
                              invoking rtos-scheduler's  code and 
                              selecting an eligible task + context saving +
                              context loading
--->refer to slides 50 and 52 of freehand_diagrams_18.02.2022.pdf  - this diagram is 
    explained, for systick-handler and related preemption-points - we can 
    redraw similar diagrams, for rtos-aware IO-isrs and other system-exceptions 
     --->first, visualize 'ta' 
     --->next, visualize  "ta' "
     --->next, visualize  'tb  '
     --->next, visualize  'tc  '
     --->next, visualize  'tc' '

     ---->next, try to understand and interpret 1), 2), and 3), in 
          a pendsv-handler 
          --->if a current-taski is being preempted, its context-saving
              is done, in 1)
          --->next, 2) of rtos is invoked, which is the rtos-scheduler/
              algorithm 
          --->lastly, the newly, selected Taskj's hw-context will be 
              loaded, in 3) 
          --->so, 2) contributes to scheduler-overhead 
          --->so, 1) + 3) contributes to context-switching overhead/time
          --->after the systick-handler, it may take a few-cycles to 
              invoke pendsv-handler - it is known as scheduler-latency 
          --->systick-handler will do sw-timer processing + sw-clock
              updation - so, there will isr/handler processing-overhead 
 ---->do not conclude - the above descriptions apply to a typical, scenario 
---->if there are other scenarios, you may need to add more latencies to 
     tb - ta
--->similarly, for tc'  -  ta
---->so, we can use the above scenarios/diagrams, templates, but not 
     conclude ???
    

      ---->tc' -  ta  ---->is known as task-response time


      ----->task-response time depends on other factors, as well 
            --->in this context, interrupt-latency does influence task-response time -
                if interrupt-latency is increased, task-response time will be 
                increased 
            --->in this context, if ISRs are longer and task-scheduling overhead is 
                longer, it will increase task-response times...

       ----->so, we need to control the above latencies, so that they are time-determinitic, 
             as well as other task operations/job operations, so that the task-response
             times are deterministic 


  ---->so, far measuring the above latencies, we need to use certain hw clock 
       / time-base - using a hw clock/time-base, we need to measure certain 
       time-stamps, at certain points, in our ISRs and task-methods
       ---->for instance, refer to 22_timing_measurement_main.c  
            (23_timing_measurements_main2.c)
            --->we are using SySTick->VAL , as time-base to collect 
                time-stamps - do not use external, hw-timers, for 
                this job - they will not serve the purpose ?? 
            --->for instance, refer to one of the task-methods, 
                in 22_timing_measurement_main.c  ??

  ----->so, how do we measure
        "systick-interrupt latency" and "task-scheduling latency
         of a periodic-task"(periodic-task using vTaskDelayUntil() ) ???

   -->for this requirement and scenario, we cannot use th e
      external /high-resolution hw-timer ?? you can analyze,
      what happens, if we use an external hw timer, for this
      job ??

      -->we need to use the down counter of VAL of systick
          -->SysTick->VAL will provide the current down
             counter-value
          -->refer to the above code-sample, for strategic points of
             measurement of time-stamps, for interrupt
             latencies and task scheduling latencies
          -->refer to core_cm4.h(part of cmsis library,
             which is part of vendor's embedded sw stack),
             for a structure defining
             systick's registers/layout and also, memory
             mapping this structure object to a set  of
             memory-mapped addresses - this maps the registers
             to appropriate memory-mapped registers

             -->because of this, we can do
                   SysTick->VAL 

          -->based on the above, if we need to read the current
             running counter of systick/VAL, we can access
             SysTick->VAL  ???

   -->following coding steps enable us to measure systick interrupt latency
      and task scheduling latency :

  for measuring point a), use the following time-stamp variables
               ->timestamp1 is set to a hard-coded value, which will be the reload
                 value, when the systick generates the hw int. and is reloaded/restarted

   ->unsigned long ultimestamp1 = ( configCPU_CLOCK_HZ / configTICK_RATE_HZ ) - 1UL; 
           //timestamp1 will provide 'ta' 
   ->unsigned long ultimestamp2 = 0; //collect it in the systick-handler of rtos-code 
           //timestamp2 will provide "ta' " 
   ->unsigned long ultimestamp3 = 0; //collect it just after vTaskDelayUntil(), in 
                                     //a periodic-taski - 
           //timestamp1 will provide 'tb ' 

---->refer to freehand diagrams on periodic tasks and important
     events - refer to above discussions 

->unsigned long ultimestamp1 = ( configCPU_CLOCK_HZ / configTICK_RATE_HZ ) - 1UL;
    (this is used, for measuring point 'ta', in this context)

     ->timestamp2 needs to captured, at the first instruction of
       systick handler(ta'), in port.c
              timestamp2 = SysTick->VAL

     ->(timestamp1 - timestamp2)  will provide systick's interrupt
       latency, in this RTOS platform

     ->using timestamp3, we can measure the actual task release time of
       a periodic task(tb) - in a periodic task, capture timestamp3
       just after vTaskDelayUntil()

              timestamp3 = SysTick->VAL

     ->(timestamp1 - timestamp3) will provide us the task-scheduling
        latency ???

     ->we can capture several such time-stamps, for several systick
       int. events and jobs cycles, using  larger buffers

             timestamp2[i] = SysTick->VAL;

             timestamp3[i] = SysTick->VAL;

--->we can print the values , using printf, for several iterations

--->based on the resolution of systick-timer, calculate the 
    actual, latencies ?? do not conclude - these are typical, 
    latencies, in a simple system - may be more complicated, due 
    to hw platform, sw platform, and application-design ??









Part-IIi - Assignment-06
 

----->in the first part of this assignment, you need to use ex23_4_new.zip
      --->refer to 4_rtos_updated_sep2022.txt , for rtos interrupt management and 
          related topics 
      --->also, refer to MasteringFreertos tutorial, chapter 7 and chapter 9
            --->chapter 7 provides basics of rtos interrupt management 
            --->chapter 9 provides advanced code-samples, for rtos interrupt management 


     --->first, understand the project codes - main.c and stm32f4xx_it.c 
         --->in main.c, use task1 and deferred task only 
         --->in stm32f4xx_it.c, focus on rtos-aware ISR only 
         --->we are using user-button to generate hw int. event and its rtos-aware ISR is 
             being used 
         --->this rtos-aware-isr and deferred-task are connected, using a binary semaphore 
         --->set both configUSE_PREEMPTION and configUSE_TIME_SLICING to 0
         --->as shown, in the rtos-aware ISR, there is portYIELD_FROM_ISR(xHigherPriorityTaskWoken1); 
             needs to be used, as an explicit, preemption point 
         --->refer to the comments, in the rtos-aware ISR 

     ---->in the first scenario, set task1's priority to 2 and deferred-task's priority to 1
          --->test the behaviour of task1, user-button interrupt event, rtos-aware-isr , and deferred-task     
          --->describe the observations and your understanding 

    ----->in the second scenario, set task1's priority to 2 and deferred-task's priority to 3
          --->test the behaviour of task1, user-button interrupt event, rtos-aware-isr , and deferred-task
          --->describe the observations and your understanding 

 
 

:w

