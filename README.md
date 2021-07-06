# Control_P
float ReferenciaAltura = 40; //SetPoint o Referencia de altura de tanque.
const float Kp = 100; //Constante proporcional para control
const int Pwm = 10; //Pin 10 para pwm
float CicloUtil;

void setup() {
  pinMode(Pwm, OUTPUT);
  Serial.begin(9600);
}

void loop() {
  float ADCVal = analogRead(A0); //Canal A0 para sensor (0->0V-1023->5V) ADC de 10 bits
  float Sensor = ADCVal * (5.0 / 1023.0); //Realizamos transformación a voltaje
  float Error = (ReferenciaAltura * 0.0482) - Sensor; //El 0.048 transforma la altura en voltaje (Ganancia del sensor)
  float ControlP = Error * Kp; //Control Proporcional
  ControlP = ControlP * 0.08333; //Normalzación, 0.0833->1/12V
  
  if (ControlP > 1.0) { //Ciclo util maximo (k) no puede valer mas de 1
    ControlP = 1.0;
  }
  
  if (CicloUtil < 0) {
    CicloUtil = 0;
  }
  if (Error < 0) {
    Error = 0;
  }
  if (ControlP < 0) {
    ControlP = 0;
  }

  CicloUtil = ControlP * 255; //255 es el maximo para PWM (255->100%)
  delay(100);
  analogWrite(Pwm, CicloUtil); //Escritura de pwm para motobomba. (490Hz) para motobomba.
  Serial.print("Sensor (v):  ");
  Serial.print(Sensor);
  Serial.print("      Error ");
  Serial.print(Error);
  Serial.print("      ControlP: ");
  Serial.print(ControlP);
  Serial.print("      Altura: ");
  Serial.print(ADCVal / 10);
  Serial.print(" cm ");
  Serial.print("      PWM: ");
  Serial.println(CicloUtil);
}
