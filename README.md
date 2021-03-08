- Cloned from https://gitlab.com/mattwb65/srt-live-server
- Edited by b3ck for the IRL Community

Introduction
============
srt-live-server(SLS) is an open source live streaming server for low latency based on Secure Reliable Transport (SRT).
Normally, the latency of transport by SLS is less than 1 second in internet.


Requirements
==============
Install Docker >> https://docs.docker.com/get-docker/


Instructions
==============
- Run this command in a CMD window or PowerShell: `docker pull b3ckontwitch/sls-b3ck-edit`
- Open your Docker Dashboard, click on Images on the left and you should see `b3ckontwitch/sls-b3ck-edit` listed.
- Hover over the `b3ckontwitch/sls-b3ck-edit` image you should see a `RUN` button on the right, hit the `RUN` button.
- A `New Container` window will pop-up;
>> - Click on optional settings.
>> - Give the container any name you want.
>> - For the Ports section just match the local host ports with the container ports;
>> - You'll need to click the (+) button to add the `8181` web server port.

>> - New Container Configuration Control:
>> - If you want to be able to change your configuration settings, like ports and streamid(s), you'll need to make a directory on your computer and put the `sls.conf` in the directory then match it up with a volume in the container and have it point to `/ETC/SLS`.

>> - If you will be using a custom `sls.conf` then you'll need to make one and put it in that directory you just made on your computer, here is what should be in it;

```
srt {
    worker_threads  1; # Usually don't need to touch this
    worker_connections 300; # or this...
    
    http_port 8181; # HTTP Port for viewing your stats, ex; http://127.0.0.1:8181/stats, useful for 3rd Party Applications.
    cors_header *;  # Used for 3rd party applications, so you can pull data into them.
    
    log_file logs/error.log; # Log file location, if you have errors, look here.
    log_level info; # Log Level
    
    record_hls_path_prefix /tmp/mov/sls; # If you want HLS, this is the HLS location.
         
    server {
        listen 30000; # The port that SRT will listen on, don't forget to forward your ports!
        latency 1000; # Match this with your Client/Encoder, the lower this is then the less lag, but it could cause missed frames and pixelation,
        # in unstable conditions, You can go all the way up to 5000 with latency, which will usually help during unstable conditions, but induce lag.

        domain_player play; # This is what determines your play URL, ex; play/live/cam1
        domain_publisher publish; # This is what determines your publish URL, ex; publish/live/cam1
        
        default_sid publish/live/cam1; # If your SRT client/encoder doesn't support streamid, this will be the default.
        
        backlog 100; # Accept connections at the same time.
        idle_streams_timeout 3; # How many seconds until streams are considered idle and then closed.
        
        
        app {
            app_player live; # Name of your player URL, ex; if this was set to remote the play URL would be; play/remote/cam1
            app_publisher live; # Name of your publisher URL, ex; if this was set to access the publish URL would be; publish/access/cam1
            
            record_hls off; # Turn HLS on/off
            record_hls_segment_duration 10; # How long in seconds you want the HSL segments to be.
         
        }
    }
}
```

>> - If you don't change the config these defaults are as follows:
>> - SRT Port: 30000
>> - OBS Play URL: srt://<YOUR-LOCAL-IP>:30000?streamid=play/live/test
>> - Client Publish URL: srt://<YOUR-EXTERNAL-IP>:30000?streamid=publish/live/test

- After you figured out the way you want to manage your configuration click on the blue `RUN` button to add the image to a docker container.


>> - You could also install 'Microsoft Code' >> https://code.visualstudio.com/download , then install the Docker plugin and then browse and edit the `sls.conf` that way after the container is running:

>> ![image](https://user-images.githubusercontent.com/1740542/110266056-a378a180-7f82-11eb-8806-c1cf968dc30f.png)

View with OBS
==============
OBS supports SRT protocol to view stream when version is later than v25.0. you can use the following URL in media source:

`srt://<YOUR-IP>:<PORT>?streamid=play/live/test`
example: `srt://127.0.0.1:30000?streamid=play/live/test`

Note:
=====

1. SLS refers to the RTMP URL format(domain/app/stream_name), example: publish/live/test. The URL of SLS must be set in streamid parameter of SRT, which will be the unique identification a stream.

2. How to distinguish the publisher and player of the same stream? In conf file, you can set parameters of domain_player/domain_publisher and app_player/app_publisher to resolve it. Importantly, the two combination strings of domain_publisher/app_publisher and domain_player/app_player must not be equal in the same server block.

3. There is a simple android app for test sls, your can download from https://github.com/Edward-Wu/liteplayer-srt

Release Notes
============
v1.4.8-b (b3ck edit)
---------------------------
1. Now compatible with SRT Source v1.4.2
2. Added Credits and Resources for the IRL Community.
3. Edited `DockerFile` to `checkout` the `master` from the SRT Source.
4. Created Windows Batch Files to build and start the SLS `DockerFile`.
