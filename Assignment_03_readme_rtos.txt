//the focus of this assignment is rtos tasks, concurrency, shared-data, shared-data race-conditions, 
//and mechanisms used to prevent shared-data race-conditions, using rtos mechanisms....
//
//most of the basics are borrowed from basic OS concepts, but rtos platform and its mechanisms are
//implemented differently compared to GPOS mechanisms.... 
//
//  
//--->problem 1 :  using concurrent tasks, generate a shared-data race-condition and 
                   illustrate practically

            ---->create two tasks, as described below :
                 --->there is a shared-data, which is shared_flag with a 
                     default value 1 - if it is 1, this shared-flag is 
                     unused - if it is 0, the shared-flag is in use - 
                     initialized to 1 
                 --->task 1 with scheduling priority 1(lower importance) and period 1 sec
                 --->use vTaskDelayUntil() 
                 --->job should be setting LED3/job-entry and checking a shared-flag - 
                     shared among all the tasks/task-methods 
                     --->if the shared-flag is 1, decrement it to 0 and
                         simulate a shared-data job using HAL_Delay() - spin for sometime
                         - we may use a LED, as well to notify, if there is a 
                           clash  
                     --->end of the job, set shared-flag to 1 - this is a
                         typical, shared-data problem...
                 --->if we find the shared-flag is set to 0, it means, there is 
                     a contention and we should set LED5, red-led, as a notification
                     --->still, simulate shared-data job, using HAL_Delay()
                 ---->at the end of the job, reset all LEDs - job-entry LED and 
                      shared-data contention, LED 
                 --->task 2 is created with priority 2(higher importance) and period 200msecs
                      ---->LED6 must be set , instead of LED3 - LED6 is the 
                           job-entry LED ...
                      ---->otherwise, coding is similar to task 1 - repeat all the above steps 
            ----->refer to code sample, ex5_main.c provided 
                  ---->do not just copy the code - understand and use it
                  ---->you can use one of the existing projects and 
                       modify its main.c , using code from ex5_main.c...  
also,                        

set #define configUSE_PREEMPTION                   1 ??  //as per above problem-1's requirements 
set #define configUSE_TIME_SLICING                 0 ??  //as per above problem-1's requirements 


//---->problem 2 : 

          ----->make a copy of the above project of problem 1 and make the following 
                changes :

                ---->add interrupt lock to shared-data regions, like 
                     shown in ex6.c - do not copy the code - just use it as a reference/sample code 
                ---->what is your observation....?? explain clearly ??
                    --->what is the effect of  taskENTER_CRITICAL() and taskEXIT_CRITCAL()
                        on critical-sections/shared-data access , in task1 and task2 ??
                    --->explain the effects on RTOS interrupts/exceptions  and tasks ??  



//---->problem 3 :  
       --->problem 2 + another higher-priority task3 ??
--->first-break on 07.03.2022....
          ----->make a copy of the above project of problem 2 and make the following 
                changes : refer to ex7.c - use this as a reference /sample  

                ----> now, add one more task, task 3 with priority 3 and 
                      period of 50 msecs....this is unrelated to task 1 and task 2

                ---->test with interrupt locks , in task 1 and task 2 
                     -->what do you observe ??
            scenario1--->if we use HAL_Delay(50), in Task3, Task3 will execute
                         without much problems, since it is non-blocking and 
                         cannot be preempted - however, Task1 and Task2 are
                         starved - so, the design is unacceptable 
            scenario2--->if we use vTaskDelay(50), in Task3, Task3 will execute
                         , periodically, but block often - so, Task1 and Task2 will
                         be scheduled/dispatched - however, taskENTER_CRITICAL()
                         /task_EXIT_CRITICAL()  will disable certain hw ints/
                         exceptions, so preemption points/scheduler are disabled - 
                         so, Task3's scheduling/dispatching will be affected - 
                         we can observe this behaviour - however, we need to 
                         use certain tools and techniques to find the latencies
                         involved, to prove the actual, timing non-determinism 


                ---->test by commenting interrupt locks, in task 1 and task 2 
                     ---->what do you observe ?? fill the details 

                ----> can you explain the behaviour, in the above cases ??
                      --->refer to the above comments and fill the details 

            --->so, in the scenario, how do we solve all the issues and 
                schedule/dispatch and execute the jobs of all the tasks, 
                as per our requirements ?? functional , as well as 
                timing requirements   ??
                --->functional-requirements :
                    --->we need to create and manage two tasks 
                    --->task1 is of lower-importance and task2 is of higher-importance
                    --->there is a shared-data used 
                    --->we should protect this shared-data access, using 
                        a suitable lock 
                    --->task3 is periodic and assigned highest-priority
               ---->timing-requirements :
                    --->task1 is periodic, using a certain period ??
                    --->task2 is periodic, using a certain period ??
                    --->task3 is periodic, using a certain period ??
