# GÜNEŞ TAKIP SISTEMI

## Açıklama
Güneş takip sistemi projesinde güneşin konumunu algılayarak kendini otomatik olarak yönlendirebilen bir güneş takip sistemi geliştirdim. Tasarım sürecinde mekanik parçaları 3D modelleme ile oluşturup 3D yazıcıdan ürettim ve tüm donanım bileşenlerini entegre ederek çalışır bir prototip elde ettim. Donanım ve yazılımın birlikte çalıştığı bu sistemde, algılama ve kontrol mekanizmasını başarıyla kurgulayarak gerçek zamanlı tepki verebilen bir yapı oluşturdum. Bu süreç, yalnızca teknik bilgi kazanmamı değil; aynı zamanda problem çözme, sistematik düşünme ve bir projeyi baştan sona geliştirme becerilerimi de önemli ölçüde geliştirdi.

## Görseller
![1720019066722](https://github.com/user-attachments/assets/be3c3ddc-ba57-4f20-85bf-93e740839f8e)

## KOD
# Gerekli modüller ve sınıflar ithal ediliyor
from machine import Pin, ADC, PWM  # Donanım pinleri, analog-dijital çevirici ve PWM kontrolü için
import time  # Zamanlama işlemleri için

# LDR (ışık sensörü) pinleri tanımlanıyor; burada 4 tane sensör pini belirlenmiş
ldr_pins = [0, 1, 2, 3]

# Servo motorların kontrolü için kullanılan pinler atanıyor
servo_pin_x = Pin(10)
servo_pin_y = Pin(7)

# ADC kanalından okuma yapılabilmesi için ADC nesnesi oluşturuluyor
adc = ADC(0)

# PWM sinyalleri servo motorlar için oluşturuluyor
pwm_x = PWM(servo_pin_x)
pwm_y = PWM(servo_pin_y)

# Servo motorların çalışma frekansı ve minimum/maximum görev döngüsü (duty cycle) değerleri tanımlanıyor
SERVO_FREQ = 50  # Servo motorlar için tipik frekans (50Hz)
SERVO_MIN_DUTY = 1000  # Minimum duty cycle (servo minimum pozisyon)
SERVO_MAX_DUTY = 2000  # Maksimum duty cycle (servo maksimum pozisyon)

# PWM frekansları servo motorlara uygulanıyor
pwm_x.freq(SERVO_FREQ)
pwm_y.freq(SERVO_FREQ)

# Servo motoru X ekseninde belirli bir açıda konumlandırmak için fonksiyon
def move_servo_x(angle):
    # Açıyı (0-180 derece) servo motorun kabul ettiği duty cycle aralığına dönüştür
    duty = int((angle / 180) * (SERVO_MAX_DUTY - SERVO_MIN_DUTY) + SERVO_MIN_DUTY)
    # Hesaplanan duty cycle değerini nanosanise cinsinden PWM çıkışına uygula
    pwm_x.duty_ns(duty * 500)

# Servo motoru Y ekseninde belirli bir açıda konumlandırmak için fonksiyon
def move_servo_y(angle):
    # Açıyı duty cycle aralığına dönüştür
    duty = int((angle / 180) * (SERVO_MAX_DUTY - SERVO_MIN_DUTY) + SERVO_MIN_DUTY)
    # PWM sinyalini uygula
    pwm_y.duty_ns(duty * 500)

# Sonsuz döngü ile sistem sürekli çalışacak şekilde ayarlanıyor
while True:
    ldr_values = []  # LDR sensörlerinden alınan değerlerin saklanacağı liste

    # Tüm LDR pinlerinden veri okunuyor
    for pin in ldr_pins:
        ldr_pin = Pin(pin)
        ldr_value = adc.read_u16()  # 16-bit hassasiyette ADC okuması yapılıyor
        ldr_values.append(ldr_value)  # Okunan değer listeye ekleniyor

    # İlk iki LDR değerine göre servo motorların açısı hesaplanıyor
    # ADC maksimum değeri 65535 olarak kabul edilip 0-180 dereceye ölçekleniyor
    x_angle = (ldr_values[0] / 65535) * 180
    y_angle = (ldr_values[1] / 65535) * 180

    # Servo motorlar hesaplanan açı değerlerine göre hareket ettiriliyor
    move_servo_x(x_angle)
    move_servo_y(y_angle)

    # Döngü hızı için kısa bir bekleme süresi (0.1 saniye)
    time.sleep(0.1)
