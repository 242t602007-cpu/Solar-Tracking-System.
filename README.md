# Solar-Tracking-System.
import time
from machine import Pin, ADC, PWM

# LDR sensors
ldr_left = ADC(Pin(34))
ldr_right = ADC(Pin(35))
ldr_top = ADC(Pin(32))
ldr_bottom = ADC(Pin(33))

# Servo motors
servo_horizontal = PWM(Pin(18), freq=50)
servo_vertical = PWM(Pin(19), freq=50)

horizontal_angle = 90
vertical_angle = 90


def set_servo(servo, angle):
    # Convert angle to servo duty cycle
    duty = int(40 + (angle / 180) * 75)
    servo.duty(duty)


while True:

    # Read LDR values
    left = ldr_left.read()
    right = ldr_right.read()
    top = ldr_top.read()
    bottom = ldr_bottom.read()

    # Horizontal tracking
    if left > right + 100:
        horizontal_angle -= 2
    elif right > left + 100:
        horizontal_angle += 2

    # Vertical tracking
    if top > bottom + 100:
        vertical_angle += 2
    elif bottom > top + 100:
        vertical_angle -= 2

    # Limit servo angles
    horizontal_angle = max(0, min(180, horizontal_angle))
    vertical_angle = max(0, min(180, vertical_angle))

    # Move servos
    set_servo(servo_horizontal, horizontal_angle)
    set_servo(servo_vertical, vertical_angle)

    print("Left:", left,
          "Right:", right,
          "Top:", top,
          "Bottom:", bottom)

    print("Horizontal:", horizontal_angle,
          "Vertical:", vertical_angle)

    time.sleep(0.5)
