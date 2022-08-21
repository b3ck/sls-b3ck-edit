Why?
============
To streamline an SRT solution for the IRL community, with NOALBS(https://b3ck.com/noalbs) setup to work specifically with this SRT Solution you have one of the most reliable IRL stream recovery systems that you can run on your own.

Starting Notes:
============
- Cloned from https://gitlab.com/mattwb65/srt-live-server
- Edited by b3ck for the IRL Community (Added Credits & Links during server bootup to help others)
- This readme only covers Docker Desktop for Windows, but may have similarities to other Operating Systems but it's not intended.


Introduction:
============
srt-live-server(SLS) is an open source live streaming server for low latency based on Secure Reliable Transport (SRT).
Normally, the latency of transport by SLS is less than 1 second in internet.


Requirements:
============
Install Docker >> https://docs.docker.com/get-docker/

Command Line Alternative:
============
If you don't want to use the Docker Desktop you can alternatively run this command after installing docker:
```
docker run -d -p 30000:30000/udp -p 8181:8181/tcp --name=sls-b3ck-edit --restart=always --pull=always -v sls-b3ck-edit_data:/data b3ckontwitch/sls-b3ck-edit
```

Install Instructions (Using Docker Desktop):
============
- Run this command in a Command window or PowerShell: `docker pull b3ckontwitch/sls-b3ck-edit`
- Open your Docker Dashboard, click on Images on the left and you should see `b3ckontwitch/sls-b3ck-edit` listed.
- Hover over the `b3ckontwitch/sls-b3ck-edit` image you should see a `RUN` button on the right, hit the `RUN` button.
- A `New Container` window will pop-up;
> - Click on optional settings.
> - Give the container any name you want.
> - For the Ports section just match the local host ports with the container ports;
> - You'll need to click the (+) button to add the `8181` web server port.

> - New Container Configuration Control:
> - If you want to be able to change your configuration settings, like ports and streamid(s), you'll need to make a directory on your computer and put the `sls.conf` in the directory then match it up with a volume in the container and have it point to `/ETC/SLS`.

> - If you will be using a custom `sls.conf` then you'll need to make one and put it in that directory you just made on your computer, here is an example of what should be in it;

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

        # This is what determines your "play" URL..
        domain_player play; # if this was set to "view" your player URL would be for example; view/live/feed1
        
        # This is what determines your "publish" URL..
        domain_publisher publish; # if this was set to "give" your publish URL would be for example; give/live/feed1
        
        default_sid publish/live/feed1; # If your SRT client/encoder doesn't support streamid, this will be the default.
        
        backlog 100; # Accept connections at the same time.
        idle_streams_timeout 3; # How many seconds until streams are considered idle and then closed.
        
        
        app {
            app_player live; # Name of your player URL, ex; if this was set to "remote" the play URL would be; play/remote/cam1
            app_publisher live; # Name of your publisher URL, ex; if this was set to "access" the publish URL would be; publish/access/cam1
            
            record_hls off; # Turn HLS on/off
            record_hls_segment_duration 10; # How long in seconds you want the HSL segments to be.
         
        }
    }
}
```

> - If you don't change the config these defaults are as follows:
> - SRT Port: `30000`
> - OBS Play URL: `srt://<YOUR-LOCAL-IP>:<SRT-PORT>?streamid=play/live/feed1`
> - Client Publish URL: `srt://<YOUR-EXTERNAL-IP>:<SRT-PORT>?streamid=publish/live/feed1`
> - Example Client Publish URL: `srt://255.255.255.255:30000?streamid=publish/live/feed1`

- After you figured out the way you want to manage your configuration click on the blue `RUN` button to add the image to a docker container.


> - You could also install 'Microsoft Code' >> https://code.visualstudio.com/download , then install the Docker Extension and then browse/edit/save the `sls.conf`, please keep in mind you can only browse/edit/save files in a Docker Container while it's running:

> ![image](https://user-images.githubusercontent.com/1740542/110266056-a378a180-7f82-11eb-8806-c1cf968dc30f.png)

Viewing your SRT stream in OBS:
============
OBS supports SRT protocol to view stream when version is later than v25.0. you can use the following URL in media source:

`srt://<YOUR-IP>:<PORT>?streamid=play/live/feed1`
example: `srt://127.0.0.1:30000?streamid=play/live/feed1`

![image](https://user-images.githubusercontent.com/1740542/110401480-4262d380-803f-11eb-96f7-8c1760010d00.png)

Things to Know:
https://apps.apple.com/us/app/larix-broadcaster/id1042474385

1.) SLS refers to the RTMP URL format(domain/app/stream_name), example: publish/live/feed1. The URL of SLS must be set in streamid parameter of SRT, which will be the unique identification a stream.

2.) How to distinguish the publisher and player of the same stream? In conf file, you can set parameters of domain_player/domain_publisher and app_player/app_publisher to resolve it. Importantly, the two combination strings of domain_publisher/app_publisher and domain_player/app_player must not be equal in the same server block.

3.) There is an APP you can use on your phone to stream to your SRT server:
- Android: https://play.google.com/store/apps/details?id=com.wmspanel.larix_broadcaster
- Apple: https://apps.apple.com/us/app/larix-broadcaster/id1042474385

VMware - Docker Desktop Prerequisites (Provided by: Th3GamerVerse)
============
If you want to use Docker inside a VMWare Virtual Machine follow these steps:

1.) (In VMWare) Make sure the Docker VM BIOS is set to allow virtualization:
- a.) Shutdown the Docker VM (VM must be turned OFF)
- b.) Right click the Docker VM and choose “Edit Settings” 

![image](https://user-images.githubusercontent.com/1740542/132995110-b55bff56-3dc8-4400-bf01-e26c4e1335db.png)

- c.) Expand CPU setting:

![image](https://user-images.githubusercontent.com/1740542/132995148-49cc26c3-7512-4e3a-8917-d10b9d2faef4.png)

- d.) Select the “Expose hardware assisted virtualization to the guest OS” option:

![image](https://user-images.githubusercontent.com/1740542/132995189-231f2905-711a-4f59-a563-218105a1bbeb.png)

- e.) Power the Docker VM back up and continue with the following steps.

2.) Run these commands in PowerShell (on the Docker VM): (May not be needed in all cases)
- `Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All`
- `Enable-WindowsOptionalFeature -Online -FeatureName Containers -All`

3.) Install: https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi

4.) Run this final command in PowerShell:
- `wsl --set-default-version 2`

5.) After you have done all of this you should be able to install and run Docker Desktop in a Windows Virtual Machine using VMWare.


Release Notes
============
v1.4.8-b (b3ck edit)
---------------------------
1. Now compatible with SRT Source v1.4.2
2. Added Credits and Resources for the IRL Community.
3. Edited `DockerFile` to `checkout` the `master` from the SRT Source.
4. Created Windows Batch Files to build and start the SLS `DockerFile`.
5. Added support for SRTLA connections.

Contact / Support
============
If you have any issues feel free to message me on [discord](https://discordapp.com/channels/@me/96991451006660608) @ `b3ck#3517`
