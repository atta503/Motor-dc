#include <stdio.h>
#include "freertos/FreeRTOS.h"
#include "freertos/task.h"
#include "driver/ledc.h"
#include "esp_err.h"

#define MOTOR_IN1_PIN       18 // Canal PWM 0
#define MOTOR_IN2_PIN       19 // Canal PWM 1

#define LEDC_TIMER          LEDC_TIMER_0
#define LEDC_MODE           LEDC_LOW_SPEED_MODE
#define LEDC_DUTY_RES       LEDC_TIMER_10_BIT
#define LEDC_FREQUENCY      5000

void init_motor_driver(void) {
    // Configura o Timer
    ledc_timer_config_t ledc_timer = {
        .speed_mode       = LEDC_MODE,
        .timer_num        = LEDC_TIMER,
        .duty_resolution  = LEDC_DUTY_RES,
        .freq_hz          = LEDC_FREQUENCY,
        .clk_cfg          = LEDC_AUTO_CLK
    };
    ESP_ERROR_CHECK(ledc_timer_config(&ledc_timer));

    // Configura Canal 0 para IN1
    ledc_channel_config_t ledc_ch0 = {
        .speed_mode     = LEDC_MODE,
        .channel        = LEDC_CHANNEL_0,
        .timer_sel      = LEDC_TIMER,
        .intr_type      = LEDC_INTR_DISABLE,
        .gpio_num       = MOTOR_IN1_PIN,
        .duty           = 0,
        .hpoint         = 0
    };
    ESP_ERROR_CHECK(ledc_channel_config(&ledc_ch0));

    // Configura Canal 1 para IN2
    ledc_channel_config_t ledc_ch1 = {
        .speed_mode     = LEDC_MODE,
        .channel        = LEDC_CHANNEL_1,
        .timer_sel      = LEDC_TIMER,
        .intr_type      = LEDC_INTR_DISABLE,
        .gpio_num       = MOTOR_IN2_PIN,
        .duty           = 0,
        .hpoint         = 0
    };
    ESP_ERROR_CHECK(ledc_channel_config(&ledc_ch1));
}

// Para girar para FRENTE: PWM no IN1 e 0 no IN2
// Para girar para TRÁS: 0 no IN1 e PWM no IN2
void set_motor_speed(int speed_in1, int speed_in2) {
    ledc_set_duty(LEDC_MODE, LEDC_CHANNEL_0, speed_in1);
    ledc_update_duty(LEDC_MODE, LEDC_CHANNEL_0);

    ledc_set_duty(LEDC_MODE, LEDC_CHANNEL_1, speed_in2);
    ledc_update_duty(LEDC_MODE, LEDC_CHANNEL_1);
}

void app_main(void) {
    init_motor_driver();

    while (1) {
        // Frente a 60% da velocidade (duty ~614 no IN1, 0 no IN2)
        set_motor_speed(614, 0);
        vTaskDelay(pdMS_TO_TICKS(3000));

        // Parar (0 nos dois)
        set_motor_speed(0, 0);
        vTaskDelay(pdMS_TO_TICKS(1000));

        // Trás a 100% da velocidade (0 no IN1, 1023 no IN2)
        set_motor_speed(0, 1023);
        vTaskDelay(pdMS_TO_TICKS(3000));

        // Parar
        set_motor_speed(0, 0);
        vTaskDelay(pdMS_TO_TICKS(1000));
    }
}