----->so, the above functional and timing requirements must be 
      satisfied - we need to code, test, and validate ??
 


//----->problem 4 : 

          ---->make  a copy of the above project of problem 3 and make the 
              following changes :   

              ---->"remove interrupt locks",  in task 1 and task 2 

              --->instead, "use a binary semaphore" and test - what do 
                  you observe ??

          ---->in the case of this rtos platform, there is a strict, binary-semaphore
          ---->it can accept 0 or 1, only - we cannot increment this binary-sempahore
               beyond 1 
          ---->initial value of a binary-semaphore is 0 and we need to explicitly, 
               do a xSemaphoreGive() to initialize the value to 1, if needed
          ---->since a xSemaphoreTake() will decrement a binary-semaphore, 
               atomically, but will not disable hw ints./exceptions - 
               similarly, xSemaphoreGive() will increment a binary-semaphore, 
               atomically 
          ---->when we use xSemaphoreTake() and xSemaphoreGive(), to protect Task1's 
               and Task2's critical sections, only task1 and task2 are affected, 
               according to their concurrent executions 
          ---->so, Task3 will be unaffected and scheduled/dispatched, as per 
               its priority and periodicity 
          ---->we can use tools and techniques to measure and validate the 
               timing performance of the above tasks 
        --->refer to 3_rtos_updated_sep2021.txt 
        --->refer to FreeRTOS reference manual of >=v.9.0.0  

---->based on the above problems and scenarios, we understand that, interrupt-locking
     disabled interrupts/preemptions, for unreasonably, long durations 
--->if we use semaphores, or mutexes, interrupt-locking/preemptions are disabled, 
    for minimal durations, in OS-code/kernel-paths - however, locking of 
    critical-sections is done, effectively 
--->if you test the design and coding of problem 4, you will see the 
    results, as expected ?? 
--->so, if we are protecting short critical-sections,we will use interrupt-locking and
    if we are protecting long critical-sections, we will use semaphore/mutex 
 


//------>problem 5 :

          ---->make  a copy of the above project of problem 4 and make the 
              following changes :   

              ---->remove interrupt locks in task 1 and task 2 

              --->instead, use a mutex-semaphore and test - what do 
                  you observe ??
   ---->first, refer to MasteringFreeRTOS tutorial....
             --->section 7.3 ....must read 
             --->similar to GPOS/thread mutex, but certain details will differ
   ---->next, read 3_rtos_updated_sep2021.txt - search, for mutex and 
       complete all the sections

            ---->so, should we use a mutex-semaphore, in the place 
                 of a binary-semaphore ?? YES - it is a  
                 robust-lock, in a typical rtos-platform 
                 --->implicit error-checking of operations
                     --->for instance,in the case of 
                         a binary-semaphore if there are two xSemaphoreTake() 
                         calls, in a task, the second xSemaphoreTake() 
                         will not return error, but block the current 
                         task, forever - so, it will also, block the 
                         other task ,forever....problems are serious and 
                         trouble-shooting is tricky ??  
                     --->for instance,in the case of 
                         a mutex-semaphore, if there are two xSemaphoreTake() 
                         calls, in a task, the second xSemaphoreTake() 
                         will return error, so 
                         trouble-shooting is straight forward  ?? we can 
                         easily capture the coding-bugs ?? 


--->lunch break on 07.03.2022.....resuming at 2.40 pm

--->now, we need to switch to 3_rtos_updated_2022.txt and 
    study and understand priority-inversion problems and
    related priority-inheritance solutions ???
 --->refer to following section and complete....
  39....revised on 07.03.2022.....
  15365 --->Real-time perspective - 7

 --->priority-inheritance protocol is enabled  
       --->implement and test the code ?? what do you observe ??
 
--->third-break on 07.03.2022.... 


//------>problem 6 :

       ---->    create two tasks that will do the following :
