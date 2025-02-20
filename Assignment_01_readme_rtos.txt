Pre-assignment steps :

1) install your STM32CubeIDE on Linux, or Windows - Linux preferred 
   --->find your installation directories and binaries ??
       --->possibly, under /opt/st/<*>

       --->possibly, /home/<username>/st/<*> 



2) create a simple, bare-metal blinky code, using STM32CubeMX, for 
   our chip-set and board 
   --->configure for our specific chip-set and enable LEDs 
       --->initially, use the default clock-tree - do not 
           change it
           --->refer to FreeRTOS_v1.7-CMSIS OS API pdf from 
               STM - go through the selected slides and comments, 
               before you start your assignments 
               -->you can refer to these slides and comments, 
                  time to time ??       
           --->however, APIs used are from CMSIS, whereas, 
               we will be using native APIs of FreeRTOS 
           --->still, core aspects of FreeRTOS are very 
               well covered, for CM-x processors/STM32 boards 
           --->if you need more details on CMSIS APIs, you 
               can refer to rtos.DM00105262.pdf 
               --->this is an application note from 
                   STM-32, for FreeRTOS and related 
                   programming
           --->refer to FreeRTOS official references,
               as per the comments ??
               --->Mastering manual 
               --->reference manual 
           --->also, refer to 4_rtos_updated_sep2022_new.txt ??
               --->selective sections ?? refer to comments ??  
 
       --->we will reconfigure the clock-tree, during 
           FreeRTOS assignments and scenarios ??
           --->initially, let us not touch the clock-tree
               of the micro-controller 
           --->however, when we do certain hw-Timer/timing related
               assignments and IO related assignments, 
               we will reconfigure clock-tree
           --->in the Indus-board, external clock source does not 
               work, due to hw bugs, but future boards will be 
               rectified, so do not enable HSE clock-configuration
            --->if you do, clock-configuration code will fail
                and enter an STM32-HAL assert code, 
                so your application 
                will crash, during hw initialization 
                --->if you are running your application under
                    debug control of Segger-debug-link, 
                    use pause icon to stop the cpu and 
                    you will find the debugger halts, in 
                    the assert code of STM32-HAL  
                 

   --->generate code 
   --->run and debug 

--->resume post-lunch on 08.01.2024....

3) next, we will enable FreeRTOS and configure FreeRTOS set-up ??  
   --->click and select your *.ioc file of your project 
       --->enable freertos 
       --->freertos will use systick as its hw-timer for time-base 
       --->for STM32-HAL, we need to select TIMx as hw-timer
           for time-base ??
                --->select your TIMx ??

Note: since systick must be dedicated to FreeRTOS time-base, 
      we need to change the time-base of STM32-HAL software-stack
      so, do the appropriate changes , in the .ioc configuration 
      settings 

4) enable FreeRTOS, in your middleware settings  
    ----->do not change any configuration settings of 
          FreeRTOS ??
          --->we will do these settings manually ??


5) next, in the NVIC section of .ioc configuration, do the following :
   --->one, TIMx(TIM1)'s  interrupt's  hw-priority must be set to 
       0 - as per rules of FreeRTOS - we will explain, in lectures ??
       --->refer to 4_rtos_internals_frtos.pdf 
   --->two, systick's exception's hw-priority must be set to 
       15 - as per rules of FreeRTOS - we will explain, in lectures ??
       --->refer to 4_rtos_internals_frtos.pdf 

6) now, generate project/code, for HAL and FreeRTOS 
   initialization 
   ----->now, go to your project layout and spot your 
         main.c 

   ----->make a copy of this main.c and rename it 
         main_frtos.c, in the same project location/dir 

   ----->do not edit main.c  


   ---->disable  main.c , in the project - there is 
        a technique - follow and complete - on the file, 
        right-click - you will see some option/settings ??

   ---->now, edit main_frtos.c , as per main.c and main2.c,
        provided on the cloud - follow strictly  ???
        ---->if you have doubts, ask  ??


  


   ---->disable  main.c , in the project - there is 
   ---->disable  main.c , in the project - there is 

   --->for these steps, refer to STM32CubeMX initialization 
       guide ??


