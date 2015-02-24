rpi_code
========

#!/usr/bin/python
#
# HD44780 20x4 LCD Script for Raspberry Pi
# Author : Talha Chiklikar
# Date   : 23/10/2014
#

# The wiring for the LCD is as follows:
# 1 : GND
# 2 : 5V
# 3 : Contrast (0-5V)*
# 4 : RS (Register Select)
# 5 : R/W (Read Write)       - GROUND THIS PIN
# 6 : Enable or Strobe
# 7 : Data Bit 0             - NOT USED
# 8 : Data Bit 1             - NOT USED
# 9 : Data Bit 2             - NOT USED
# 10: Data Bit 3             - NOT USED
# 11: Data Bit 4
# 12: Data Bit 5
# 13: Data Bit 6
# 14: Data Bit 7
# 15: LCD Backlight +5V**
# 16: LCD Backlight GND

#import
import RPi.GPIO as GPIO
import time

# Define GPIO to LCD mapping
LCD_RS = 7
LCD_E  = 8
LCD_D4 = 25 
LCD_D5 = 24
LCD_D6 = 23
LCD_D7 = 18
LED_ON = 15  # LCD Backlight - BOARD 10 EQV

# Define some device constants
LCD_WIDTH = 20    # Maximum characters per line
LCD_CHR = True
LCD_CMD = False

LCD_LINE_1 = 0x80 # LCD RAM address for the 1st line
LCD_LINE_2 = 0xC0 # LCD RAM address for the 2nd line
LCD_LINE_3 = 0x94 # LCD RAM address for the 3rd line
LCD_LINE_4 = 0xD4 # LCD RAM address for the 4th line 

# Timing constants
E_PULSE = 0.00005
E_DELAY = 0.00005

GPIO.setmode(GPIO.BCM)       # Use BCM GPIO numbers
GPIO.setup(LCD_E, GPIO.OUT)  # E
GPIO.setup(LCD_RS, GPIO.OUT) # RS
GPIO.setup(LCD_D4, GPIO.OUT) # DB4
GPIO.setup(LCD_D5, GPIO.OUT) # DB5
GPIO.setup(LCD_D6, GPIO.OUT) # DB6
GPIO.setup(LCD_D7, GPIO.OUT) # DB7
# GPIO.setup(LED_ON, GPIO.OUT) # Backlight enable


def lcd_init():
  # Initialise display
  lcd_byte(0x33,LCD_CMD)
  lcd_byte(0x32,LCD_CMD)
  lcd_byte(0x28,LCD_CMD)
  lcd_byte(0x0C,LCD_CMD)  
  lcd_byte(0x06,LCD_CMD)
  lcd_byte(0x01,LCD_CMD)  

def lcd_string(message,style):
  # Send string to display
  # style=1 Left justified
  # style=2 Centred
  # style=3 Right justified

  if style==1:
    message = message.ljust(LCD_WIDTH," ")  
  elif style==2:
    message = message.center(LCD_WIDTH," ")
  elif style==3:
    message = message.rjust(LCD_WIDTH," ")

  for i in range(LCD_WIDTH):
    lcd_byte(ord(message[i]),LCD_CHR)

def lcd_byte(bits, mode):
  # Send byte to data pins
  # bits = data
  # mode = True(LCD_CHR)  for character
  #        False(LCD_CMD) for command

  GPIO.output(LCD_RS, mode) # RS

  # High bits
  GPIO.output(LCD_D4, False)
  GPIO.output(LCD_D5, False)
  GPIO.output(LCD_D6, False)
  GPIO.output(LCD_D7, False)
  if bits&0x10==0x10:
    GPIO.output(LCD_D4, True)
  if bits&0x20==0x20:
    GPIO.output(LCD_D5, True)
  if bits&0x40==0x40:
    GPIO.output(LCD_D6, True)
  if bits&0x80==0x80:
    GPIO.output(LCD_D7, True)

  # Toggle 'Enable' pin
  time.sleep(E_DELAY)    
  GPIO.output(LCD_E, True)  
  time.sleep(E_PULSE)
  GPIO.output(LCD_E, False)  
  time.sleep(E_DELAY)      

  # Low bits
  GPIO.output(LCD_D4, False)
  GPIO.output(LCD_D5, False)
  GPIO.output(LCD_D6, False)
  GPIO.output(LCD_D7, False)
  if bits&0x01==0x01:
    GPIO.output(LCD_D4, True)
  if bits&0x02==0x02:
    GPIO.output(LCD_D5, True)
  if bits&0x04==0x04:
    GPIO.output(LCD_D6, True)
  if bits&0x08==0x08:
    GPIO.output(LCD_D7, True)

  # Toggle 'Enable' pin
  time.sleep(E_DELAY)    
  GPIO.output(LCD_E, True)  
  time.sleep(E_PULSE)
  GPIO.output(LCD_E, False)  
  time.sleep(E_DELAY)   

def lcd_string(txtmessage,lineno,tstyle):
  lcd_byte(lineno, LCD_CMD)
  lcd_string(txtmessage,tstyle)

