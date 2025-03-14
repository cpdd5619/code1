# code1
import RPi.GPIO as GPIO
import time

class SmokeAlarm:
    def __init__(self, smoke_sensor_pin=17, buzzer_pin=27):
        """Initialize the smoke alarm system."""
        self.smoke_sensor_pin = smoke_sensor_pin
        self.buzzer_pin = buzzer_pin
        
        # GPIO setup
        GPIO.setmode(GPIO.BCM)
        GPIO.setup(self.smoke_sensor_pin, GPIO.IN)
        GPIO.setup(self.buzzer_pin, GPIO.OUT)
        GPIO.output(self.buzzer_pin, GPIO.LOW)  # Ensure buzzer is off initially

    def detect_smoke(self):
        """Check for smoke presence, returns True if smoke is detected."""
        return GPIO.input(self.smoke_sensor_pin) == GPIO.LOW

    def alert(self):
        """Trigger the buzzer alarm when smoke is detected."""
        print("⚠️ WARNING! Smoke detected!")
        for _ in range(5):  # Beep 5 times
            GPIO.output(self.buzzer_pin, GPIO.HIGH)
            time.sleep(0.2)
            GPIO.output(self.buzzer_pin, GPIO.LOW)
            time.sleep(0.2)

    def monitor(self, interval=1):
        """Continuously monitor the smoke sensor."""
        try:
            while True:
                if self.detect_smoke():
                    self.alert()
                else:
                    print("✅ Environment is normal.")
                    GPIO.output(self.buzzer_pin, GPIO.LOW)  # Ensure buzzer is off
                
                time.sleep(interval)  # Adjust the detection interval
        except KeyboardInterrupt:
            self.cleanup()

    def cleanup(self):
        """Clean up GPIO resources on exit."""
        print("\nExiting... Cleaning up GPIO.")
        GPIO.cleanup()

# Create and start the smoke alarm monitoring system
if __name__ == "__main__":
    alarm = SmokeAlarm()
    alarm.monitor()
