 //
 //lecture - refer to FreeRTOSConfig.h and FreeRTOS manuals, as needed
 //
 //lecture - refer to lecture notes, for details of sw clock,
 //          sw timers, vTaskDelay(), and 
 //          vTaskDelayUntil() - read and understand, before proceeding 
//   ------>refer to Topic - of sw-timers     , in 4_rtos_updated_sep2022.txt
//   ------>refer to 18_offset_periodic_main.c
//   ------>refer to 16_3_tasks_acc_console_thresholdleds.c

 //
 //
//lecture - in these samples/projects/assignments, we are replacing 
//          crude sw loops, with loops involving RTOS services, like 
//          vTaskDelay(), or vTaskDelayUntil() - select appropriate 
//          services, as per the requirements
//          in addition, these are blocking services, so may provide
//          better implementation, for different schedules/configuration
//          settings - for instance, we will not be, using 
//          taskYIELD, as there is no need - even if we use cooperative 
//          scheduling, we will be preferring blocking services over 
//          yielding services -  we will be using cooperative scheduling, 
//          along with blocking RTOS services, not yielding RTOS  services
//          - if timing issues need to be handled,you can use timing 
//          services, otherwise services, like taskYIELD can do the job
//
//--->based on the below mentioned scenarios, in different problems, 
//    we can select configuration settings of scheduling policies and
//    also, use appropriate services, like vTaskDelay() or vTaskDelayUntil(), or
//    HAL_Delay() ?? or, some other services , as needed - we are responsible, for 
//    for our coding ??
//
//HAL_Delay() is less-efficient, compared to vTaskDelay() and vTaskDelayUntil(), 
//in this rtos platform - still, if HAL_Delay() suits our application design, 
//we can use it 
      

//
///in all the below mentioned problems, you need to satify 
//the requirements of jobs, using appropriate RTOS services
//

 //
 //assignment problem 1:
 //
 //           create a task, which will do the following :
              - drive a single LED on the board
              - 2000 milliseconds on-time 
              - 500  milliseconds off-time
              - use vTaskDelay()/vTaskDelayUntil() appropriately  
                to implement appropriate delays, as needed - you must
                select the most appropriate delay API and use ?? 
              - model the tasks | code and implement | test | verify 
              - you may change the timing parameters of on-time and off-time, 
                and test the task's behaviour - check, if the changes are 
                effective, as expected ??       
                --->instead of 2000 msecs --->  4000msecs               
                --->instead of 500 msecs --->   1000msecs               
//--->you can use HAL_Delay() or vTaskDelay(), as per your task's job
//--->you must select the most appropriate scheduler configuration settings,
//    for this application ?? change FreeRTOSConfig.h and re-build the 
//    FreeRTOS + our application - test and verify ??
//
//we need to do the following static analysis of the 
//above job :
//--->we need to check, if we can use HAL_Delay(), in place of vTaskDelay() ??
//-->we need to check, if we can use vTaskDelayUntil(), in place of 
//   vTaskDelay() ??
//-->justify the answers ??
//
//
//refer to Assignment_04_readme.txt, for more details on time-stamp 
//capturing and usage ?? refer to main3.c/ main4_fully_reentrant.c ??
//
//--->in these timing measurements, we will not measure int.latencies/
//    task-scheduling latencies - these jobs are shifted to 
//    Assignment_06_readme.txt 
//
//
//--->we need to test and do run-time analysis of the above job(s) :
//--->we need to use an up-counting hw-timer(a free hw-timer) - it must be counting,
//    at a higher-resolution ?? - find suitable resolution 
//--->you can refer to chapter-5 of eres1_realtime.pdf - it provides
//    certain illustrations - do not just copy - take the techniques 
//
//--->also, refer to 12_time_base_main.c /main3.c / main4_fully_reentrant.c 
//      --->a hw-timer is used, for up-counting - check the resolutions 
//      --->check the HAL apis used 
//
//---->using the above techniques, you can add time-stamp measurements
//     to the above and collect data 
//---->using this set of run-time data, we can do a timing-analysis ??
//
//
 
 





//assignment problem 2:
//            - in the above problem, modify the single task code to 
                drive 4 LEDs, with different timing parameters - 
                different on-time(s) and off-time(s)  ??? you must 
                use only, one task to complete the job    
///           - in the above context, first test, for 2 LEDs, with 
                different timings - once the design is tested and 
                verified, add 2 more LEds 
              - timing issues need to be strictly handled 
              - you are free to replace simple jobs, with 
                non-trivial embedded jobs    
