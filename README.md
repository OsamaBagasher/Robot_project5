//working we showed steven at the end of sem1
// Ahmed Babader        ID/ 13001782
#include "mbed.h"


//BusOut stepPins(PC_0, PC_1, PC_2, PC_3);
DigitalIn powerbutton(PC_13), usEcho(PB_5);
DigitalOut in_1(PB_10), in_2(PA_8), in_3(PA_9), in_4(PC_7), green(PA_5), red(PB_9);

PwmOut pwm1_2(PB_4), pwm3_4(PB_6), usTrig(PB_3);

Timer A;

int echo(void);


class Motors {
public:
    Motors(void): poweroff(1), clockwise(1) {}        // delay(10) {}       // dont need the delay varible it was use to change
                                                                // the speed of the stepper but we will use PMW for DC 
    void tick(void);
    
    void forward(void);
    void backward(void);
    void left(void);
    void right(void);
    
//    void set_delay(int d) {delay = d;}
    void power(void) {
        if(poweroff == 0) {             // use user push button
            poweroff = 1;
        }else {
            poweroff = 0;
        }
    }
    void direction(void) {
        if (clockwise == 1) {
            clockwise = 0;
        } else {
            clockwise = 1;
        }
    }
    

    
private:
    int poweroff;
    int clockwise;
//    int right;    
};


int main()
{
    pwm1_2.period_us(20000);
    pwm3_4.period_us(20000);
    usTrig.period_us(65535);
    
    usTrig.pulsewidth_us(10);
    
    
//    Motors sm;
    
    Motors All_motors;                      // later when we get another motor drive control each motor seprately
    
    int obstical_range;    
    
    Ticker t;
    t.attach_us(&All_motors, &Motors::tick, 100000);
    
    
    while (1) {
//        char s[80];
//        int n;
//        gets(s);
//        puts(s);

        obstical_range = echo();
        
        if (!powerbutton.read()) {  // user button to toggle power insead
            All_motors.power(); 
            wait(.2);    
        }
        
        if(obstical_range <= 30) {
            red.write(1);
            All_motors.direction();
        } else {
            red.write(0);
        }
          
/*        } else if (s[0] == 'D') {     // use it to change PWM i.e. speed
            sscanf(s+1, "%d", &n);
            if ((n > 3) && (n < 21)) {
                All_motors.set_delay(n);
            }*/
        
    }
}


int echo(void){         // the while in this function may cause the robot to stop if the echo is not reading
    int distance;
    while (! usEcho.read()); //wait for us_echo to go high
    A.start();
    while (usEcho.read()); //wait for echo to go low
    A.stop();
//    printf("%d\n", A.read_us());
//    wait_us(100000);       // add if does not work
    distance = A.read_us() / 58;
    A.reset();
    return distance;         // if does not return correctly put t.read_us() into integer varible  
}




void Motors::tick(void) {
//    static int counter = 0, shift = 0;              // dont need shift and counter
    if (poweroff){            //&& ((counter % delay) == 0)) {     // keep the poweroff varible
        if (clockwise) {
            forward();           
           
           // stepPins.write(0x1 << (shift % 4));
/*            in_1.write(0); 
            in_2.write(1);
            in_3.write(1);
            in_4.write(0);  
*/

        }else{
            left();
            
           // stepPins.write(0x8 >> (shift % 4));
/*            in_1.write(1); 
            in_2.write(0);
            in_3.write(0);
            in_4.write(1);
*/
        }
//        shift++;
    } else if (poweroff == 0) {     // keep the poweroff varible
        //stepPins.write(0);
        in_1.write(0); 
        in_2.write(0);
        in_3.write(0);
        in_4.write(0);
    }
//    counter++;
} 


void Motors::forward(void) {
    pwm1_2.pulsewidth_us(8000);
    pwm3_4.pulsewidth_us(8000);
    in_1.write(1); 
    in_2.write(0);
    in_3.write(0);
    in_4.write(1); 
}

void Motors::backward(void) {
    left();
    in_1.write(0); 
    in_2.write(1);
    in_3.write(1);
    in_4.write(0);
    wait(2);
    clockwise = 1;
    
}

void Motors::left(void) {
    pwm1_2.pulsewidth_us(15000);
    pwm3_4.pulsewidth_us(15000);
    green.write(1);
    in_1.write(0); 
    in_2.write(1);
    in_3.write(0);
    in_4.write(1); 
    wait(0.5);
    green.write(0);
    clockwise = 1;
}

void Motors::right(void) {
    in_1.write(1); 
    in_2.write(0);
    in_3.write(0);
    in_4.write(0); 
}
