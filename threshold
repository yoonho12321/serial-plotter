#define TSL1401_CLK 22  // Define CLK pin
#define TSL1401_SI 23   // Define SI pin
#define TSL1401_AO A0   // Define AO pin
#define NPIXELS 128     // Define the number of pixels

byte Pixel[NPIXELS]; // Array to store measured values <0-255>

#define FASTADC 1       // Enable fast ADC

// Macros for setting and clearing register bits
#define cbi(sfr, bit) (_SFR_BYTE(sfr) &= ~_BV(bit))
#define sbi(sfr, bit) (_SFR_BYTE(sfr) |= _BV(bit))

byte threshold_data[NPIXELS]; // Array for threshold-processed data

// Function to threshold pixel data
void line_threshold(int threshold)
{
  for(int i = 0; i < NPIXELS; i++) // Declare 'i' and properly increment
  {
    if (Pixel[i] > threshold) // If pixel value is above the threshold
    {
      threshold_data[i] = 255; // Assign 255
    } 
    else 
    {
      threshold_data[i] = 0; // Assign 0 if below threshold
    }
    
  }
}

void setup()
{
    Serial.begin(115200);  // Start serial communication (115200 baud)
    Serial.println("Camera Initialized");

    pinMode(TSL1401_CLK, OUTPUT); // Set CLK pin as output
    pinMode(TSL1401_SI, OUTPUT);  // Set SI pin as output

    digitalWrite(TSL1401_CLK, LOW); // Set initial CLK value
    digitalWrite(TSL1401_SI, LOW);  // Set initial SI value

    #if FASTADC
      // Set ADC prescaler to 16
      sbi(ADCSRA, ADPS2);
      cbi(ADCSRA, ADPS1);
      cbi(ADCSRA, ADPS0);
    #endif
}

// Function to read data from the TSL1401 camera
void read_TSL1401_camera(void)
{
    // Set SI and CLK signals
    digitalWrite(TSL1401_SI, HIGH);
    digitalWrite(TSL1401_CLK, HIGH);
    delayMicroseconds(1);

    digitalWrite(TSL1401_CLK, LOW);
    digitalWrite(TSL1401_SI, LOW);
    delayMicroseconds(1);

    // Read NPIXELS number of data points
    for(int i = 0; i < NPIXELS; i++)
    {
        digitalWrite(TSL1401_CLK, HIGH);
        delayMicroseconds(1);
        Pixel[i] = analogRead(TSL1401_AO); // Read analog data and store in Pixel array
        digitalWrite(TSL1401_CLK, LOW);
        delayMicroseconds(1);
    }
}

// Function to send camera data via serial communication
void send_camera_data_serial()
{
 
    for(int i = 0; i < NPIXELS; i++) // Loop through NPIXELS
    {
        Serial.print(Pixel[i]); // Output each pixel value via serial
        Serial.print(",");
        Serial.println(threshold_data[i]);
    }
  
    // Output additional 0s for 64 iterations
    for(int i = 0; i < 64; i++)
    {
        Serial.print(0);
        Serial.print(",");
        Serial.println(0); 
    }
}

void loop() 
{
  
    read_TSL1401_camera();  
    line_threshold(150);// Read camera data
    send_camera_data_serial();// Send the read data    // Process data with a threshold of
    delay(100);                  // Delay for 100ms
}