//--->you must select the most appropriate scheduler configuration settings,
//    for this application ?? change FreeRTOSConfig.h and re-build the 
//    FreeRTOS + our application - test and verify ??
//
//NOTE: for the above problem2, there are many configuration scenarios :
//1) ---> if all the tasks are assigned equal priorities, we can just 
//        set configUSE_PREEMPTION to 0 and configUSE_TIME_SLICING to 0
//
//2) ---->however, if one or more tasks are assigned higher priorities, 
//        we must set configUSE_PREEMPTION to 1 and 
//        configUSE_TIME_SLICING to 0 - 
//        Reasoning : let us assume, that a higher priority task invokes
//                    vTaskDelay() or vTaskDelayUntil(), the task/tcb
//                    will be blocked, in a sw-timer list - in the near 
//                    future, its sw-timer will expire and the task/tcb 
//                    will be added to an Rq - since it is of higher 
//                    importance, it must be scheduled, immediately, so  
//                    certain preemption-points must be enabled - for this, 
//                    configUSE_PREEMPTION must be set to  1 - in this 
//                    scenario, the sw-timer processing and unblocking
//                    the above task is done, in systick-handler - so,
//                    configUSE_PREEMPTION will enable a preemption-point
//                    , in systick-handler, for handling higher-priority
//                    task unblocking and related scheduling 
//
//          

//assignment problem 3:
//            - create a periodic task and manage, using vTaskDelayUntil() 
              - drive the Green LED 
              - 2 seconds of on-time 
              - 2 seconds of off-time 
              - use appropriate parameters, for vTaskDelayUntil()
              - we can use vTaskDelay(), but we wish to solve 
                this problem, differently ??
              - model | implement | test | verify ??? 
//--->you must select the most appropriate scheduler configuration settings,
//    for this application ?? change FreeRTOSConfig.h and re-build the 
//    FreeRTOS + our application - test and verify ??
//

//assignment problem 4:
//            - create 2 periodic tasks and manage, using vTaskDelayUntil() 
              - tasks must be assigned the same real-time priority 
              - in the first task, drive  the  Green LED 
              - in the second task, drive the  Red   LED 
              - 1 second of on-time and 1 second off-time,for   Green LED
              - 2 seconds of on-time and 2 seconds off-time,for Red   LED
              - use appropriate parameters, for vTaskDelayUntil()
              - model | implement | test | verify ???
              - what happens, if we assign unequal priorities ?? explain 
                the behaviour ??
---->we may use different on and off timings, as per our visualization 
     requirements ??
--->if you need better visualization, you may add time-offsets to 
    jobs ?? you need to use first vTaskDelayUntil() above the while(1) 
    loop of our job code ??
//--->you must select the most appropriate scheduler configuration settings,
//    for this application ?? change FreeRTOSConfig.h and re-build the 
//    FreeRTOS + our application - test and verify ??
//
 
---->first, just implement Green LED task and test ?? for its behaviour ??
              
              task1(void *param)
              {
                 //
                 //declare and use a local-variable 
                 //getting current time-stamp from sw-clock of RTOS
                 //
                 //
                 xlocaltime = xTaskGetTickCount();

                 while(1)
                 {
                    
                     HAL_GpioWritePin(p1, p2, p3);//SET green LED
                     //delay, for 1 sec, so that, LED is on 
                     vTaskDelay(1000);  //on-time of 1 sec


                     HAL_GpioWritePin(p1, p2, p3);//RESET green LED
                     //we are designing a periodic-task, with a period of
                     //2seconds, which will also, take care of 
                     //1second off-time
                     //
                     vTaskDelayUntil(&xlocaltime, 2000);

                 }
                     
               }       


---->second, just implement RED LED task and test ?? for its behaviour ??
              task2(void *param)
              {

                 xlocaltime = xTaskGetTickCount();

                 while(1)
                 {
                     HAL_GpioWritePin(p1, p2, p3);//SET green LED
                     vTaskDelay(2000);  //on-time of 1 sec
                     HAL_GpioWritePin(p1, p2, p3);//RESET green LED
                     vTaskDelayUntil(&xlocaltime, 4000);

                 }
                     
               }      
 
//assignment problem 5: 
              - extend the above problem, for 4 LEDs 
              - each LED should be managed, using a separate task and 
                different on/off timing - 1 sec, 2 secs, 3 secs, and 4 secs, 
                respectively, for on / off times 
              - assign equal priority and model | test | verify ??
              - assign unequal priorities and model | test | verify ??
