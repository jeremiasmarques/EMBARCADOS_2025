# Biblioteca MPU6050 para ESP32

Esta biblioteca permite a leitura dos dados do acelerômetro e giroscópio do sensor MPU6050 via protocolo I2C utilizando o framework ESP-IDF no microcontrolador ESP32.

## Configuração

1. **Conecte o MPU6050 ao ESP32**:
   - Conecte o pino `VCC` do MPU6050 ao `3.3V` do ESP32.
   - Conecte o pino `GND` do MPU6050 ao `GND` do ESP32.
   - Conecte o pino `SDA` do MPU6050 ao pino `SDA` do ESP32 (por exemplo, GPIO 21).
   - Conecte o pino `SCL` do MPU6050 ao pino `SCL` do ESP32 (por exemplo, GPIO 22).

2. **Configure o I2C no seu projeto**:
   No arquivo `main.c` do seu projeto, configure o barramento I2C:
   ```c
        #include <stdio.h>
        #include "mpu6050.h"
        #include "freertos/FreeRTOS.h"
        #include "freertos/task.h"

        void app_main() {
        i2c_master_init();
        mpu6050_init();

          while (1) {
            // Ler aceleração
            float accel_x, accel_y, accel_z;
            read_acceleration(&accel_x, &accel_y, &accel_z);

            if(!(accel_x == accel_y && accel_z == 0 && accel_x == 0)) {
                printf("Aceleração: X=%.2fg, Y=%.2fg, Z=%.2fg\n", accel_x, accel_y, accel_z);
            }

            // Ler giroscópio
            float gyro_x, gyro_y, gyro_z;
            read_gyroscope(&gyro_x, &gyro_y, &gyro_z);

            if(!(gyro_x == gyro_y && gyro_z == 0 && gyro_x == 0)) {
                printf("Giroscópio: X=%.2f°/s, Y=%.2f°/s, Z=%.2f°/s\n", gyro_x, gyro_y, gyro_z);
            }

            // Angulo de Euler
            float roll, pitch, yaw;
            float dt = 1.0 / 100.0;
            calculate_euler_angles(&roll, &pitch, &yaw, dt);

            if(!(roll == 0 && pitch == 0 && yaw == 0)) {
                printf("Euler: roll=%.2f, pitch=%.2f, yaw=%.2f\n", roll, pitch, yaw);
            }

            vTaskDelay(pdMS_TO_TICKS(1000)); // Atualiza os dados a cada 1 segundo
          }
        }
   ```

## Uso

1. **Inicialize o MPU6050**:
   ```c
   mpu6050_init(I2C_NUM_0);
   ```

2. **Leia os dados do acelerômetro e giroscópio**:
   ```c
   mpu6050_data_t data;
   mpu6050_read_data(I2C_NUM_0, &data);

   printf("Acelerômetro: X=%f, Y=%f, Z=%f\n", data.accel_x, data.accel_y, data.accel_z);
   printf("Giroscópio: X=%f, Y=%f, Z=%f\n", data.gyro_x, data.gyro_y, data.gyro_z);
   ```

## Exemplo Completo

Aqui está um exemplo completo de como usar a biblioteca:

```c
#include <stdio.h>
#include "mpu6050.h"
#include "freertos/FreeRTOS.h"
#include "freertos/task.h"

void app_main() {
    i2c_master_init();
    mpu6050_init();

    while (1) {
        mpu6050_read_data();
        vTaskDelay(pdMS_TO_TICKS(1000)); // Atualiza os dados a cada 1 segundo
    }
}
```