def lcd_clear(line):
  if line&0xFF==0xFF:
    all_lines = [LCD_LINE_1,LCD_LINE_2,LCD_LINE_3,LCD_LINE_4]
    for n in all_lines:
      lcd_byte(n , LCD_CMD)
      lcd_string("",3)
  else
    lcd_byte(line, LCD_CMD)
    lcd_string("",3)

def main():
  # Main program block

  # Initialise display
  print "Starting LCD"
  lcd_init()

  print "Starting LCD"
  # Send some centered text
  lcd_string("--------------------",LCD_LINE_1,2)
  lcd_string("Rasbperry Pi",LCD_LINE_2,2)
  lcd_string("Model B",LCD_LINE_3,2)
  lcd_string("--------------------",LCD_LINE_4,2)
  
  time.sleep(3) # 3 second delay 

  lcd_string("Starting up LCD",LCD_LINE_1,2)
  lcd_string("---------------",LCD_LINE_2,3)
  lcd_string("",LCD_LINE_3,3)
  lcd_string("Please Wait...",LCD_LINE_4,1)

  time.sleep(20) # 20 second delay 

  # Blank display
  lcd_clear(0xFF)


  time.sleep(3) # 3 second delay

# Start of program
if __name__ == '__main__':
  main()



type temporary_type=utf8 record
   string("\x01") capacity;
   string("\x01") gic2;
   string("\x01") counterparty_type;
   string("\x01") counterparty_class;
   string("\x01") ubs_ag_business_code;
   string("\x01") euro_ind_ccnace_code;
   string("\x01") company_relationship_comprel;
   string("\x01") new_boe_inst;
   string("\x01") capital_adequacy_risk_weighting;
end; /*Temporary variable*/





// Rollup

temp :: rollup(temp, in) =
begin
   temp.capacity :: if(!is_null(in.classification_value) and in.classification_value member [vector "AGENT","PRINC","UNDPRIN","AGENTUPR"]) temp.capacity + ',' + in.classification_value else temp.capacity;
   temp.gic2 :: if(in.classification_type_tid == 60233662000) temp.gic2 + ',' + in.classification_value else temp.gic2;
   temp.counterparty_type :: if(in.classification_type_tid == 60233660000) temp.counterparty_type + ',' + in.classification_value else temp.counterparty_type;
   temp.counterparty_class :: if(in.classification_type_tid == 60233672000) temp.counterparty_class + ',' + in.classification_value else temp.counterparty_class;
   temp.ubs_ag_business_code :: if(in.classification_type_tid == 60233674000) temp.ubs_ag_business_code + ',' + in.classification_value else temp.ubs_ag_business_code;
   temp.euro_ind_ccnace_code :: if(in.classification_type_tid == 43515370000) temp.euro_ind_ccnace_code + ',' + in.classification_value else temp.euro_ind_ccnace_code;
   temp.company_relationship_comprel :: if(in.classification_type_tid == 60233666000) temp.company_relationship_comprel + ',' + in.classification_value else temp.company_relationship_comprel;
   temp.new_boe_inst :: if(in.classification_type_tid == 60233664000) temp.new_boe_inst + ',' + in.classification_value else temp.new_boe_inst;
   temp.capital_adequacy_risk_weighting :: if(in.classification_type_tid == 60233668000) temp.capital_adequacy_risk_weighting + ',' + in.classification_value else temp.capital_adequacy_risk_weighting;
end;

temp :: initialize(in) =
begin
   temp.capacity :: "";
   temp.gic2 :: "";
   temp.counterparty_type :: "";
   temp.counterparty_class :: "";
   temp.ubs_ag_business_code :: "";
   temp.euro_ind_ccnace_code :: "";
   temp.company_relationship_comprel :: "";
   temp.new_boe_inst :: "";
   temp.capital_adequacy_risk_weighting :: "";
end;

out :: finalize(temp, in) =
begin
  out.party_id :: in.party_id;
  out.capacity :: string_substring( first_defined(temp.capacity,","),2,length_of(first_defined(temp.capacity,",")));
out.gic2 :: string_substring( first_defined(temp.gic2,","),2,length_of(first_defined(temp.gic2,",")));
out.counterparty_type :: string_substring( first_defined(temp.counterparty_type,","),2,length_of(first_defined(temp.counterparty_type,",")));
out.counterparty_class :: string_substring( first_defined(temp.counterparty_class,","),2,length_of(first_defined(temp.counterparty_class,",")));
out.ubs_ag_business_code :: string_substring( first_defined(temp.ubs_ag_business_code,","),2,length_of(first_defined(temp.ubs_ag_business_code,",")));
out.euro_ind_ccnace_code :: string_substring( first_defined(temp.euro_ind_ccnace_code,","),2,length_of(first_defined(temp.euro_ind_ccnace_code,",")));
out.company_relationship_comprel :: string_substring( first_defined(temp.company_relationship_comprel,","),2,length_of(first_defined(temp.company_relationship_comprel,",")));
out.new_boe_inst :: string_substring( first_defined(temp.new_boe_inst,","),2,length_of(first_defined(temp.new_boe_inst,",")));
out.capital_adequacy_risk_weighting :: string_substring( first_defined(temp.capital_adequacy_risk_weighting,","),2,length_of(first_defined(temp.capital_adequacy_risk_weighting,",")));
end;