//--->you must select the most appropriate scheduler configuration settings,
//    for this application ?? change FreeRTOSConfig.h and re-build the 
//    FreeRTOS + our application - test and verify ??
//
     

//assignment problem 6:
//     --->this is same, as problem 2 - only change is re-entrancy 
//           write a single reentrant task method and 
//           pass appropriate task parameters - 
//           in the above problems, write a single re-entrant task method 
//           and use it , for all the tasks
//
//         - task methods can be different or same, based on the respective
//           jobs 
//         - if a task methods are same, they must be reentrant - the jobs 
//           are similar, but hw involved may different, for different 
//           jobs/tasks - for instance, same type of sensor, but different 
//          instances of the sensor - same peripheral, but different 
//          instances - different UART instances   
//
//        - on the other hand, if we are dealing with different jobs, 
//          in different tasks,we need to write different task methods  
//--->you must select the most appropriate scheduler configuration settings,
//    for this application ?? change FreeRTOSConfig.h and re-build the 
//    FreeRTOS + our application - test and verify ??
//


//03.03.2022...first-break ....15 minutes....

//assignment problem 7:
//           - create 2 periodic tasks 
             - one periodic task will do the following : 
                  - flash Blue LED, for a certain duration of the 
                    task period of this task and switch-off, during 
                    the remaining duration of the task period 
                  - for this task, Green LED will be ON, during the 
                    flashing duration and switched-off , during the 
                    remaining duration of the task period 
---->in the first task, we have a period of 10 seconds 
     --->in this first task, there is a 6 seconds duration of 
         a simulated job - meaning, in each period, there is a job 
         of 6seconds execution - so, the job duration is signified, 
         by Green LED - the job is signified, by a flashing blue LED - 
         this blue LED must flash, at 1 hz period - blue LED will 
         be on, for 0.5 seconds and OFF, for 0.5 seconds - this will 
         be, for 6 seconds, in every period of first task
     --->once the 6 seconds is over, in a period, al the LEDs are
         switched-off and vTaskDelayUntil() is invoked, again   
         
----->similarly, do the following, in a second task - it signifies
      another job, but timing is the same ....
              - in another periodic task, flash orange LED and use 
                RED LED , for showing the active duration of the task 
                period - characteristics are similar the first task, 
                but LEDs are different 
              - period of tasks will be 10 seconds 
              - flashing duration will be 6 seconds and flashing 
                rate will be 1 Hz
              - use equal priority, for tasks -      model | test | verify 
              - use unequal priorities, for tasks -  model | test | verify
//--->you must select the most appropriate scheduler configuration settings,
//    for this application ?? change FreeRTOSConfig.h and re-build the 
//    FreeRTOS + our application - test and verify ??
//
 
  
//hints for the above problem
//
//first, test, for one set of LEDs(blue/green), in a single task/only task 
//
//second, the period of the task will be 10 seconds, so use vTaskDelayUntil()
//
//third, the active time of the job will be 6 seconds 
//
//fourth, in the 6 seconds of active job, toggle blue LED, at the rate 
//of 1 second - for this, we can use vTaskDelay(500), or you can use HAL_Delay()
//for flashing, if your task/job requires it 
//
//
//if you have tested the above, now add green led - switch-on the green 
//LED before toggling and at the end of the toggling, switch-off the green LED
// 
//
void vTaskHeartBeat( void *pvParameters )
{
char *pcTaskName;
portTickType xLastWakeTime;

    pcTaskName = ( char * ) pvParameters;

	xLastWakeTime = xTaskGetTickCount();

	/* As per most tasks, this task is implemented in an infinite loop. */
	for( ;; )
	{
		/* Print out the name of this task. */
		//vPrintString( pcTaskName );

		/* We want this task to execute exactly every 250 milliseconds.  As per
		the vTaskDelay() function, time delay is measured, in ticks ???*/

		//lecture - we need to explore this API, using reference manual,
		//          user-manual and source-code

                prvToggleLED();
		vTaskDelay(250);
                //vTaskDelayUntil(&xLastWakeTime, p2);//p2 will be the period of the task 
	}
}
//
//---->refer to slides 39,45-46,52-58 and 60   of  17_desd_rtos_March2023.pdf
// --->periodic-jobs are well illustrated and different
//       real-time parameters are highlighted
//---->also, refer to 4_rtos_internals_ftros.pdf/ slides 1-4
//
//
//for Task1, 1 represents systick-events
//for Task1, 2 represents periodic, task-release times
//
//for Task2, 1 represents systick-events
//fpr Task2, 2' represents periodic, task-release times
//
//
//for assignment-4, we need to capture time-stamps, at 
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
