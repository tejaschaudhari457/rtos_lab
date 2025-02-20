 //
 //lecture - refer to FreeRTOSConfig.h and FreeRTOS manuals, as needed
 //
 //lecture - refer to lecture notes, for details of sw clock,
 //          sw timers, vTaskDelay(), and 
 //          vTaskDelayUntil() - read and understand, before proceeding 
//   ------>refer to line nos. ?? - ??   , in 3_rtos_updated_sep2022.txt
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
//--->set-up Tim2, as a 32-bit, up-counting timer - set the
//    prescale to 0 and divisor to 1 - otherwise, the 
//    default initialization is good-enough
//
//
//


 //
 //assignment- problem 1:
 //
 //           
//    
// --->use HAL_Delay(100); , in main() of your code, before 
//     vTaskStartScheduler() 
//
//---->refer to main2.c and measure the time-stamps and interpret 
//     actual delays/latencies ??
//     


 //
 //assignment- problem 2:
 //
 //           
//    
//
//---->refer to main2.c and measure the time-stamps and interpret 
//     actual delays/latencies of vTaskDelay(100)  ??
//     
//--->you need to first, use the code-block, before for() loop, in 
//    Task1 and measure actual delay/latencies of 
//    vTaskDelay(100) 
//
//--->next, you can use the code-block at the end of the loop, 
//    as per Task1's code - uncomment and test the actual 
//    delay/latencies
//
//--->compare the above two results and provide reasoning ??
//
//


//assignment - problem-3 
//
//refer to main3.c 
//
//
//-->first, enable only, Task1, not Task2 and Task3 - 
//   capture and print periodic-task release-times 
//--->find differences between consecutive time-stamps and 
//    calculate the actual periods/latencies ?? justify, if 
//    possible - provide reasoning ??
//
// 
//

//
//
//assignment - problem-4 
//
//refer to main3.c 
//
//
//--> enable  all the tasks, Task1, Task2, and Task3
//
//
//--->capture and print periodic-task release-times 
//--->find differences between consecutive time-stamps and 
//    calculate the actual periods/latencies ?? justify, if 
//    possible - provide reasoning ??
//
//--->do you find any other issues ?? provide reasoning ??
//


//
//
//assignment - problem-5 
//
//refer to main4.c
//
//
//--> enable  all the tasks, Task1, Task2, and Task3
//
//
//--->capture and print periodic-task release-times   
//--->find differences between consecutive time-stamps and
//    calculate the actual periods/latencies ?? justify, if
//    possible - provide reasoning ??
//
//--->do you find any other issues ?? provide reasoning ??
//--->why do we need to use a mutex-lock, in the code-sample - 
//    explain ??
//
//--->what happens, if we assign different task-priorities to 
//    tasks, in this set-up ??
//--->capture and print periodic-task release-times
//--->find the differences between consecutive time-stamps of 
//    each task - calculate the actual periods and latencies ?? 
//    justify and provide reasoning ??
//
//--->does task-priority influence task-release times of a task ??
//    justify and provide reasoning ??
//
//
//

//
//--->assignment - problem-6
//
//
//
//once the above problems, in this assignment are completed, 
//you can attempt problem-7 of assignment-2  
//
//--->however, there are design problems, in problem-7 of 
//    assignment-2 
//    --->try to spot the problems ??
//    --->once you spot the problems, try to find solutions
//        , like changing the design of tasks' jobs
//
//
