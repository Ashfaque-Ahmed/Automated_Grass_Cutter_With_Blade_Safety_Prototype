# Automated_Grass_Cutter_With_Blade_Safety_Prototype
//motor Driver pin
int EN1 = 12;
int L_PWM1 = 24;
int R_PWM1 = 25;
int EN2 = 2;
int L_PWM2 = 26;
int R_PWM2 = 27;
int cutter = 22;

//TCS3200 Color Sensor pin
#define S0_PIN 5
#define S1_PIN 4
#define S2_PIN 7
#define S3_PIN 6
#define OUT_PIN  8

//HC-05 Sonar Sensor pin
const int trigPin = 9;
const int echoPin = 10;
long duration;
int distance;

void setup()
{
  // put your setup code here, to run once:
  pinMode(EN1, OUTPUT);
  pinMode(R_PWM1, OUTPUT);
  pinMode(L_PWM1, OUTPUT);
  pinMode(EN2, OUTPUT);
  pinMode(R_PWM2, OUTPUT);
  pinMode(L_PWM2, OUTPUT);
  pinMode(cutter, OUTPUT);

  // Set the S0, S1, S2, S3 Pins as Output
  pinMode(S0_PIN, OUTPUT);
  pinMode(S1_PIN, OUTPUT);
  pinMode(S2_PIN, OUTPUT);
  pinMode(S3_PIN, OUTPUT);
  //Set OUT_PIN as Input
  pinMode(OUT_PIN, INPUT);
  // Set Pulse Width scaling to 100%
  digitalWrite(S0_PIN, HIGH);
  digitalWrite(S1_PIN, HIGH);
  Serial.begin(9600);

  // Sonar
  pinMode(trigPin, OUTPUT); // Sets the trigPin as an Output
  pinMode(echoPin, INPUT); // Sets the echoPin as an Input
}
void loop()
{
  digitalWrite(R_PWM1, HIGH);
  digitalWrite(L_PWM1, LOW );
  analogWrite(EN1, 33);
  digitalWrite(R_PWM2, HIGH);
  digitalWrite(L_PWM2, LOW );
  analogWrite(EN2, 34 );
  digitalWrite(cutter, HIGH);
//Color Sensor
  int r, g, b;
  r = process_red_value();
  g = process_green_value();
  b = process_blue_value();
  Serial.print("r = ");
  Serial.print(r);
  Serial.print(" ");
  Serial.print("g = ");
  Serial.print(g);
  Serial.print(" ");
  Serial.print("b = ");
  Serial.print(b);
  Serial.print(" ");
  Serial.println();
  if (r < 42)
  {
    Serial.println("Not Grass");
  }
  else if (g < 153)
  {
    Serial.println("Grass detected");
    digitalWrite(cutter, LOW);
  }
  else if (b < 64)
  {
    Serial.println("Not Grass");
  }
 // Sonar
  digitalWrite(trigPin, LOW); // Clears the trigPin
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);// Sets the trigPin on HIGH state for 10 micro seconds
  digitalWrite(trigPin, LOW);
  duration = pulseIn(echoPin, HIGH);// Reads the echoPin, returns the sound wave travel time in microseconds
  distance = duration * 0.034 / 2;// Calculating the distance
  Serial.print("Distance: ");// Prints the distance on the Serial Monitor
  Serial.println(distance);
  if (distance <= 30)
  {
    analogWrite(EN1, 50);
    analogWrite(EN2, 0);
    digitalWrite(R_PWM1, LOW);
    digitalWrite(L_PWM1, HIGH);
    digitalWrite(R_PWM2, HIGH);
    digitalWrite(L_PWM2, LOW);
    delay(1000);
    digitalWrite(R_PWM1, HIGH);
    digitalWrite(L_PWM1, LOW );
    digitalWrite(R_PWM2, HIGH);
    digitalWrite(L_PWM2, LOW );
  }
}
int process_red_value()
{
  digitalWrite(S2_PIN, LOW);
  digitalWrite(S3_PIN, LOW);
  int pulse_length = pulseIn(OUT_PIN, LOW);
  return pulse_length;
}
int process_green_value()
{
  digitalWrite(S2_PIN, HIGH);
  digitalWrite(S3_PIN, HIGH);
  int pulse_length = pulseIn(OUT_PIN, LOW);
  return pulse_length;
}
int process_blue_value()
{
  digitalWrite(S2_PIN, LOW);
  digitalWrite(S3_PIN, HIGH);
  int pulse_length = pulseIn(OUT_PIN, LOW);
  return pulse_length;
}