////let us assume that there are 3 tasks, with equal priorities
//check task creation API and priority values...ideally, set it to 
//1 or 2 ....all tasks must be assigned same priority - in this scenario, 
//           we want to manage equal priority tasks ??
//
//--->refer to FreeRTOSConfig.h ---> we may need to check and modify some of the
//    configuration parameters
//    --->refer to FreeRTOSConfig.h, in your project layout ??  
//        --->refer to scenarios below ??
//
//refer to #define configMAX_PRIORITIES			(  8 ) 
//  --->this allows scheduling priorities 0 ---> 7
//  -->0 is the lowest scheduling priority /importance
       --->typically, 0 is reserved for idle task of the system..
       --->we can use the range 1 - 7, in this case....
//  -->in this case, 7 is the highest scheduling priority /importance
//---->do not confuse these with hw int. priorities - hw int priorities
//     are strictly associated with int. events and ISRs/handlers
//---->you can also refer to freertos reference manual....for configuration
//     parameters..chapter7
// 
//#define configTICK_RATE_HZ                       ((TickType_t)1000) 
//   --->this rtos configuration parameter defines no. of systicks per
//       second 
//       --->if set to 1000, systick's period is 1msecs
//       --->if set to 100,  systick's period is 10msecs
       --->these parameters are project-specific, so 
           we will reconfigure ??
//
// 
////refer to project code/main.c 
//--->you can refer to Topic - 7 of 4_rtos_updated_sep2022_new.txt
 ///lecture - since 3 tasks are of same priority, there are 3 possible schedules/
  //         configuration settings  -
 ///         (a) if kernel preemption is enabled(set to 1) and 
 ///             RR/time-slicing is enabled(set to 1)
 //              --->if preemption is enabled, priority-based 
                     scheduler's preemption-points/
                     trigger-points are enabled 
                 --->actually, priority-based scheduling policy is 
                     always present in the kernel, but may or 
                     may not be effective 
                 --->in addition, if preemption is enabled and 
                     time-slicing is enabled, preemption-points/
                     trigger-points of
                     of 
                     time-slicing scheduler/policy are enabled
                --->RR scheduler is always present, in the 
                    system - however, it may or may not be 
                    effective   
 ///         (b) if kernel preemption is enabled and  
////             RR/time-slicing is disabled
                 --->if preemption is enabled, priority-based 
                     scheduler's preemption points/
                     trigger-points are enabled 
                 --->actually, priority-based scheduling policy is 
                     always present, but may or may not be 
                     effective 
                 --->in addition, if preemption is enabled and 
                     time-slicing is disabled, preemption
                     points/trigger-points of 
                     time-slicing policy are disabled, so 
                     time-slicing policy will be ineffective 
 ///         (c) if kernel preemption is disabled, 
////             RR/time-slicing is disabled(anyway, ineffective) 
                 --->if preemption is disabled, priority-based 
                     scheduler's preemption points/
                     trigger-points are disabled
                 --->actually, priority-based scheduling policy is 
                     always present, but if preemption is disabled, 
                     this policy will be ineffective
                 --->in addition, if preemption is disabled and 
                     time-slicing is enabled or disabled, 
                     preemptions/preemption-points/trigger-points of 
                     time-slicing policy are disabled, so 
                     time-slicing policy will be ineffective - 
                     actually, 
                     scheduler-triggers are disabled 
//assignment 1 and problem 1 : there are many scenarios - modify and test
//                             the application of code_sample1_stm32_f3disc.zip 
//
///understand the above configuration settings, in FreeRTOSConfig.h ??
//
//if you need any details, refer to freertos reference manual pdf for
// specific details.... 
//
//in addition, refer to 4_rtos_assignment_hints_15.06.2019.txt - follow the hints
////           for each of the above settings/schedules, test and verify the 
  //           functional correctness of the jobs - 
 //            try the above configuration settings, rebuild the RTOS/kernel and the application -
 //            test the behaviour of the application tasks, in each case and justify the results -
 //            if you need more details, refer to the Master FreeRTOS guide pdf and 
 //            FreeRTOS reference manual pdf     
 
 //lecture - refer to FreeRTOSConfig.h
 //
 //lecture - if the kernel preemption is disabled, 
 //          is there a possibility of
 //          a proper schedule ?? check the implementation details and also, test your set-up, 
 //          using each configuration ?? are the results, as expected ??
 //

---->once you have tested all the above scenarios, you can go ahead and test 
     the next scenario ??