--->these two tasks are non-periodic - we wish to illustrate specific 
    scenarios .... 
      
                      --→ access a shared data region – say, accessing a common/shared LED
                          --->in this case, we are accessing a shared-resource, which 
                              is a LED 
                      ---→ take/decrement a binary-semaphore instance - entering a critical-section
                         ----→ flash a shared LED(orange) ,for 2 seconds, at 5 HZ rate 
                      -----→ increment/ give semaphore instance - exiting a critical-section

                   ------→after the above code-section, flash another specific,  LED,
                           for 5 seconds, at 5 Hz and 
                          suspend the task using suspend system-API - vTaskSuspend() 

                    ----→one of the above tasks will be assigned a scheduling priority of 2
                                ---->this is Task1
                                     --->it will be assigned a different LED, during 5 seconds
                                         flashing 

                   -----→another task will be assigned a scheduling priority of  3
                                ---->this is Task2
                                     --->it will be assigned a different LED, during 5 seconds
                                         flashing 
 
         

      ------->  create another task  with a priority of 5 
                                 ----->this is Task3

         ------→this task will be used to flash another specific LED for 5 seconds
                     at the rate of 10HZ 
         -------→after the above job, suspend using suspend API - vTaskSuspend()  

--->scenario-1---->in task1 and task2, initially, use vTaskDelay(), for flashing LEDs
--->scenario-2---->next, in task1 and task2, use HAL_Delay(), for flashing LEDs 
         --->in this scenario, we want to actually, simulate busy-jobs,
             using HAL_Delay() ?? so, we prefer HAL_Delay() over vTaskDelay() 
 

      -------> first, just create the above tasks, schedule and test them – what do you 
               observe – note down your observations….


//problem 7 

          ----->in the above problem 6, you can replace semaphore lock with a mutex lock 
               ----->what are your observations

              ---->similar to earlier arguments, a mutex-lock is more robust than,
                   a binary-semaphore lock
 


//problem 8 

         now, do the following changes to the problem 6 and test again :

       ------→we need to block  the Task2, in problem 6 using vTaskDelay(1000); , 
              initially, once 
         ---→similarly for the Task3,   using vTaskDelay(1000); once, initially

         ---->use semaphore lock, as in problem 6 

     ----→now, rebuild and test the above set-up – what do you observe ??

----->in this scenario, following will be true :

          --->task1 will be scheduled and dispatched, first 
          --->task2 and task3 will be scheduled and dispatched, after 1000msecs/1sec 
          --->now, what happens, when task1 is scheduled/dispatched and executed ??
              ---->it will dec. the binary-semaphore and enter its critical-section 
              ---->after 1000msecs/1sec, task2 and task3 will be added to respective
                   Rqs - so, there will be a preemption - so, task 3 will be 
                   scheduled/dispatched - so, task1 will be preempted 
              ---->after task3 completes its job and suspends, task2 will attempt to access
                   the binary-semaphore of the critical section - it will be 
                   blocked 
              --->now, task1 will be scheduled/dispatched and resumed 
              --->it will complete the critical section and task2 will be 
                  unblocked, when binary-semaphore is released 
              --->there will be a preemption and task2 will complete its 
                  critical-section and its other jobs 
              --->when task2 suspends, task1 will be scheduled/dispatched and 
                  executed - it will resume, after its critical section and
                  complete its job, and suspends ....
------>based on the above scenario and concurrent executions, task 1 is the most 
       affected task - task 3 is the least affected task - so, the response time
       of job of task 1 will the most affected, due to preemptions 
       --->in addition, release time of task 2 is affected, due to task 1 and task 3
       --->in addition, response time of task 2 is affected, due to task 1 and 
           task 3 - task 1 affects task 2, due to critical-section - task 3 
           affects task 2 , due to higher-importance 
     --->actually, in every design, we need to do a thorough analysis of 
         such problems, and test and measure the latencies - based on the 
         latencies, we can validate real-time performance of tasks of jobs of
         our application 
     --->so, we need to do the above analysis, for every task and complete
         the testing, measurements, and validation....



//problem 9 

        what happens, if we replace semaphore lock with a mutex lock ??
              -----→explain your observation and justify…..
      ---->fill details...


