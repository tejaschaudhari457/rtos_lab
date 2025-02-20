
//lecture:
  //in this assignment, we are creating a switch scanning/debouncing task, which 
  //will sample the switch states and decide a switch press, after the switch 
  //bouncing effect settles - this will require the task to sample the switch states, 
  //periodically(period and number of samples depend on the switch / bouncing time) -
  //typically, a time-triggered task and a real-time job/task - 
  //once this real-time task has detected a switch press, the event can be notified, using 
  //a semaphore or a task notification, or a message queue, as per the requirements 
  //of the application ??
  

  //a design-scenario 
  //
  //producer-task will be a periodic-task/time-triggered, which will sample switch-state and
  //if there is a switch press, increment a counting semaphore
  //we will be using vTaskDelay() or vTaskDelayUntil() - systick + sw-timer are used to do 
  //time-triggering - we sample switch-press, in our periodic-task/time-triggered 
  //
  // 
  //initially, use a counting semaphore - increment this counting semaphore, 
  //when there is a true switch-press event, in the producer-task - 
  //a consumer-task will block and wait, for 
  //this counting-semaphore to be incremented, by the producer, when it detects 
  //a switch press - consumer-task will toggle an LED, whenever the counting 
  //semaphore is incremented, due to a switch-press event - consumer task will 
  //be a sporadic/aperiodic task waiting, for a switch-press event communicated, 
  //using a counting-semaphore's synchronization  


//--->refer to 1_rtos_queries_aug2019.txt
       ---->query no.38 provides certain hints 
            for switch polling and debouncing...
    
//--->we need to enable counting semaphores, using configUSE_COUNTING_SEMAPHORES 
      in FreeRTOSConfig.h of our project set-up and rebuild - this will enable 
      the frame-work of counting semaphores and we can use related system apis
      and objects - we need to explicitly, enable this feature....

//---->regarding switch-debounce techniques, you can refer to 
       eres1_realtime.pdf/section 4.16 / page 108

----->the following reference and techniques are used, in our code-samples
----->refer to code-samples,14_leds_switches_sem.c and 15_leds_switches_queue.c
      --->do not copy the code samples, but take the design and coding-logic
          , and add it to our own project-sample   
//---->regarding switch-debounce techniques, you can refer to 
       programming embedded systems.pdf/section 11.4 / page 215
       ---->there is a debouncing algorithm used 
             --->sampling period of switch is 10 msecs
                ---->no. of samples is 1(high) + 12(lows)
                  ---->the objective is to spot one high followed by 
                       12 lows and if covers a longer duration, than
                       bouncing time of the leading edge of the switch-press
     ---->assuming the above points are clear, have you tested with 
          a sampling period of 10 msecs and 1(high) + 12(lows) and does it 
          work ??


//----->in the current assignment, first ensure, that our switch-sampling-debouncing
//      task works, correctly - meaning, producer-task works, correctly - 
//      once the producer task works, correctly, we can add consumer task and
//      test its behaviour ?? 
//
//   ---->you can refer to the following assignment problems, as you code and solve the
//        producer-task method ??


//assignment problem 1:
//--->initially, modify your main_switch.c to suit your 
//    board - test the pinstate, using debug break-point -
//    based on the default pin-state, you can modify your 
//    debounce-logic, as mentioned, in the producer-code  
//
//
//
//        Modify the switch polling period to different values and check the
//        behaviour. the default is 1ms - change it to 2, 3, 4ms and check
//        the behaviour.Explain the behaviour , based on your understanding ???
//
//--->use different timing requirements and check, if no. of samples and
//    sampling period, and debouncing-time(sum of the time-taken to 
//    collect samples) is really, affecting reading switch-presses ?? 
//
//    --->change the sampling-rate to a different number, like 4-msecs/5-msecs/3-msecs/2-msecs/10-msecs - 
//          try  to press the switch, fast enough....are you able to 
//          capture every event, or missing some events ??
//          --->if you are missing events, our timing requirements are
                unsatisfied - so, unreliable 
            --->find the highest sampling-period, that will not miss
                the events, so provide reliable event counting ??  
//
//
//
//assignment problem 2:
//        Modify the above implementation, by replacing the counting semaphore, 
//        with a message queue - implement the changes, test, and verify ???
//
//--->basics remain the same, but a different IPC is being used 
// 

//assignment problem 3:
//        in the modified implementation, using message queue, 
//        do further modification to toggle a different LED, 
//        for each successive key press - you can handle 
//        4 LEDs, which are available on the board - 
//        implement the changes,test, and verify ???
//--->a modified form of problem-2 - understand the queue's message-passing 
//



//assignment problem 4:
//        change the intensity of an LED, for each press - initially, 
//        it should increase and reach the max. intensity - next, 
//        it should decrease, for further switch press events - 
//        this cycle can be continued, for further switch presses ??

//        this a classical example, for a multi-state task -
//        the behaviour of the task depends on the current value of the 
//        duty cycle - usage of the queue is different, as per the 
//        current value of the duty cycle 
    
//        build | test | verify the behaviour of the task, for 
//        different duty cycle values, by controlling the switch task/switch 
//
//--->this will need a more elaborate consumer-task code - there are
//    some coding issues involved ?? refer to the code-samples below 
//  

//you can create a simple task as follows :
   --->this task will be a stand alone task
   --->it will be periodic 
   --->it will switch-on a given LED for 50% of the period of the task
   --->it will switch-off a given LED for 50% of the period """

       while(1) {
       {

          --->set the LED 
          --->vTaskDelay(250);
          --->reset the LED
          --->vTaskDelayUntil(500);
       }

---->once the above basic code is tested, you 
     can receive data from a queue and set the 
     ON time - we need to pass a variable to 
     vTaskDelay(dutycycle); 
---->this dutycycle must be set based on data received
     while(1) 
     {
         ---->non-blocking queue receive api 
         --->based on data received, dutycycle variable is set
           --->set the LED 
          --->vTaskDelay(dutycycle);
          --->reset the LED
          --->vTaskDelayUntil(500);
       }


      



