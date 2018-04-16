# program_repository
*import os
import sys
import AWSIoTPythonSDK
sys.path.insert(0, os.path.dirname(AWSIoTPythonSDK.__file__))
from AWSIoTPythonSDK.MQTTLib import AWSIoTMQTTClient
import RPi.GPIO as gpio
import time
fan_out_ch4=37
light_out_hall_ch3=40
light_out_bedroom_ch2=38

def setup():
        gpio.setmode(gpio.BOARD)
        gpio.setwarnings(False)
        gpio.setup(fan_out_ch4,gpio.IN)
        gpio.setup(light_out_hall_ch3,gpio.IN)
        gpio.setup(light_out_bedroom_ch2,gpio.IN)
        gpio.setwarnings(False)
        gpio.setup(fan_out_ch4,gpio.OUT)
        gpio.setup(light_out_hall_ch3,gpio.OUT)
        gpio.setup(light_out_bedroom_ch2,gpio.OUT)
        
        gpio.setwarnings(False)

# For certificate based connection
myMQTTClient = AWSIoTMQTTClient("myClientID")
# For Websocket connection
# myMQTTClient = AWSIoTMQTTClient("myClientID", useWebsocket=True)
# Configurations
# For TLS mutual authentication
myMQTTClient.configureEndpoint("a3cd6yyy4as8ey.iot.us-west-2.amazonaws.com", 8883)
# For Websocket
# myMQTTClient.configureEndpoint("Your end point", 443)
cwd = os.getcwd()
print(cwd)
certificateFolder =  "certificates"
CA_certificatefileName =cwd +"/"+certificateFolder + "/rootCA.pem"  
privateKeyName = cwd +"/"+certificateFolder + "/d91b3ba369-private.pem.key"  
certificateFileName = cwd +"/"+certificateFolder + "/d91b3ba369-certificate.pem.crt"  


        
def message(self,data,message):
    print "message called"
    print data
    print message.payload
    print message.topic
    topic=message.topic
    payload = message.payload
    if topic=='hall/fan':
             if payload =="1":
                gpio.output(fan_out_ch4,gpio.HIGH)
               #print "fan going to turn on"
                print "hall\fan on"
             else :
               #  off_fan()
                gpio.output(fan_out_ch4,gpio.LOW)
                print "hall\fan off"
               # print "payload is not known"
    elif topic=='hall/light':
            
            if payload =="1":
                     gpio.output(light_out_hall_ch3,gpio.HIGH)
                     print("hall\light on")            
            else:
                    gpio.output(light_out_hall_ch3,gpio.LOW)
                    print("hall\light oFF")  
    elif topic=='hall/bedroom/light':
            if payload =="1":
                     gpio.output(light_out_bedroom_ch2,gpio.HIGH)
                     print("hall\bedroom\light on")
            else:
                    gpio.output(light_out_bedroom_ch2,gpio.LOW)
                    print("hall\bedroom\light off")
    else:
            print("unknown")





# For certificate based connection
myMQTTClient = AWSIoTMQTTClient("myClientID")
# For Websocket connection
# myMQTTClient = AWSIoTMQTTClient("myClientID", useWebsocket=True)
# Configurations
# For TLS mutual authentication
myMQTTClient.configureEndpoint("a3cd6yyy4as8ey.iot.us-west-2.amazonaws.com", 8883)
# For Websocket
# myMQTTClient.configureEndpoint("Your end point", 443)
cwd = os.getcwd()
print(cwd)
certificateFolder =  "certificates"
CA_certificatefileName =cwd +"/"+certificateFolder + "/rootCA.pem"  
privateKeyName = cwd +"/"+certificateFolder + "/d91b3ba369-private.pem.key"  
certificateFileName = cwd +"/"+certificateFolder + "/d91b3ba369-certificate.pem.crt"  

myMQTTClient.configureCredentials(CA_certificatefileName, privateKeyName, certificateFileName)
# For Websocket, we only need to configure the root CA
# myMQTTClient.configureCredentials("YOUR/ROOT/CA/PATH")
myMQTTClient.configureOfflinePublishQueueing(-1)  # Infinite offline Publish queueing
myMQTTClient.configureDrainingFrequency(2)  # Draining: 2 Hz
myMQTTClient.configureConnectDisconnectTimeout(10)  # 10 sec
myMQTTClient.configureMQTTOperationTimeout(5)  # 5 sec

myMQTTClient.connect()
myMQTTClient.publish("hall/fan", "myPayload", 0)
myMQTTClient.publish("hall/light", "myPayload", 0)
myMQTTClient.publish("hall/bedroom/light", "myPayload", 0)
myMQTTClient.subscribe("hall/fan", 1, message)
myMQTTClient.subscribe("hall/light", 1, message)
myMQTTClient.publish("hall/bedroom/light",1,message)

setup()

