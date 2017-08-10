#include "mbed.h"
PwmOut servo(PB_0);
DigitalIn button(PC_13);

int main(){
    int direction=0, pw=2000;
    servo.period_us(20000);
    while (button.read());
    
        while (1){
            servo.pulsewidth_us(pw);
            wait_us(40000);
            if (direction == 0){
                pw+=20;
                if (pw== 2020){
                    direction=1;
                    pw=2000;
                    }
            }else{
                pw-=20;
                if (pw==980){
                direction =0;
                pw=1000;
                }
                }
            
            }
    }

