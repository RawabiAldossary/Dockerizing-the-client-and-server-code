# Dockerizing-the-client-and-server-code
 

### Create a docker file for both client and server 


```python
FROM python:3.10

RUN apt-get update & apt-get upgrade 

# SET MY WORKING DIR -
WORKDIR /client

# Add requirments 
ADD requirments.txt .
RUN pip install  -r requirments.txt


# SET MY WORKING DIR --> LOOK THIS UP 
WORKDIR /server

#  COPY FROM MY COMPUTER TO THE CONTAINER /CLIENT dir
COPY . .

ADD server.py .

EXPOSE 1883
EXPOSE 1883/udp

CMD ["python3" ,"-u","server.py"]
```

-  The **EXPOSE** instruction informs Docker that the container listens on the specified network ports at runtime. You can specify whether the port listens on TCP or UDP, and the default is TCP if the protocol is not specified.


- **EXPOSE 1883/udp** ==> to expose  UDP


- **-u** ==> Make the stream unbuffered 


- An alternative method is setting the PYTHONUNBUFFERED environment variable

### The Server script


```python
import socket
UDP_IP  = '0.0.0.0'
UDP_PORT = 1883

sock = socket.socket(socket.AF_INET, # Internet
                     socket.SOCK_DGRAM) # UDP
sock.bind((UDP_IP, UDP_PORT))
print("here")
while True:
    packet = sock.recv(10000)
    print(packet[6:7][0])
```

- The Server establish a connection 


- The While loop iterates until ==> (I have to know the sentence)


- print(packet[6:7][0]) ==> 
             To enusure that all the packets were reveiced 
             Extract the packet number by slicing the array

                          
                          
                            

### To Expose the ports at the run time 


```python
docker run  -p 1883:1883 -p 1883:1883/udp -- server
```

- **-P** ==> Publish all exposed ports to the host interfaces
- Connection resfued error ==> solved by adding **-p 1883:1883/udp** (mapping UDP port 80 in the container to UDP port 8080 on the Docker host)



### The Client script


```python
import socket


IPADDR = '0.0.0.0'
PORTNUM = 1883


s = socket.socket(socket.AF_INET, socket.SOCK_DGRAM, 0)

s.connect((IPADDR, PORTNUM))
for i in range (7) :
    p = packets[i]
    s.send(p)

s.close()
```
