# ese5190-lab2b-Part2
## Still confusing about how to set up successfully, this are my experiment code:
```
#include "pico/stdlib.h"
#include <stdio.h>
#include <stdlib.h>
#include <math.h>
#include "registers.h"

volatile uint32_t * register_address;
uint32_t full_gpio_register_value;
uint32_t pin_21_selection_mask;
uint32_t select_pin_state;
uint32_t shifted_pin_21_state;

typedef struct {
    uint32_t target_address;
    uint32_t value;
    uint32_t write;
    char address_ch[8];
    char value_ch[8];
    bool rORw;
} Repl; 

void render_to_console(Repl status) {
    // adjust console window height to match 'frame size'
    for (uint8_t i=0; i<10; i++) { 
        printf("\n");
    }
    
    if (status.rORw){
        printf("Read Mode");
    }else{
        printf("Write Mode");
    }
    printf("Current register:  0x%08x\n",   status.target_address);
    printf("Register value:    0x%08x\n",   status.value); 
}  

uint32_t get_uint32_from_string(char charArray[]){
    int p = 0;
    int power = 7;
    uint32_t result = 0x00000000;
    uint32_t currentBitNumber = 0x0;
    int currentnumber = 0;
    while(p < 8){
        if(charArray[p]){
            if(charArray[p] >= '0' && charArray[p] <= '9'){
                currentnumber = (charArray[p] - '0');
            }else{
                currentnumber = (charArray[p] - 'A') + 10;
            }
            currentBitNumber = (uint32_t)currentnumber * (pow(0x10, power));
            result = result + currentBitNumber;
            p++;
            power--;
        }
    }
    return result;
}

int main() {
    // initialize the register status
    Repl status;
    status.target_address =  0x00000000;
    status.value = 0x00000000;
    status.write = 0x00000000;
    status.rORw = 'R';

    char input;
    int counter = 0;
    
    while (true) {
        // read the mode
        printf("Please enter the mode:\n");
        input = getchar_timeout_us(0);
        if (input == 'R' || input == 'r'){status.rORw = true;}
        else if (input == 'W' || input == 'w'){status.rORw = false;}
        else {break;}

        // read the register address
        printf("Please enter the register adddress:\n");
        while(counter < 8){
            input = getchar_timeout_us(0);
            // read the address of register
            if ((input <= '9' && input >= '0') || (input >= 'A' && input <= 'F')){
                status.address_ch[counter] = input;
                counter ++; 
                if (counter == 8){printf("Address finished!\n");}
            } else { printf("Invalid input, enter again!\nPlease enter the register address:"); break;}
        status.target_address = get_uint32_from_string(status.address_ch);

        // read mode
        if (status.rORw){
            volatile uint32_t *register_address;
            register_address = (volatile uint32_t *) status.target_address;
            status.value = register_read(register_address);
            printf("Register value:    0x%08x\n",   status.value); }
        
        // write mode
        else{
            printf("Please enter the value:\n");
            while(counter < 8){
                input = getchar_timeout_us(0);
                // read the address of register
                if ((input <= '9' && input >= '0') || (input >= 'A' && input <= 'F')){
                    status.address_ch[counter] = input;
                    counter ++; 
                if (counter == 8){printf("Value finished!\n");}
                } else { printf("Invalid input, enter again!\nPlease enter the value:"); break;}
            }
            status.value = get_uint32_from_string(status.value_ch);

            volatile uint32_t *register_address;
            register_address = (volatile uint32_t *) status.target_address;
            register_write(register_address, status.value);
            printf("Register value:    0x%08x\n",   status.value);
        }
        render_to_console(status);
        sleep_ms(1000); // don't DDOS the serial console
    }}}  
    
    ```
