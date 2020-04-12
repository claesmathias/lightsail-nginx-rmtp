# lightsail-streaming-server
Streaming server on AWS Lightsail

## Create instance
Create AWS Lightsail instance via [AWS Lightsail CLI](https://docs.aws.amazon.com/cli/latest/reference/lightsail/index.html "AWS Lightsail CLI")
```
aws lightsail create-instances --instance-names "streaming-vm" --availability-zone eu-west-2a --blueprint-id centos_7_1901_01 --bundle-id medium_2_0 --user-data file://user-data.txt
```
You can find the script in `/var/lib/cloud/instance/user-data.txt` on the instance

## Configure instance
### Open ports
```
aws lightsail open-instance-public-ports --port-info fromPort=1935,toPort=1935,protocol=TCP --instance-name streaming-vm
```

### Assign static IP (optional)
Static IP addresses are free only while attached to an instance. You can manage five at no additional cost.
```
aws lightsail allocate-static-ip --static-ip-name streaming-vm-static-ip
aws lightsail attach-static-ip --static-ip-name streaming-vm-static-ip --instance-name streaming-vm
```

### SSH
Download the key file [here](https://lightsail.aws.amazon.com/ls/webapp/account/keys "AWS Lightsail keys")
```
chmod 600 LightsailDefaultKey-eu-west-2.pem 
```
Obtain the public IP
```
aws lightsail get-instance --instance-name streaming-vm | grep publicIpAddress
aws lightsail get-static-ip --static-ip-name streaming-vm-static-ip | grep ipAddress
```
Login to your instance
```
ssh -i LightsailDefaultKey-eu-central-1.pem  centos@PUBLIC_IP
```
View instances [here](https://lightsail.aws.amazon.com/ls/webapp/home/instances "View instances")

## Stream video

## Server test
``` wget https://file-examples.com/wp-content/uploads/2017/04/file_example_MP4_1920_18MG.mp4
ffmpeg -re -i file_example_MP4_1920_18MG.mp4 -codec copy -f flv rtmp://localhost:1935/live/test
```

## Client test
* Open a [VLC](http://www.videolan.org/vlc/index.html) player (it also works in Raspberry Pi using `omxplayer`)
* Click in the "Media" menu
* Click in "Open Network Stream"
* Enter the URL from above as `rtmp://<ip_of_host>/live/<key>` replacing `<ip_of_host>` with the IP of the host in which the container is running and `<key>` with the key you created in OBS Studio. For example: `rtmp://192.168.0.30/live/test`
* Click "Play"
* Now VLC should start playing whatever you are transmitting from OBS Studio

