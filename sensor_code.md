# 对应传感器代码
## 目录
+ [湿度传感器/温度传感器](#湿度/温度传感器)
+ [粉尘传感器](#粉尘传感器)
+ [甲醛传感器](#甲醛传感器)
+ [TVOC传感器](#TVOC传感器)


## 湿度/温度传感器
```
#include <My_DHT_Using_Opp.h>
dht DHT;
#define DHT11_PIN 5
void setup()
{
  Serial.begin(9600);
}

void loop() 
{
  Serial.print("DHT11, \t");
  int chk = DHT.read11(DHT11_PIN);
  switch (chk)
  {
    case DHTLIB_OK:  
    Serial.print("OK,\t"); 
    break;
    case DHTLIB_ERROR_CHECKSUM: 
    Serial.print("Checksum error,\t"); 
    break;
    case DHTLIB_ERROR_TIMEOUT: 
    Serial.print("Time out error,\t"); 
    break;
    default: 
    Serial.print("Unknown error,\t"); 
    break;
  }
  Serial.print(DHT.humidity, 1);
  Serial.print(",\t");
  Serial.println(DHT.temperature, 1);

  delay(2000);
}
```
DHT 头文件在[此处](https://github.com/Docupa/air_project/blob/master/DHT11-master.zip)




## 粉尘传感器
```
int dustPin=0;
float dustVal=0;
 
int ledPower=2;
int delayTime=280;
int delayTime2=40;
float offTime=9680;
void setup()
{
  Serial.begin(9600);
  pinMode(ledPower,OUTPUT);
  pinMode(dustPin, INPUT);
}
 
void loop()
{
  digitalWrite(ledPower,LOW); 
  delayMicroseconds(delayTime);
  dustVal=analogRead(dustPin); 
  delayMicroseconds(delayTime2);
  digitalWrite(ledPower,HIGH); 
  delayMicroseconds(offTime);
 
  delay(1000);
  if (dustVal>36.455)
    Serial.println((float(dustVal/1024)-0.0356)*120000*0.035);
} 

```



## 甲醛传感器
```
void setup() 
{
  pinMode(A0,INPUT);
  Serial.begin(9600);
}
double map_user(double val,double from_min,double from_max,double to_min,double to_max)
{
  return(val/(from_max-from_min)*(to_max-to_min) + to_min);
}
void loop() 
{
  double a0_value;
  double a_0;
  a0_value=analogRead(A0);
  a0_value=map_user(a0_value,0,1023,0,5)-0.4;
  if(a0_value<0)
    a0_value=0;
  a_0=map_user(a0_value,0,1.6,0,5);
  Serial.print(a_0);
  Serial.println(" ppm");
  delay(2000);
}
//由于暂时受条件影响，不能在不同浓度的甲醛环境下测试，代码不能保证必定可行。
//而仪器的官方文档写到模块的预热需要大约 3 分钟的时间，所以一开始的数据可能是不准确的。
```



## TVOC传感器
```
void setup() 
{
  Serial.begin(9600);
}

void loop() 
{
  char res[4];
  //数据读取占用 RX 端口，烧程序时需注意
  for(int i=0;i<4;++i)
  {
    res[i]=Serial.read();
    if(res[i]==0xff)
      --i;
  }
  if(res[0]!=0xff)
  {
    if(res[3]==res[2]+res[1]+res[0])
    {
      int low=res[2],high=res[1];
      high=high<<8;
      Serial.print((low+high)*0.1);
      Serial.println(" ppm");
    }
    else
      Serial.println("数据传输错误！");
  }
  delay(1000);
}
//由于暂时受条件影响，不能在不同浓度的TVOC环境下测试，代码不能保证必定可行。
//仪器的官方文档写到模块的预热需要大约 3 分钟的时间，预热过程中的数据默认 0xff 。
```
