# STM32_FreeRTOS Touch & Bluetooth Controller
Bu proje, STM32 mikrodenetleyicisi ve FreeRTOS kullanılarak geliştirilmiş; dokunmatik sensör verilerini işleyen ve Bluetooth üzerinden haberleşme sağlayan bir gömülü yazılım uygulamasıdır.

## Projenin Amacı
Gerçek zamanlı işletim sistemi (RTOS) konseptlerini kullanarak; donanım kesmelerini (Hardware Interrupts), görev yönetimini (Task Management) ve kaynak paylaşımını (Resource Sharing) güvenli şekilde yönemeyi amaçlar.

## Kullanılan Teknolojiler ve Yöntemler
* Donanım: STM32 (Nucleo Serisi), HC-05/06 Bluetooth Modülü, Kapasitif Dokunmatik Sensör.

* Binary Semaphore: HAL_GPIO_EXTI_Callback (ISR) içerisinden BluetoothTask'ı tetiklemek için kullanıldı. Bu sayede "Deferred Interrupt Handling" mimarisi uygulandı.
* Mutex: UART hattına (ortak kaynak) erişimi korumak ve veri bütünlüğünü sağlamak için kullanıldı.

* DefaultTask: Sistem boşta (Idle) durumunu yönetir.
* LedTask: Sistemin çalıştığını gösteren "Heartbeat" LED'ini kontrol eder.
* BluetoothTask: Dokunma olayını bekler ve UART üzerinden veri gönderir.

## Çalışma Mantığı
* Kullanıcı sensöre dokunduğunda bir External Interrupt (EXTI) oluşur.
* Kesme fonksiyonu (HAL_GPIO_EXTI_Callback) sadece bir Semafor (Semaphore) bırakır ve hemen çıkar. (Keep ISR short!)
* Semaforu bekleyen BluetoothTask uyanır (Unblocked).
* Task, UART hattını kullanmak için Mutex'i alır (Acquire).
* "DOKUNULDU!" mesajını gönderir ve Mutex'i serbest bırakır.

## Pin Konfigürasyonu
* UART7: Bluetooth İletişimi (TX/RX)
* LED_GREEN: Durum LED'i
* TOUCH_PIN: Harici Kesme (EXTI) Girişi 