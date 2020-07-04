import cv2
import numpy as np
import pyzbar.pyzbar as pyzbar
import pytesseract
import re
import time
import sys
import ibmiotf.application
import ibmiotf.device
import random


cap = cv2.VideoCapture(0)
font = cv2.FONT_HERSHEY_PLAIN

# TO READ THE COUNT VALUE FROM TXT FILE

prev_count = []
with open('sample.txt') as f:
    for line in f:
        try:
            n = int(line)
            if n > 999 or line.strip() == '-0': continue #filtering numbers >999 and strings with '-0'
            prev_count.append(n)
        except ValueError:
            pass

final_count=prev_count[0]

down=int(input("PEOPLE GETTING  DOWN : "))

count= final_count - down

print(count)
# SCANNING THE QR CODE

while count <= 50:
    _, frame = cap.read()

    decodedObjects = pyzbar.decode(frame)
    for obj in decodedObjects:
        a = obj.data;
        print("COUNTING PROCESS : ", count)
        if a == b'10001':
            count = count + 1
            print(" !! AUTHENTICAION VERIFIED :) !! HAVE A SAFE JOURNEY !!")
            time.sleep(3)

        else:
            print("!! OOPS  SORRY  :( YOUR AUTHENTICATION NOT VERIFIED !! PLEASE SCAN WITH THE  REQUIRED QR CODE !!")
            time.sleep(3)


    cv2.imshow("frame", frame)

    key = cv2.waitKey(1)
    if key == 27:
        break
adv=int(input("PEOPLE GETTING DOWN NEXT STOP : "))
count= count - adv
if (count >= 50):
    print("!! BUS IS FULL PLEASE WAIT FOR NEXT BUS !!")
    count = 50
else:
    count = count
    print("COUNT INSIDE THE BUS IS :", count)

# DETEREMIN THE NEXT STOP

L=str(input("ENTER THE NEXT BUS-STOP : "))

live_check=str(input("IS BUS STILL RUNNING : Y/N "))

if(live_check == 'Y' or live_check=='y' ):
    live_check='ONLINE'


elif(live_check=='N' or live_check=='n'):
    live_check='OFFLINE'
    count=0
    L='OFFLINE'

# CONFIGURE THE DEVICE FROM THE CLOUD
organization = "xkpuae"  # organization ID
deviceType = "bus28f"  # device type
deviceId = "0028"  # device id
authMethod = "token"
authToken = "123456728"  # token

try:
    deviceOptions = {"org": organization, "type": deviceType, "id": deviceId, "auth-method": authMethod,
                     "auth-token": authToken}
    deviceCli = ibmiotf.device.Client(deviceOptions)
    print("\n")


except Exception as e:
    print("Caught exception connecting device: %s" % str(e))
    sys.exit()

deviceCli.connect()
# !! AS WE ARE REPLACING HARDWARE WITH PYTHON PROGRAMMING THE COUNT AND THE LOCATION VALUES ARE TO BE ENTRED MANUALLY !!!

bus=deviceType
print("BUS NUMBER: ",bus) #TO PRINT THE BUS NUMBER

#VALUES FOR LOCATION AND COUNT
print("\n")

C=count
S=live_check
cloud_count=0
s= random.randint(25,48)
while True:
    data = {'d': {'Location': L, 'Count': C,'Live': S}} #data formate


    def myOnPublishCallback(): #to determine the location and count values

        print("Location = %s " % L, "Count = %s " % C ,"Live = %s " % S ,"to IBM Watson ")


    success = deviceCli.publishEvent("event", "json", data, qos=0, on_publish=myOnPublishCallback)
    cloud_count=cloud_count+1

    if not success:
        print("Problem in  connecting  to IOT platform")
    time.sleep(3)

    if (cloud_count==4):
        break;




deviceCli.disconnect()

print(" PLEASE NOTE THE PRESENT COUNT : " ,count)

fh = open('sample.txt','w')
fh.write("%d" %count)
fh.close()


#configure different device with specgic configuration

#  IBM Watson Device Credentials each device has its own credentials

# To acess different bus different credential is listed below

# ------------------------------------------------------------------

# for BUS_NUMBER 29C:
#Device Type = "bus29c"
#Device ID = "0029"
#AuthMethod = "token"
#AuthToken = "123456729"

# --------------------------------------------------------------
# FOR BUS_NUMBER 28F:
#Device Type="bus28f"
#Device ID = "0028"
#AuthMethod = "token"
#AuthToken ="123456728"

# ---------------------------------------------------------------
#For BUS_NUMBER 7G:
#Device Type ="bus7g"
#Device ID ="0007"
#AuthMethod = "token"
#AuthToken ="123456787"

# ------------------------------------------------------------------
#FOR BUS_NUMBER BUS12A :
#Device Type = "bus12a"
#Device ID = "0012"
#AuthMethod = "token"
#AuthToken = "123456712"

#------------------------------------------------------------------
#FOR BUS_NUMBER BUS46G :
#Device Type = "bus46g"
#Device ID = "0046"
#AuthMethod = "token"
#AuthToken = "123456746"

#------------------------------------------------------------------
#FOR BUS_NUMBER BUS6a :
#Device Type = "bus6a"
#Device ID = "0006"
#AuthMethod = "token"
#AuthToken = "123456786"

#-----------------------------------------------------------------
#FOR BUS_NUMBER BUS23E:
#Device Type = "bus23e"
#Device ID = "0023"
#AuthMethod = "token"
#AuthToken = "123456723"

#-------------------------------------------------------------------
#For bus number BUS25:
#Organization ID
#xkpuae
#Device Type
#bus25s
#Device ID
#0025
#Authentication Method
#use-token-auth
#Authentication Token
#123456725

#----------------------------------------------------------------------
#Four bus number bus30a :
#Organization ID
#xkpuae
#Device Type
#bus30a
#Device ID
#0030
#Authentication Method
#use-token-auth
#Authentication Token
#123456730
#---------------------------------------------------

#For bus number bus1a :
#Organization ID
#xkpuae
#Device Type
#bus1a
#Device ID
#0001
#Authentication Method
#use-token-auth
#Authentication Token
#123456781