//problem 10 


       --->what happens, if we change the following, in problem 8 and problem 9

             --->change the priorities of tasks, as below :

                   --->Task1  ---->  2
                   --->Task2  ---->  5
                   --->Task3  ---->  3
 
   ---->now,repeat problem 8, with the changes :

          --->Task1 will be first scheduled/dispatched and executed 
          --->after 1000msecs/1sec, Task2 will attempt to access the binary-semaphore and 
              will be blocked 
          --->after 1000msecs/1sec, Task3 will be scheduled/dispatched and executed
          --->after this, task1 will scheduled/dispatched and it will complete
              its critical section and release the binary-semaphore
          ---->so, task2 will be unblocked , as well as scheduled/dispatched
          ---->continue and fill the details....


   ---->now,repeat problem 9, with the changes :

          --->Task1 will be first scheduled/dispatched and executed 
          --->after 1000msecs/1sec, Task2 will attempt to access the mutex-lock and
              will be blocked - when task2 is blocked on a mutex-lock, the owner 
              of this mutex-lock is assigned task2's priority, which is 5 - this is 
              part of priority-inheritance protocol of a mutex-lock - this is 
              done, in the background  
          --->after 1000msecs/1sec, Task3 will be NOT BE scheduled/dispatched and executed
          --->so, task1 will be scheduled/dispatched and executed, again 
          --->task1 will complete
              its critical section and release the binary-semaphore
          ---->so, task2 will be unblocked , as well as task1's priority is 
               reset to its original priority, 2, and task 2 will be scheduled/dispatched
          ---->continue and fill the details....
  ---->in this scenario, where task1 and task2 are using a shared-resource and 
       a mutex-lock, priority-inheritance is effective, since task 3 is of 
       intermediate priority and is disallowed to execute, while task 1
       is executing, holding the mutex, and task 2 is blocked on the mutex - 
       so, effectively, task 2's priority is passed to task 1, as long 
       as it is holding the mutex and task 2 is blocked on the mutex 
       ---->one, refer to section 7.3 of MasterFreeRTOS tutorial 
       ---->also, refer to rt_e_concepts.pdf 
              ---->chapter 5 - last page 
              ---->section 16.4 - the diagrams are really good  
              ---->you need to understand priority inversion, first 
                   and next, priority inheritance protocol
              ---->also, refer to 3_rtos_updated_sep2022.txt - refer to priority inheritance 
       ------>refer to Figure 16.6
                 ---->initially, low-priority task acquired a mutex-lock 
                      and enters a critical-section and uses a shared-resource
                 ---->while task1 is , in the middle of its critical-section, 
                      higher-priority task2 will pre-empt and attempt to 
                      acquire the same mutex-lock and access the same shared-resource
                  --->however, task2 will be blocked and task 1 is resumed, again 
                  --->task1 will complete its critical section and release mutex-lock 
                  --->task2 will  be unblocked, and scheduled/dispatched and executed
                  --->task2 will acquire the mutex-lock and complete its job 
               --->in this scenario, task 2's response is increased, due to 
                   added latency of priority-inversion problem - this latency 
                   is known as priority-inversion latency 
        ------>refer to Figure 16.7
                ---->all the above points of 16.6 apply 
                ---->in addition, while Task2 is blocked on mutex-lock 
                     and Task1 is resumed/executing, unrelated, intermediate-
                     priority tasks can preempt task1 - due to this, task1
                     will finish/complete its critical-section, after a 
                     longer-latency - meaning, after intermediate-priority 
                     tasks complete, task1 is resumed - task1 will 
                     complete its critical-section and unlock mutex-lock
                ---->now, task2 will be unblocked, and scheduled/dispatched 
                     and executed 
                ---->task2 will acquire mutex-lock, access shared-resource
                     and complete its job 
                --->however, response-time of task2 is increased , due to 
                    latencies added, by intermediate-priority tasks 
               --->such latencies may be unacceptable 

      --->however, if mutex-lock/mutex-lock operations implement 
          priority-inheritance protocol, intermediate-priority tasks
          cannot preempt task1, so these additional latencies 
          can be avoided 

    ---->now, refer to Figure 16.8 
         --->as mentioned, in 16.6 and 16.7, all the points apply
         --->however, when task1(low-priority) is holding a 
             mutex-lock and is preempted, by task2(high-priority) and
             task2 is blocking on mutex-lock, task1's run-time priority
             is temporarily, increased to that of task2's priority - this is 
             part of priority-inheritance protocol
         --->so, once task2 blocks and task1 resumes execution, task1 
             cannot be preempted and delayed, by intermediate-priority 
             tasks - so, there will be no additional latencies, due to 
             intermediate-priority tasks
         --->when task1 completes its critical-section and releases
             mutex-lock, task1's run-time priority is reset to its 
             original priority - so, task2 is unblocked and 
             immediately scheduled/dispatched 
          --->when task2 completes its job, it will be blocked(a different 
              blocking), and task1 and other intermediate-priority tasks
              may be scheduled/dispatched


 
  
           

 
  

 