//if we have assigned equal priorities to tasks and disabled time-slicing also, 
//we have a perfect scenario, for co-operative scheduling...
//
//we can now use taskYIELD() api, in each task's job - what will taskYIELD() do ??
//--->current taski's TCBi  will be added to tail of an rq of respective priority
//--->scheduler will be invoked and effectively, we will have a co-operative
//scheduling....another task at the head of the same Rq will be selected and dispatched
//--->if we use taskYIELD(), in every task, the above operations are repeated 
//--->we can achieve a "manual round-robin scheduling-policy" 
 
//           in the above co-operative schedules, if the time-slicing is disabled, 
//           add taskYIELD, at appropriate 
//           places, in the tasks, as needed - this will provide a suitable 
             co-operative scheduling, 
//           for our requirements - 
             if you need more details, refer to Mastering FreeRTOS and FreeRTOS
//           reference pdfs -- taskYIELD is explained ....
//           what is the benefit of using taskYIELD, in the task methods, for co-operative scheduling ???
///if we select co-operative schedule/related configuration settings, 
///we may need taskYIELD, since our tasks/jobs do not use blocking 
///services - meaning, we will face starvation, in one or more tasks,
//if taskYIELD is not used  
//
//initially, for a cooperative schedule, test the jobs, without 
//taskYIELD ??? understand the starvation problems ??
//
//next, add taskYIELD and test and verify, that starvation is eliminated
//and our application's functional correctness 
//
//what will be the best configuration settings, for a co-operative scheduling, 
//using taskYIELD and a set of tasks with equal priorities ??
//     --->preemption should be disabled, as well as time-slicing should be 
           disabled
//do not use unequal priorities and taskYIELD(), since this will not 
//effectively provide co-operative scheduling - it does not make 
//sense 
// 
//
//if we have any preemption requirements, other than time-slicing, we 
//neet to set configUSE_PREEMPTION to 1 - otherwise, set it to 0 -
//of course, configUSE_TIME_SLICING must be set to 0, since we want 
//any switching between tasks, under our control 
//
//for every application design and OS set-up /configuration, we must 
//test and validate our application's tasks and related functional 
//temporal paramters 
//
//
//problem 1 - second-part
//
//now, modify the above main2.c to support a single, re-entrant task method only 
//
//every task is assigned the same task-method - however, each task is passed a
//different param4 --> in this case, the only parameter is LEDi
//
//however, create an object and provide a field, for LEDi, in this object-
//create one object per task, fill and pass pointer to respective, object ??
//we will be using similar techniques, in the upcoming scenarios, but 
//we may need to pass more fields, in these objects ??
//
//all the specific parameters must be passed, using param4 of xTaskCreate() 
//
//refer to 2_main.c   
//
//
//problem-2 - starts here - initially, certain task-modelling is 
//            discussed - after this discussion, problem-2 is being
//            described
//
//NOTES : certain OS peculiarities and related techniques 
//
//refer to rt_e_concepts.pdf - section 15.2.3 - barrier-mechanism  
//
//this is known as barrier-mechanism - can be used to co-ordinate a set of
//tasks to synchronize their order of execution - 
//so, we may order the execution of a set of tasks, at the beginning of
//their task methods, before entering an infinite-loop of respective jobi - 
//we may need to add initial-delays, or synchronization techniques ??
//   
//--->refer to Topic - 10   of 4_rtos_updated_sep2022_new.txt 
//-->refer to 9_main.c - contains vTaskDelay() and other sw-timer APIs 
/
//refer to 18_offset_periodic_main.c - refer to code and  comments on 
//using vTaskDelayUntil(), as described below 
//refer to the above code-sample coding techniques - implement and test
//-->refer to vTask_LED_ON( void *pvParameters ) and vTask_LED_OFF( void *pvParameters ) 
//--->there is a vTaskDelayUntil(), before while() loop, in these
//    task-methods - understand the usage and comments 
//
//in the above code-sample, vTaskDelayUntil() is being used to add a time-offset/
//initial-delay,
//at the beginning of a task-method, before entering its job's loop 
//
//for instance, if there are 3 tasks, we can do the following :
//
//task1 ---> method1 ---> vTaskDelayUntil(&p1, offset_delay1);,before while(1)
//task2 ---> method2 ---> vTaskDelayUntil(&p1, offset_delay2);,before while(1)
//task3 ---> method3 ---> vTaskDelayUntil(&p1, offset_delay3);,before while(1)
//
//in the above set-up, p1 will contain a common, past time-stampi, which
//will  be the reference time-stamp, for vTaskDelayUntil() of 
//all the tasks 
//
//offset_delay3 > offset_delay2 > offset_delay1 
//
//based on the above set-up, task1's job-loop  will be delayed, by 
//offset-delay1 - so, the first scheduling/dispatching of job-loop
//of task1 will be, after offset-delay1 
//
//based on the above set-up, task2's job-loop  will be delayed, by 
//offset-delay2 - so, the first scheduling/dispatching of job-loop
//of task1 will be, after offset-delay2  
//
//based on the above set-up, task3's job-loop  will be delayed, by 
//offset-delay3 - so, the first scheduling/dispatching of job-loop
//of task3 will be, after offset-delay3 
//
//
//

