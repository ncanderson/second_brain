202401291629
Status: #idea
Tags: #opt #bosch #work 

# gss_video_streamer

![](https://ca.slack-edge.com/T7G6XJ012-U021U88P50F-c8a3e88961e0-48)

Nate Anderson  [3:59 PM](https://greenseasystems.slack.com/archives/D022E3HE7T9/p1706561968051009)  

do you have a minute for some video streamer config?

![+1](https://a.slack-edge.com/production-standard-emoji-assets/14.0/google-small/1f44d.png)![rolling_on_the_floor_laughing](https://a.slack-edge.com/production-standard-emoji-assets/14.0/google-small/1f923.png)![point_up](https://a.slack-edge.com/production-standard-emoji-assets/14.0/google-small/261d-fe0f.png)

![](https://ca.slack-edge.com/T7G6XJ012-U01AFJP0ZS8-9e4e8650d38a-48)

Naomi  [4:00 PM](https://greenseasystems.slack.com/archives/D022E3HE7T9/p1706562052369639)  

yeah whats up

![](https://ca.slack-edge.com/T7G6XJ012-U021U88P50F-c8a3e88961e0-48)

Nate Anderson  [4:04 PM](https://greenseasystems.slack.com/archives/D022E3HE7T9/p1706562287513099)  

how does `gss_video_streamer` know which yml nodes to use for its config?

[4:05](https://greenseasystems.slack.com/archives/D022E3HE7T9/p1706562301270049)

like from here [https://github.com/GreenseaSystems/project_videoray_safec2/blob/0188701f9104060d66[…]/run/config_files/gss_video_streamer/sonar_bandwidth_config.yml](https://github.com/GreenseaSystems/project_videoray_safec2/blob/0188701f9104060d660ba9b863efc107de75b694/run/config_files/gss_video_streamer/sonar_bandwidth_config.yml#L18)

[sonar_bandwidth_config.yml](https://github.com/GreenseaSystems/project_videoray_safec2/blob/0188701f9104060d660ba9b863efc107de75b694/run/config_files/gss_video_streamer/sonar_bandwidth_config.yml)

ffmpeg_high:  

![<https://github.com/GreenseaSystems/project_videoray_safec2|GreenseaSystems/project_videoray_safec2>](https://slack-imgs.com/?c=1&o1=wi32.he32.si&url=https%3A%2F%2Fslack.github.com%2Fstatic%2Fimg%2Ffavicon-neutral.png)[GreenseaSystems/project_videoray_safec2](https://github.com/GreenseaSystems/project_videoray_safec2) | Added by [GitHub](https://greenseasystems.slack.com/services/B020FPGD3PA "GitHub")

[4:05](https://greenseasystems.slack.com/archives/D022E3HE7T9/p1706562336195639)

ffmpeg/shm/local

![](https://ca.slack-edge.com/T7G6XJ012-U01AFJP0ZS8-9e4e8650d38a-48)

Naomi  [4:05 PM](https://greenseasystems.slack.com/archives/D022E3HE7T9/p1706562338703599)  

the node names are hard coded (edited) 

[4:06](https://greenseasystems.slack.com/archives/D022E3HE7T9/p1706562394053469)

you gotta have all of them or it freaks out ik its bad design i just cant convince anyone to give me dev time sry

![](https://ca.slack-edge.com/T7G6XJ012-U021U88P50F-c8a3e88961e0-48)

Nate Anderson  [4:06 PM](https://greenseasystems.slack.com/archives/D022E3HE7T9/p1706562403383079)  

I'm confused since this doesn't seem true:  

# ALL OF THE FOLLOWING NODES MUST EXIST: stream_low, stream_med, stream_high, file_low, file_med, file_high, shm_low, shm_med, shm_high, local_low, local_med, local_high

![](https://ca.slack-edge.com/T7G6XJ012-U01AFJP0ZS8-9e4e8650d38a-48)

Naomi  [4:07 PM](https://greenseasystems.slack.com/archives/D022E3HE7T9/p1706562436427459)  

oh i guess i didnt update that comment

[4:07](https://greenseasystems.slack.com/archives/D022E3HE7T9/p1706562445467469)

the node names in the example config are the correct ones

![:+1:](https://a.slack-edge.com/production-standard-emoji-assets/14.0/google-small/1f44d.png)1

[4:08](https://greenseasystems.slack.com/archives/D022E3HE7T9/p1706562499068589)

stream and file are just combined into ffmpeg i think

![](https://ca.slack-edge.com/T7G6XJ012-U021U88P50F-c8a3e88961e0-48)

Nate Anderson  [4:08 PM](https://greenseasystems.slack.com/archives/D022E3HE7T9/p1706562522982339)  

So we have three sets of configs here. These are my process server args:  

    - client_name: local
      uuid: main_cam_stream
      process_name: gss_video_streamer
      arguments:
        - --output_config_file
        - /opt/greensea/opt_mdas/config_files/gss_video_streamer/main_camera_output_config.yml
        - --bandwidth_config_file
        - /opt/greensea/opt_mdas/config_files/gss_video_streamer/camera_bandwidth_config.yml
        - --input_video_address
        - [rtsp://service](rtsp://service):Bosch123!@172.31.0.8:554?h26x=0&line=1&channel=1
        - --default_frame_rate=12
        - --default_width=1440
        - --default_height=810
        - --default_bit_rate=40000000
        - --logging_level=info
        - --publish_channel=MAIN_VIDEO_STAT
        - --subscribe_channel=MAIN_VIDEO_CMD
      process_cmd: keep_alive
      keep_alive_ms: 1000
      publish_console: 0

[4:09](https://greenseasystems.slack.com/archives/D022E3HE7T9/p1706562548634009)

I don't see the correlation between this config and the output config.

![](https://ca.slack-edge.com/T7G6XJ012-U01AFJP0ZS8-9e4e8650d38a-48)

Naomi  [4:09 PM](https://greenseasystems.slack.com/archives/D022E3HE7T9/p1706562585735179)  

the process server config points to the output config where you list all the outputs you want to use

[4:09](https://greenseasystems.slack.com/archives/D022E3HE7T9/p1706562590344289)

is that what you mean?

[4:10](https://greenseasystems.slack.com/archives/D022E3HE7T9/p1706562659782479)

the reason theres an output config file is cause in some cases there are a lot of outputs for video streamer and its best to declare them separately to make it number-of-outputs-agnostic (edited) 

![](https://ca.slack-edge.com/T7G6XJ012-U021U88P50F-c8a3e88961e0-48)

Nate Anderson  [4:14 PM](https://greenseasystems.slack.com/archives/D022E3HE7T9/p1706562856641709)  

So I guess it consumes the whole file, and maintains the settings internally at runtime:  

temp_out_config->Set_bandwidth_settings(m_default_bandwidth_settings, m_ffmpeg_bw_settings, m_shm_bw_settings, m_local_bw_settings);

Which set of configs is used for the stream output?

[4:14](https://greenseasystems.slack.com/archives/D022E3HE7T9/p1706562867872269)

Or rather, what args configure the output?

![](https://ca.slack-edge.com/T7G6XJ012-U01AFJP0ZS8-9e4e8650d38a-48)

Naomi  [4:15 PM](https://greenseasystems.slack.com/archives/D022E3HE7T9/p1706562948059909)  

yep it always stores the settings for all types of output (bad design from 3 years ago Naomi)

[4:16](https://greenseasystems.slack.com/archives/D022E3HE7T9/p1706562977719309)

the nodes that control streams and files is the ffmpeg_* nodes

![](https://ca.slack-edge.com/T7G6XJ012-U021U88P50F-c8a3e88961e0-48)

Nate Anderson  [4:16 PM](https://greenseasystems.slack.com/archives/D022E3HE7T9/p1706563011955959)  

ok

![](https://ca.slack-edge.com/T7G6XJ012-U01AFJP0ZS8-9e4e8650d38a-48)

Naomi  [4:16 PM](https://greenseasystems.slack.com/archives/D022E3HE7T9/p1706563012734709)  

you can set low med and high bandwidth "profiles" that you can switch between without restating the stream

![](https://ca.slack-edge.com/T7G6XJ012-U021U88P50F-c8a3e88961e0-48)

Nate Anderson  [4:17 PM](https://greenseasystems.slack.com/archives/D022E3HE7T9/p1706563034209729)  

how would one use the shm output or local output?

![](https://ca.slack-edge.com/T7G6XJ012-U01AFJP0ZS8-9e4e8650d38a-48)

Naomi  [4:17 PM](https://greenseasystems.slack.com/archives/D022E3HE7T9/p1706563064334009)  

if you dont care about changing the bandwidth mid-stream though, you can set the default settings to what you want and never touch the profiles

[4:18](https://greenseasystems.slack.com/archives/D022E3HE7T9/p1706563099335109)

you can add a shm or local (pop-up window) output by adding it to the output config file

[4:19](https://greenseasystems.slack.com/archives/D022E3HE7T9/p1706563149459569)

in the bandwidth config file you modify the bandwidth profiles for each type separately (cause streams care more about bandwidth than shm for example) (edited) 

![](https://ca.slack-edge.com/T7G6XJ012-U021U88P50F-c8a3e88961e0-48)

Nate Anderson  [4:19 PM](https://greenseasystems.slack.com/archives/D022E3HE7T9/p1706563175871169)  

so 'stream' uses ffmpeg?  

stream:
  output_uuid: "radar"
  output_format: "stream"
  output_codec: "h264"
  output_address: "[udp://100.86.227.84:4004?ttl=0](udp://100.86.227.84:4004?ttl=0)"

![](https://ca.slack-edge.com/T7G6XJ012-U01AFJP0ZS8-9e4e8650d38a-48)

Naomi  [4:19 PM](https://greenseasystems.slack.com/archives/D022E3HE7T9/p1706563189529079)  

yep!

![](https://ca.slack-edge.com/T7G6XJ012-U021U88P50F-c8a3e88961e0-48)

Nate Anderson  [4:20 PM](https://greenseasystems.slack.com/archives/D022E3HE7T9/p1706563215368369)  

could one include multiple yml nodes in the output file?

![](https://ca.slack-edge.com/T7G6XJ012-U01AFJP0ZS8-9e4e8650d38a-48)

Naomi  [4:20 PM](https://greenseasystems.slack.com/archives/D022E3HE7T9/p1706563227696049)  

also the node title (stream) is arbitrary for the output config, it only matters for bandwidth config

[4:20](https://greenseasystems.slack.com/archives/D022E3HE7T9/p1706563248273499)

yep thats what the output file is for! so if you have multiple outputs you can set them up for each node

![](https://ca.slack-edge.com/T7G6XJ012-U021U88P50F-c8a3e88961e0-48)

Nate Anderson  [4:21 PM](https://greenseasystems.slack.com/archives/D022E3HE7T9/p1706563264432979)  

ok, cool

![](https://ca.slack-edge.com/T7G6XJ012-U01AFJP0ZS8-9e4e8650d38a-48)

Naomi  [4:21 PM](https://greenseasystems.slack.com/archives/D022E3HE7T9/p1706563285335219)  

the commented nodes here could be uncommented and everything would work fine [https://github.com/GreenseaSystems/project_videoray_safec2/blob/0188701f9104060d66[…]694/run/config_files/gss_video_streamer/sonar_output_config.yml](https://github.com/GreenseaSystems/project_videoray_safec2/blob/0188701f9104060d660ba9b863efc107de75b694/run/config_files/gss_video_streamer/sonar_output_config.yml)

![](https://ca.slack-edge.com/T7G6XJ012-U021U88P50F-c8a3e88961e0-48)

Nate Anderson  [4:21 PM](https://greenseasystems.slack.com/archives/D022E3HE7T9/p1706563299936399)  

And video only streamer supports 3 levels of output config?

![](https://ca.slack-edge.com/T7G6XJ012-U01AFJP0ZS8-9e4e8650d38a-48)

Naomi  [4:22 PM](https://greenseasystems.slack.com/archives/D022E3HE7T9/p1706563321471139)  

wdym? you can have as many outputs as you want

![](https://ca.slack-edge.com/T7G6XJ012-U021U88P50F-c8a3e88961e0-48)

Nate Anderson  [4:22 PM](https://greenseasystems.slack.com/archives/D022E3HE7T9/p1706563329201399)  

high medium and low

![](https://ca.slack-edge.com/T7G6XJ012-U01AFJP0ZS8-9e4e8650d38a-48)

Naomi  [4:22 PM](https://greenseasystems.slack.com/archives/D022E3HE7T9/p1706563338103359)  

oh but yeah there can only be 3 bandwidth profiles

[4:22](https://greenseasystems.slack.com/archives/D022E3HE7T9/p1706563344160119)

that and "off" is the 4th one (edited) 

![](https://ca.slack-edge.com/T7G6XJ012-U021U88P50F-c8a3e88961e0-48)

Nate Anderson  [4:22 PM](https://greenseasystems.slack.com/archives/D022E3HE7T9/p1706563358947579)  

kewl

![](https://ca.slack-edge.com/T7G6XJ012-U01AFJP0ZS8-9e4e8650d38a-48)

Naomi  [4:23 PM](https://greenseasystems.slack.com/archives/D022E3HE7T9/p1706563401839669)  

just to clarify when i say "output" i mean a stream/shm/local thing and when i say "bandwidth profile" i mean the high/med/low settings


# References

Slack conversation with Naomi. Starts here: https://greenseasystems.slack.com/archives/D022E3HE7T9/p1706561968051009