//problem 2:
     --> we need to add time-offset to our tasks, as per the 
         above discussions 

     --->task1's job-loop  must be scheduled, first 
     --->task2's job-loop  must be scheduled, next
     --->task3's job-loop  must be scheduled, last 

     --->to achieve this, we need to use vTaskDelayUntil(), like 
         the first usage, in   
         //refer to 18_offset_periodic_main.c - refer to code and  comments on
         //using vTaskDelayUntil(), as described below
         //
         //in the above code-sample, vTaskDelayUntil() is being used to add a time-offset,
         //at the beginning of a task-method, before entering its job's loop
         //
         //refer to task methods of vTask_LED_ON() and vTask_LED_OFF() 
         //
         //in our case, we need to modify all the three tasks, per this 
         //coding-model ??

NOTE: you can add the following changes to your project's files, so that
      we can use printf(), using services of ITM debug-component
      --->refer to 4.3.5 of stm32cube-ide's user-guide 
          and follow the instructions 
      --->we need to have a board connected and our mode must be
          debug-mode ??
      --->make the changes and add printf()s to your task methods
 

//changes to printf() and syscalls.c, in our project 
//
//since we will be using printf(), in different tasks, 
//we need to lock and unlock the printf(),using 
//an rtos-mutex 
//
//
//-->refer to listing 121 of MasteringFreeRTOS_tutorial.pdf
//now, add a new vPrintString() and set-up a new-method ??
//--->add a mutex-lock operation
// printf("%s\n", stringptr); 
//--->add a mutex-unlock operation
//
//we are creating a thread-safe/task-safe version of printf(), 
//using a mutex-lock 
// 
//
//refer to  19_mutex_main.c, for mutex set-up/initialization and operations
//
//after the above changes, test printf(), in three tasks' methods - 
//can be non-reentrant, or reentrant ??
//   --->ideally, we need to use a mutex-lock around printf()s, in 
//       task-methods ??
//
// //refer to chapter 7/section 7.3 of MasteringFreeRTOS.....pdf / listing 121
//  xSemaphoreTake(xMutex1, portMAX_DELAY);
//  printf("vTaskDelay latency is .... %u\n", (timestamp2-timestamp1));
//  xSemaphoreGive(xMutex1);
//
//
//
//
//
//finally, use TIM2(or another external-timer) of the controller, in up-counting /time-base 
//mode - refer to code_sample1_stm32_f4disc1_timing_1.zip,
//12_time_base_main.c/main_time_stamps.c  - we must program TIM2, in 
//a high-resolution, up-counting mode, so that we can capture 
//high-resolution time-stamps  
//
//capture time-stamps, at the line, after the vTaskDelayUntil(),
//before while(1) loop,in  the
//above tasks - print the time-stamps...
//-->find the order of the start of  executions of while(1) loops of
//   tasks 
//--->find the difference, in the starting-times ??
//
//--->does the above task-modelling work, as expected ??
//



//after finishing the above assignment problems/scenarios, 
//you can skip the following, for now ?? 
//we can switch to the next assignment, Assignment_02_readme_rtos.txt ??
//
//problem 3:
               //this problem can be solved after 2 lab sessions 
//             in the above tasks/problems, repeat, by 
//             replacing the simple/crude 
//             software delay loop, 
//             using xTaskSetTimeOutState() and xTaskCheckForTimeOut() 
//             services of RTOS - these are non-blocking 
               services - will 
//             provide more precise time delays,using RTOS services -
//             refer to FreeRTOS manual, for 
//             more details of the APIs and their usage ??
               --->still, we need to understand the 
                   resolutions/errors/inaccuracies due 
                   to the services/their resolutions
               --->still, there can be latencies  


