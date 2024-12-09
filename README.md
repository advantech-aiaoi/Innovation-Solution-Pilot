# Agent Studio Demo

## Application Notes
### Revision History
  **Version**   **Date**     **Editor**   **Modifications**
  ------------- ------------ ------------ ---------------------------
  0.1           2024/09/16   Alan.Chu     Initial draft.
  1.0           2024/10/29   Alan.Chu     Install in a simpler way.

[1. Introduction 2](#introduction)

[2. Agent Studio 3](#_Toc181105829)

[2.1 System Configuration 3](#system-configuration)

[2.2 Installing an extra SSD 3](#installing-an-extra-ssd)

[2.2.1 SSD Physical Installation 3](#ssd-physical-installation)

[2.2.2 Format and set up auto-mount and swap docker storage to ssd
4](#format-and-set-up-auto-mount-and-swap-docker-storage-to-ssd)

[2.2.3 Migrate Docker directory to SSD
5](#migrate-docker-directory-to-ssd)

[2.2.4 Test Docker on SSD (Optional) 6](#test-docker-on-ssd-optional)

[2.2.5 Disable Apport reporting (Optional)
6](#disable-apport-reporting-optional)

[2.3 Running Agent Studio 7](#running-agent-studio)

[2.3.1 Downloading Essential Data 7](#downloading-essential-data)

[2.3.2 Start Up Agent Studio 8](#start-up-agent-studio)

[2.4 Agent Studio Tool Function 9](#agent-studio-tool-function)

[2.4.1 LLM 9](#llm)

[2.4.2 Speech 10](#speech)

[2.4.3 Audio 11](#audio)

[2.4.4 Video 11](#video)

[2.4.5 Database 12](#database)

[2.4.6 Tools 12](#tools)

[2.4.7 Tips and trick 13](#tips-and-trick)

[3. ExampleS 15](#examples)

[3.1 Door detetion (Load Door\_advan in presets)
15](#door-detetion-load-door_alert_webrtc_advan-in-presets)

[3.2 Factory Clothing detection (Load Attire\_det\_advan in presets)
20](#factory-clothing-detection-load-attire_det_webrtc_advan-in-presets)

[3.3 Smoke and Fire detetion (Load Smoke\_advan/ Fire\_advan in presets)
24](#smoke-and-fire-detetion-load-smoke_alert_webrtc_advan-fire_alert_webrtc_advan-in-presets)

[3.4 Forbidden zone (Load Forbidden\_zone\_advan in presets)
27](#forbidden-zone-load-forbidden_zone_alert_webrtc_advan-in-presets)

[Reference 30](#reference)

[Terms and Defintion 31](#terms-and-defintion)

Introduction
============

This document is used for AE (Application Engineer) to demo Nvidia
Jetson Live Agent Studio on Advanetch MIC-AI products (e.g. MIC-733AO).

Agent Studio
============

Quickly design and experiment with building your own automation agents,
personal assistants, and edge AI systems in an interactive environment.
This sandbox allows you to connect multimodal LLMs, speech and vision
transformers, vector databases, prompt templates, and function calling
with live sensors and I/O. It is optimized for deployment on Jetson
devices, providing on-device computing, low-latency streaming, and
unified memory.

![](./images//media/image2.png)

System Configuration
--------------------

+----------------+----------------------------------------------------+
| **Name**       | **Description**                                    |
+================+====================================================+
| Product        | MIC-733-OA4A1 (64GB)                               |
+----------------+----------------------------------------------------+
| JetPack        | V6.0GA                                             |
|                |                                                    |
|                | (https://www.dropbox.com/scl/fo/dr6zcn             |
|                | 9vfq8eokdh9uug0/AEkb5ZXJfGv4\_cJX0d\_lUEo/MIC-733- |
|                | AO/MIC-733-AO5A1/MIC-733AO\_Orin\_6.0\_V1.0.5\_SDK |
|                | ?preview=MIC-733-AO\_Flash\_SOP.pdf&rlkey=css95k1m |
|                | 3b0698nn4wrvwisva&subfolder\_nav\_tracking=1&dl=0) |
+----------------+----------------------------------------------------+
| Jetpack SDK    | Need to be installed.                              |
|                | (<https://developer.nvidia.com/sdk-manager>)       |
|                |                                                    |
|                | On Jetpack SDK setting should uncheck Jetson       |
|                | Linux.                                             |
|                |                                                    |
|                | Install                                            |
|                | Video:(https://drive.google.c                      |
|                | om/file/d/1Gb7TjpdDceMkN6rCUbk\_wYiuGzSzErn0/view) |
+----------------+----------------------------------------------------+
| Storage (NVMe) | 512GB (recommended)                                |
+----------------+----------------------------------------------------+
| USB Camera     | Logitech c270 HD webcam or V4L2 compatible camera. |
+----------------+----------------------------------------------------+
| Internet       | Should connect to the internet                     |
+----------------+----------------------------------------------------+
| Demo video     | Provided by alan chu, you should ask him.          |
+----------------+----------------------------------------------------+

Installing an extra SSD
-----------------------

###  SSD Physical Installation 

1.  Disconnect the power supply and any peripherals from your Jetson
    developer kit.

2.  Carefully insert an NVMe SSD card into the carrier board of your
    Jetson developer kit, ensuring the connector is properly seated and
    secured with the screw.

3.  Afterwards, reconnect any peripherals and plug the power supply back
    in to power on the Jetson developer kit.

4.  Once the system boots up, check that your Jetson recognizes the new
    memory controller on the PCI bus.

> lspci

The output should look like the following:

> 0007:01:00.0 Non-Volatile memory controller: Marvell Technology Group
> Ltd. Device 1322 (rev 02)

### Format and set up auto-mount and swap docker storage to ssd

1\. Run lsblk to find the device name.

> lsblk

The output should look like the following:

NAME MAJ:MIN RM SIZE RO TYPE MOUNTPOINT

loop0 7:0 0 16M 1 loop

mmcblk1 179:0 0 59.5G 0 disk

├─mmcblk1p1 179:1 0 58G 0 part /

├─mmcblk1p2 179:2 0 128M 0 part

├─mmcblk1p3 179:3 0 768K 0 part

├─mmcblk1p4 179:4 0 31.6M 0 part

├─mmcblk1p5 179:5 0 128M 0 part

├─mmcblk1p6 179:6 0 768K 0 part

├─mmcblk1p7 179:7 0 31.6M 0 part

├─mmcblk1p8 179:8 0 80M 0 part

├─mmcblk1p9 179:9 0 512K 0 part

├─mmcblk1p10 179:10 0 64M 0 part

├─mmcblk1p11 179:11 0 80M 0 part

├─mmcblk1p12 179:12 0 512K 0 part

├─mmcblk1p13 179:13 0 64M 0 part

└─mmcblk1p14 179:14 0 879.5M 0 part

zram0 251:0 0 1.8G 0 disk \[SWAP\]

zram1 251:1 0 1.8G 0 disk \[SWAP\]

zram2 251:2 0 1.8G 0 disk \[SWAP\]

zram3 251:3 0 1.8G 0 disk \[SWAP\]

nvme0n1 259:0 0 238.5G 0 disk

Identify the device corresponding to your SSD. In this case, it
is nvme0n1.

2\. Format the SSD, create a mount point, and mount it to the
filesystem.

sudo mkfs.ext4 /dev/nvme0n1

sudo mkdir /ssd

sudo mount /dev/nvme0n1 /ssd

3\. In order to ensure that the mount persists after boot.

First, identify the UUID for your SSD:

lsblk -f

Then, open /etc/rc.local file:

sudo vi /etc/rc.local

Insert the following lines , replacing the UUID with the value found
from lsblk -f:

\#!/bin/bash

sleep 10

mount
UUID=\*\*\*\*\*\*\*\*\*\*\*\*-\*\*\*\*-\*\*\*\*-\*\*\*\*-\*\*\*\*\*\*\*\*
/ssd

systemctl daemon-reload

systemctl restart docker

journalctl -u docker

exit 0

4\. Then, ensure the file has execute permissions.

sudo chmod +x /etc/rc.local

5\. Finally, change the ownership of the /ssd directory.

sudo chown \${USER}:\${USER} /ssd

### Migrate Docker directory to SSD

Now that the SSD is installed and available on your device, you can use
the extra storage capacity to hold the storage-demanding Docker
directory.

1.  Stop the Docker service.

sudo systemctl stop docker

2.  Move the existing Docker folder

sudo du -csh /var/lib/docker/ && \\

sudo mkdir /ssd/docker && \\

sudo rsync -axPS /var/lib/docker/ /ssd/docker/ && \\

sudo du -csh /ssd/docker/

3\. Edit /etc/docker/daemon.json

sudo vi /etc/docker/daemon.json

Insert \"data-root\" line like the following.

{

\"runtimes\": {

\"nvidia\": {

"args": \[\],

\"path\": \"nvidia-container-runtime\",

}

},

\"default-runtime\": \"nvidia\",

\"data-root\": \"/ssd/docker\"

}

4\. Rename the old Docker data directory and remove it

sudo mv /var/lib/docker /var/lib/docker.old

(optional) sudo rm -rf /var/lib/docker.old

5\. Restart the docker daemon

sudo systemctl daemon-reload && \\

sudo systemctl restart docker && \\

sudo journalctl -u docker

### Test Docker on SSD (Optional)

1.  \[Terminal 1\] First, open a terminal to monitor the disk usage
    > while pulling a Docker image.

> watch -n1 df

2.  \[Terminal 2\] Next, open a new terminal and start Docker pull.

> docker pull nvcr.io/nvidia/l4t-base:r35.2.1

3.  \[Terminal 1\] Observe that the disk usage on /ssd goes up as the
    > container image is downloaded and extracted.

> \~\$ docker image ls
>
> REPOSITORY TAG IMAGE ID CREATED SIZE
>
> nvcr.io/nvidia/l4t-base r35.2.1 dc07eb476a1d 7 months ago 713MB

###  Disable Apport reporting (Optional)

Every time Agent Studio crashes, a crash report is generated. These
reports require a significant amount of storage space and can quickly
fill up your system\'s hard drive. (Apport reports are stored in
/var/lib/apport/coredump). To prevent these reports from being
generated, you need to disable Apport.

> a\. sudo vim /etc/default/apport\
> b. Set enabled to 0\
> c. Restart the service: sudo systemctl stop apport && sudo systemctl
> disable apport

Running Agent Studio 
--------------------

### Downloading Essential Data

1\. Jetson-containers

cd /ssd

git clone <https://github.com/dusty-nv/jetson-containers>

bash jetson-containers/install.sh

\#Note You should put the demo videos to the /data/videos/demo folder

2\. NanoLLM

cd /ssd

git-lfs install

git clone <git@github.com:JIA-HONG-CHU/agent_studio.git>

(This requires authorization from alan\_chu to download.)

\#Note You need to place the nanodb folder from pre\_install into
jetson-containers/data and move forbidden\_zone folder from pre\_install
into the jetson-containers/data/images folder.

/\* move folder to /ssd/agent\_studio (lack of one file)

3.  Agent studion Container

docker pull dustynv/nano\_llm:24.7-r36.2.0

\# Go inside Docker to fix bugs

jetson-containers run \--env HUGGINGFACE\_TOKEN= hf\_xyz123abc456 \\

-v /ssd/agent\_studio:/opt/NanoLLM \\

dustynv/nano\_llm:24.7-r36.2.0 \\

/bin/bash

\# This will take a while to build and install like 15 mins. Check there
is no error happened.

cd /opt/NanoLLM/pre\_install && sh pre\_install.sh

\# Commit docker container

docker commit \"your\_container\_id\"
dustynv/nano\_llm:24.7-r36.2.0\_bug\_fixed

4.  Register on the HuggingFace Website to Obtain an Access Token (In
    the Settings)

![](./images//media/image3.png)

### Start Up Agent Studio

1.  Start up

jetson-containers run \--env HUGGINGFACE\_TOKEN= hf\_xyz123abc456 \\

-v /ssd/agent\_studio:/opt/NanoLLM \\

-v
/ssd/agent\_studio/pre\_install/project\_presets:/data/nano\_llm/presets
\\

dustynv/nano\_llm:24.7-r36.2.0\_bug\_fixed \\

python3 -m nano\_llm.studio

\#Start up without HUGGINGFACE\_TOKEN

jetson-containers run \\

-v /ssd/agent\_studio:/opt/NanoLLM \\

-v
/ssd/agent\_studio/pre\_install/project\_presets:/data/nano\_llm/presets
\\

dustynv/nano\_llm:24.7-r36.2.0\_bug\_fixed \\

python3 -m nano\_llm.studio

This will start the server running on your device. You can then navigate
your browser to <https://IP_ADDRESS:8050> (Don't use Firefox to open)

Note that you can use the \--load option to pre-load your prebuilt
pipeline. If the program crashes, it will automatically reload the
pipeline you initially loaded (only if you used the \--load argument the
first time). Additionally, a \"clear memory\" function has been added.
You can open a browser and go to https://IP\_ADDRESS:8050/shutdown and,
after waiting about 15 seconds, log back into https://IP\_ADDRESS:8050.
You should see your memory usage return to its initial state.

![](./images//media/image4.png)

Agent Studio Tool Function
--------------------------

This section will introduce the functions of each node in the Agent
Studio toolbar.

### LLM

1.  NanoLLM

Load quantized LLM/VLM with MLC (speed), AWQ (quality), or HF
Transformers (compatability)

\- Inputs

\* str, list\[str\], np.ndarray, torch.Tensor, cudaImage, PIL.Image

\- Commands

\* /reset /refresh /generate (these inputs can control the chat)

\- Outputs

\* delta The latest detokenized text to be generated

\* partial All of the text generated in the response so far

\* final The complete response sent at the end of the request

\* words Similar to \'delta\', but with at least one word at a time

\* history A list of the messages in the chat history (as dictionaries)

\* tools Enable the bot to call functions in plugins connected on this
channel

2.  NanoLLM\_ICL

The NanoLLM\_ICL has the same functionality as NanoLLM but also supports
ICL (In-Context Learning). It is typically used in combination with
AutoPrompt\_ICL.

3.  AutoPrompt 

Automatically apply a template whenever new data arrives. For example,
the template \'\<image\> Describe the image\' would tag each incoming
image with this text prompt, typically used with VLMs. AutoPrompt can
generate structured messages that refer to previous inputs, such as
Image 1: \<image\> Image 2: \<image\>, or by using \<text\> for the last
received text. These recent inputs are used in a last-in, first-out
(LIFO) sequence.

4.  AutoPrompt\_ICL

Same function as AutoPrompt but add new ICL fearture. Users can input
images as ICL inputs by enclosing the image path in triple quotes
(\'\'\'). For example, by applying the template '''my\_image.png'''. The
User can also detetmine whether to use ROI and setting ROI region.

5.  UserPrompt

This plugin allows you to enter text using the keyboard, either through
the terminal or a text box in the user interface. Additionally, it can
load prompts from text or JSON files, and these files can include
references to other files for loading.

6.  Textstream

A basic plugin for displaying any text stream from the system in a UI
text box. For sources from ASR or LLM, it can add color highlights to
partial and final responses.

### Speech

1.  WhisperASR

Whisper streaming speech-to-text with TensorRT
(github.com/NVIDIA-AI-IOT/whisper\_trt)

Supports these Whisper models: \'tiny\' (39M), \'base\' (74M), \'small\'
(244M)

\- Inputs

\* audio bytes, list\[int\], np.ndarray, torch.Tensor (int16, float32)

\- Outputs

\* final The text transcript of the entire segment spoken in the input

\* partial Rolling partial transcript of the words spoken so far in the
audio segment

2.  PiperTTS

Piper text-to-speech using CUDA and onnxruntime
(github.com/rhasspy/piper)

Populates a list of the available Piper models that it can download,
along with the speaker voices

for multi-speaker models. The default model is \'en\_US-libritts-high\'.
Has controls for voice speed.

\- Inputs

\* str either individual words, sentences, or an entire passage to
synthesize

\- Outputs

\* audio np.ndarray (int16) at 16KHz sample rate (for low-quality
models) or 22.5KHz

(for medium/high)

3.  VAD Filter

Voice activity detection model using Silero. Drops incoming audio unless
it exceeds the VAD threshold.

Use this on incoming audio before ASR plugins to reduce spurious
transcripts produced from background noise.

\- Inputs

\* audio bytes, list\[int\], np.ndarray, torch.Tensor (int16, float32)

\- Outputs

\* audio np.ndarray or torch.Tensor (only when VAD confidence \>
threshold)

### Audio

1.  [AudioRecorder](https://github.com/dusty-nv/NanoLLM/blob/main/nano_llm/plugins/audio/audio_output.py)

Save an audio stream to WAV file on the server.

2.  [WebAudioIn](https://github.com/dusty-nv/NanoLLM/blob/main/nano_llm/plugins/audio/web_audio.py)

Recieve audio samples streamed from the client over websockets.

3.  [WebAudioOut](https://github.com/dusty-nv/NanoLLM/blob/main/nano_llm/plugins/audio/web_audio.py)

Transmit audio samples to the client over websockets.

### Video

1.  VideoSource:

Capture images from a camera (V4L2/CSI), network streams (RTP, RTSP), or
video files (MP4, MKV, AVI, FLV).

2.  VideoOutput

Output H264/H265-encoded video to a network stream (RTP, RTSP, WebRTC),
ROI, display, or save it to a file (MP4, MKV, AVI, FLV).

3.  VideoOverlay

Overlay text on video streams for a HUD or OSD-style display.

4.  RateLimit

Limit transmission speed to a specified rate, applicable for both video
and audio.

### Database

1.  **NanoDB**

An optimized in-memory multimodal vector database designed for txt2img
and img2img similarity searches as well as image tagging.

2.  **NanoDB\_Fashion**

The previous NanoDB required an index to be created beforehand for use.
However, with NanoDB\_Fashion, you can simply place images into a
folder, and a database specific to that folder will be generated on the
spot. This database allows for keyword or image-based searches and
utilizes a customized CLIP model specifically designed to handle fashion
and apparel search.

### Tools

1.  **OwlVit\_detector**

Use TensorRT-accelerated OWLv2 for detection tasks.

\- Inputs

\* cudaImage, PIL.Image

\- Outputs

\* image The detection visualized output

\* message The detection status output

2.  **One\_Step\_Alert**

Alerts are triggered based on specified keywords, with the system
monitoring their frequency within a set number of seconds. If the
majority of occurrences within this interval match alert keywords, an
alarm is activated.

3.  **Two\_Steps\_Alert**

The system monitors specified alert keywords, counting their frequency
within a set timeframe to determine if an alarm should be triggered.
Upon the initial alert, it moves to a second alert level. At this level,
the system checks within a specified timeframe for the presence of a
\"normal\" keyword to indicate resolution. If the normal keyword is
detected, the system resets to the first alert level; if not, the alarm
continues.

4.  **Save\_Pics**

The system monitors specified alert keywords and counts their frequency
within a set timeframe to determine if images should be saved. Users can
freely configure the number of images to be stored.

### Tips and trick

Many of the previous demos, such as Llamaspeak and Live Llava, can be
easily recreated in the flexible environment of Agent Studio without any
coding. Here are some practical tips for experimenting with different
model pipeline combinations:

**UI**

-   To add elements to the agent pipeline, use the menus in the node
    editor\'s title bar or right-click inside the editor.

-   Adding elements via the context menu (right-click) will place them
    where your cursor is, so you don\'t need to manually reposition
    them.

-   To remove a component from the pipeline, right-click on it and click
    the small X button in the top-right corner.

-   To disconnect components, select the link, then right-click to make
    the X button appear.

-   Clicking a node in the graph will open its grid widget if available.
    Click again to open the settings dialog.

**Save & Load**

-   Use the Save button under the Agent menu to serialize the current
    pipeline into JSON format.

-   These saved presets are stored in a mounted directory on the host at
    jetson-containers/data/nano\_llm/presets.

-   Some frequently used subgraphs, like ASR, TTS, and video VLM, are
    included for quick addition and combination.

-   You can load these subgraphs through the Agent menu, by
    right-clicking in the node editor, or by using the \--load flag at
    startup.

**Memory**

-   As you add models, monitor the system resources in the top-right
    corner to avoid running out of memory.

-   Models remain cached in memory even after being removed from the
    node editor, due to loading times.

-   To free up memory, use the Clear Cache button in the Agent menu.

**Performance**

-   System CPU and GPU usage can be monitored in the top-right corner.

-   Node performance statistics are regularly updated for each
    component.

-   Use the RateLimiter plugin to manage data flow and balance system
    resources.

-   The drop\_inputs setting in some plugins ensures they stay current
    with the latest request.

ExampleS
========

In this section, I will showcase four demos to familiarize readers with
Agent Studio and demonstrate the types of applications it can create.

For all the following examples, please request the videos from Alan Chu.

Door detetion (Load Door\_alert\_webrtc\_advan in presets)
----------------------------------------------------------

The door detection system will trigger an alarm if the door is detected
as open for a specified duration. If it remains open past an additional
specified timeframe without closing, the alarm will continue. Another
node is set up to capture images of anyone opening the door. Using the
NanoDB model, it then searches for specific clothing attributes of
individuals passing through the door, allowing for keyword-based
identification of attire.

![](./images//media/image5.png)

The nodes used are the ones listed above.

1.  VideoSource

In the \"Video\" section of the toolbar, find and select the
\"VideoSoruce Node.\" Then, make the following settings:

-   Input: Select the location of your video (e.g., input
    /data/videos/123.MOV). This video should be located under the same
    path in the jetson-containers folder.

-   Loops: Set to -1 to enable continuous playback of the video.

2.  RateLimit\_1

In the \"Video\" section of the toolbar, find and select the \"RateLimit
Node.\" Then, make the following settings:

-   Set Rate to 1: This means only one frame is processed per second
    (i.e., only one frame per second).

-   Leave Chunk empty: Use the default value, no special configuration
    is needed.

-   Set Drop inputs to True: This means only the latest frame is kept,
    while older frames are discarded and not saved.

3.  Autoprompt

> in the \"LLM\" section of the toolbar, find and select the
> \"Autoprompt Node.\" Then, make the following settings:

-   This is to let the model know that the input prompt should be
    formatted like this. Our application is designed to determine the
    status of a safety door, so our prompt is written as follows:
    \"\<reset\>\<image\>Check the current status of the door. Is it open
    or closed?\" The \<reset\> is a special token that allows us to
    visualize the VILA model\'s output later, ensuring that only the
    results from the current frame are retained without preserving any
    historical results. The \<image\> is the location of the image being
    fed to the model, and the text following it is the question we want
    to ask.

-   seq\_replace\_mode set to true: When this feature is enabled, each
    new input will replace older ones step by step; if disabled, the
    input buffer will clear itself automatically when full.

4.  VILA1.5-3b

In the \"LLM\" section of the toolbar, find and select the \"NanoLLM
Node.\" Then, make the following settings:

-   Model Selection: We chose Efficient-Large-Model/VILA1.5-3b.

-   API Selection: We opted for MLC as the API, which enhances inference
    speed, allowing the system to respond more quickly.

-   Quantization Setting: We used the default q4f16\_ft quantization, a
    compression technique that reduces computational load while
    maintaining model performance.

-   MaxContextLength set to 256: This means the model\'s input can
    contain up to 256 tokens, which is sufficient for determining the
    status of the safety door in our application.

-   Drop inputs set to True: This ensures that only the latest frame is
    processed by the model, discarding older frames to ensure the model
    always processes the most recent information.

-   Chat Template selected as llava-v1: This is the foundational setting
    for the model\'s chat system. Choosing a chat template that matches
    the model\'s retraining process is crucial for maintaining model
    performance; otherwise, it can severely impact system performance.

-   System Prompt use the following description: A chat between a
    curious human and an artificial intelligence assistant. The
    assistant gives helpful answers to the human\'s questions.

5.  VideoOverlay

In the \"Video\" section of the toolbar, find and select the
\"VideoOverlay Node.\" Then, make the following settings:

This part can use the default settings. This Node is intended to
influence the font, typeface, and layout of the text output from the
model when rendered on an image.

6.  VideoOutput

In the \" Video\" section of the toolbar, find and select the
\"VideoOutput Node.\" Then, make the following settings:

This part can use the default settings. This node is intended to
determine where the output image should be projected and whether any
related settings need adjustment, such as encoding the image into a
specific format.

7.  Two\_Steps\_Alert

> The purpose of this module is to ensure that if the door is not closed
> within a specified time (first check) after receiving status messages
> from the VILA model, a text message will be sent to the next stage of
> the alarm system. Additionally, if the door remains open past the
> specified time (second check), an alert message will also be sent as a
> reminder.
>
> In the "Tool\" section of the toolbar, find and select the
> \"Two\_Steps\_Alert Node.\" Then, make the following settings:

-   First Check: Deafultset is 5 seconds. This is the time required for
    the initial assessment of the door status.

-   Second Check: Defaultset is 1 seconds. This is used for
    re-evaluating the door status when the initial assessment shows the
    door is \"open.\"

-   Alert: Should be set to true to enable the alert functionality.

-   Alert Keyword: This parameter indicates which keyword should be
    treated as an door open, we set it to open.

-   Normal Keyword: Set it to close, when the model is determined to be
    open during the first check, it will return to the first check\'s
    judgment if the door is assessed as closed during the second check.

-   Warning Message Text: As the title, Warning message text. The period
    \".\" should be in the last. So we set it to "Warning, The door is
    opened.".

-   Drop inputs set to True: This ensures that only the latest frame is
    processed by the model, discarding older frames to ensure the model
    always processes the most recent information.

8.  PiperTTS

![](./images//media/image6.png)

The nodes used are the ones listed above.

For this part, right-click and select presets, then choose PiperTTS to
bring up the text-to-speech system.

9.  RateLimit\_2

In the \" Video \" section of the toolbar, find and select the
\"RateLimit Node.\" Then, make the following settings:

-   Set Rate to 1: This means only one frame is processed per second
    (i.e., only one frame per second).

-   Leave Chunk empty: Use the default value, no special configuration
    is needed.

-   Set Drop inputs to True: This means only the latest frame is kept,
    while older frames are discarded and not saved.

10. AutoPrompt\_1

In the \" LLM\" section of the toolbar, find and select the \"AutoPrompt
Node.\" Then, make the following settings:

-   Setting the template to \<text\>\<image\> means that the input
    passed to the next node will be in the form of a list. The first
    element in this list will be a string of text, and the second
    element will be an image.

-   seq\_replace\_mode set to false: When this feature is enabled, each
    new input will replace older ones step by step; if disabled, the
    input buffer will clear itself automatically when full.

11. Save\_Pics

> In the "Tool\" section of the toolbar, find and select the
> \"Save\_Pics Node.\" Then, make the following settings:

-   Check Time: Deafultset is 1 second. This setting defines how many
    seconds after the door is detected as \"open\" before the system
    starts capturing images. The default value is 1 second, meaning the
    system will begin saving images 1 second after the door opens.

-   Save Frames: Defaultset is 3 frames. This parameter sets the
    duration for how long the system will capture images after the door
    opens. After saving "Save Frames\" photos, the system will return to
    detecting the door\'s status again.

-   Save Pics Folder: Defaultset is /data/nanodb/images. This specifies
    the folder where the captured images will be stored in. If you want
    to use this default path, you\'ll need to manually create the folder
    structure in the jetson-containers folder. Additionally, the stored
    photos will be in the folder with today\'s date under the path.

-   Alert: Should be set to true to enable the alert functionality.

-   Alert Keyword: Set to \"open,\" if the \"open\" keyword predominates
    during the specified check time, the system will proceed to store
    images.

-   Drop inputs set to True: This ensures that only the latest frame is
    processed by the model, discarding older frames to ensure the model
    always processes the most recent information.

12. NanoDB\_Fashion

> In the \"Database\" section of the toolbar, find and select the
> \"NanoDB\_Fashion Node.\" Then, make the following settings:

-   Path: Default is the /data/nanodb/images. This setting defines the
    directory where the NanoDB database will either be created or loaded
    from.

-   Model: Defaultset is patrickjohncyh/fashion-clip. This parameter
    specifies the embedding model to be used, either CLIP or SigLIP. You
    can choose a model from HuggingFace or provide a local path to use a
    custom model.

-   Dtype: Defaultset is 16-bit floating point. This determines whether
    the embeddings will be computed and stored as 16-bit or 32-bit
    floating point numbers.

-   Reserve: Default is 1024 MB. This setting reserves a specific amount
    of memory in megabytes (MB) for the database vectors..

-   Top K: Defaultset is top 10 results. This setting specifies how many
    search results or the top K similar entries will be returned when
    performing a search query. You can adjust this to return more or
    fewer results based on your needs.

-   Crop: Default is enabled. This parameter controls whether image
    cropping should be enabled or disabled. CLIP was trained with image
    cropping, so enabling it is recommended for CLIP. SigLIP does not
    require cropping.

-   Drop Inputs: Defaultset is ture. If true, only the latest message
    from the input queue will be used (older messages dropped).

-   Search Time: Format is Year/Month/Day. This specifies the format for
    time-based searches. You should provide the time in the format
    YYYY/MM/DD for date-specific queries, such as 2024/10/25.

> Open NanoDB\_Fashion and select data from the specified date using the
> Save\_Pics node to create a database. Once created, you\'ll see a
> block on the right side of the screen. The top section allows users to
> input the desired feature data for search, and the bottom section will
> display the search results. These results will be sorted based on the
> similarity between the photos and the input features, with the top K
> entries displayed according to the ranking you\'ve set.

![](./images//media/image7.png) ![](./images//media/image8.png)

Here is how to connect the nodes as described:

1.  **Connect the VideoSource node:**

    -   Connect the right interface of VideoSource to the left interface
        of RateLimit\_1 and the left interface of VideoOverlay and the
        left interface of RateLimit\_2.

2.  **Connect the RateLimit\_1 node:**

    -   Connect the right interface of RateLimit\_1 to the left
        interface of Autoprompt.

3.  **Connect the AutoPrompt node:**

    -   Connect the right interface of Autoprompt to the left interface
        of VILA1.5-3b.

4.  **Connect the VILA1.5-3b node:**

    -   Connect the right interface\'s **partial** output of VILA1.5-3b
        to the left interface of VideoOverlay.

    -   Connect the right interface\'s **final** output of VILA1.5-3b to
        the left interface of Two\_Steps\_Alert and the left interface
        of the AutoPrompt\_1.

5.  **Connect the RateLimit\_2 node:**

    -   Connect the right interface of RateLimit\_2 to the left
        interface of Autoprompt\_1.

6.  **Connect the Autoprompt\_1 node:**

    -   Connect the right interface of Autoprompt\_1 to the left
        interface of Save\_Pics.

7.  **Connect the VideoOverlay node:**

    -   Connect the right interface of VideoOverlay to the left
        interface of VideoOutput.

8.  **Connect the Two\_Steps\_Alert node:**

    -   Connect the right interface of Two\_Steps\_Alert to the left
        interface of PiperTTS module.

Factory Clothing detection (Load Attire\_det\_webrtc\_advan in presets)
-----------------------------------------------------------------------

This application uses the Owlvit detection model to determine whether
clothing meets the required standards. Users can customize the detection
items based on their specific scenarios. Additionally, the application
can automatically issue voice alerts to inform users about which pieces
of equipment are currently missing.

![](./images//media/image9.png)

The nodes used are the ones listed above.

1.  VideoSource

In the \"Video\" section of the toolbar, find and select the
\"VideoSoruce Node.\" Then, make the following settings:

-   Input: Select the location of your video (e.g., input
    /data/videos/123.MOV). This video should be located under the same
    path in the jetson-containers folder.

-   Loops: Set to -1 to enable continuous playback of the video.

2.  RateLimit\_1

In the \"Video\" section of the toolbar, find and select the \"RateLimit
Node.\" Then, make the following settings:

-   Set Rate to 30: This means 30 frames are processed per second.

-   Leave Chunk empty: Use the default value, no special configuration
    is needed.

-   Set Drop inputs to True: This means only the latest frame is kept,
    while older frames are discarded and not saved.

3.  OwlVit\_detector

In the \"Tools\" section of the toolbar, find and select the \"
OwlVit\_detector Node.\" Then, make the following settings:

-   Owl Json Path: This is a configuration file for detection. Please
    enter the path for this configuration file. Default is
    /opt/NanoLLM/nanoowl/owl.json.

-   Set Drop inputs to True: This means only the latest frame is kept,
    while older frames are discarded and not saved.

-   Activate to True: If True, the owlvit detector will work.

-   Return Copy to True: Copy incoming frames to prevent other possible
    consumers from losing the original.

-   The other items to True: These items represent the detection
    parameters specified in the Owl JSON file. Setting them to true
    indicates that detection is required, while setting them to false
    means detection is not necessary.

The following demonstrates how to configure the Owl JSON file.

{

    \"model\": \"owlv2\",

    \"MainObject\": {

        \"person\": {

            \"conf\": 0.15,

            \"minArea\": 10000

        }

    },

    \"Prompts\": {

        \"face shields\": {

            \"abbr\": \"face shields\",

            \"conf\": 0.3,

            \"minArea\": 2000

        },

        \"white glove\": {

            \"abbr\": \"glove\",

            \"conf\": 0.2,

            \"minArea\": 2000,

            \"pos\": \"LR\"

        },

        \"aluminized heat protection apron\": {

            \"abbr\": \"protection apron\",

            \"conf\": 0.2,

            \"minArea\": 30000

        }

    },

    \"Compl\": \[

        \"plam\"

    \],

    \"Camera\": 0,

    \"CheckPeriod\": 5,

    \"ValidChunkSize\": 3,

    \"ValidRatio\": 0.5,

    \"OKWaiting\": 5,

    \"NGWaiting\": 10,

    \"ROI\": \[

        0.4,

        0.05,

        0.75,

        1.0

    \]

}

MainObject and Prompts specify the primary detection item (which can
only be one) and the supplementary detection items (which can have
multiple entries). Both detection items include the parameters conf and
minArea. The conf parameter indicates that for the model to register a
detection, the confidence level must exceed a specified threshold.
Similarly, minArea requires that the detected object exceeds a certain
area to be considered detected.

The Prompts section has an additional parameter called pos, which should
be set to LR to indicate that the item needs to be detected on both the
left and right sides. The differentiation between left and right is
based on the center of the MainObject.

The Compl parameter is used when you notice that certain items are
frequently misclassified. By adding items that are easily confused, like
adding palm to reduce the chance of misclassifying it as glove, the
model can more accurately differentiate between palm and glove, thereby
decreasing misjudgments.

The parameters CheckPeriod, ValidChunkSize, and ValidRatio are used
together to assess whether the items to be detected are fully
represented. This is done using a sliding window approach. CheckPeriod
represents the total time in seconds spent on detection, while
ValidChunkSize denotes the number of detections in the sliding window.
ValidRatio indicates the proportion of detections needed within that
sliding window to confirm the presence of an item (e.g., if CheckPeriod
is set to 5 seconds, ValidChunkSize is set to 3, and ValidRatio is set
to 0.5, it means that in every group of three detections, at least 1.5
detections must indicate the presence of the item for it to be
considered worn. In this case, all items must be worn during the
5-second CheckPeriod to meet the requirement). Setting ValidRatio to 1
indicates a desire for high accuracy, requiring that the item appears in
every single detection.

OKWaiting and NGWaiting represent the number of seconds that the system
should maintain the OK and NG states after checking. Setting these
values to -1 means that once in that state, it will remain indefinitely.

ROI indicates the area of interest for detection within the frame. You
must provide four values representing the coordinates: top-left x,
top-left y, bottom-right x, and bottom-right y. These values must be
within the range of \[0,1\].

4.  VideoOutput

In the \" Video\" section of the toolbar, find and select the
\"VideoOutput Node.\" Then, make the following settings:

This part can use the default settings. This node is intended to
determine where the output image should be projected and whether any
related settings need adjustment, such as encoding the image into a
specific format.

5.  PiperTTS

![](./images//media/image6.png)

The nodes used are the ones listed above.

For this part, right-click and select presets, then choose PiperTTS to
bring up the text-to-speech system.

Here is how to connect the nodes as described:

1.  **Connect the VideoSource node:**

    -   Connect the right interface of VideoSource to the left interface
        of RateLimit\_1.

2.  **Connect the RateLimit\_1 node:**

    -   Connect the right interface of RateLimit\_1 to the left
        interface of OwlVit\_detector.

3.  **Connect the OwlVit\_detector node:**

    -   Connect the right interface\'s **Image** output of
        OwlVit\_detector to the left interface of VideoOutput.

    -   Connect the right interface\'s **message** output of
        OwlVit\_detector to the left interface of PiperTTS module.

Smoke and Fire detetion (Load Smoke\_alert\_webrtc\_advan/ Fire\_alert\_webrtc\_advan in presets)
-------------------------------------------------------------------------------------------------

Smoke detection and fire detection are common scenarios in public
safety. The following scenario demonstrates how we use the VLM model to
analyze image footage to determine the presence of smoke and fire. Once
detected, an alarm is immediately triggered.

![](./images//media/image10.png)

![](./images//media/image11.png)

The nodes used are the ones listed above.

1.  VideoSource

In the \"Video\" section of the toolbar, find and select the
\"VideoSoruce Node.\" Then, make the following settings:

-   Input: Select the location of your video (e.g., input
    /data/videos/123.MOV). This video should be located under the same
    path in the jetson-containers folder.

-   Loops: Set to -1 to enable continuous playback of the video.

2.  RateLimit

In the \"Video\" section of the toolbar, find and select the \"RateLimit
Node.\" Then, make the following settings:

-   Set Rate to 15: This means 15 frames are processed per second.

-   Leave Chunk empty: Use the default value, no special configuration
    is needed.

-   Set Drop inputs to True: This means only the latest frame is kept,
    while older frames are discarded and not saved.

3.  AutoPrompt (smoke)

In the \"LLM\" section of the toolbar, find and select the \"Autoprompt
Node.\" Then, make the following settings:

-   This is to let the model know that the input prompt should be
    formatted like this. Our application is designed to determine
    whether smoke is present, so our prompt is written as follows: \"
    \<reset\>\<image\>\<image\>\<image\>Does the image show any smoke
    ?Return \"Yes\" if you see any, or \"No\" if there is no visible
    smoke.\" The \<reset\> is a special token that allows us to
    visualize the VILA model\'s output later, ensuring that only the
    results from the current frame are retained without preserving any
    historical results. The \<image\> is the location of the image being
    fed to the model, and the text following it is the question we want
    to ask.

-   seq\_replace\_mode set to true: When this feature is enabled, each
    new input will replace older ones step by step; if disabled, the
    input buffer will clear itself automatically when full.

4.  AutoPrompt (fire)

In the \"LLM\" section of the toolbar, find and select the \"Autoprompt
Node.\" Then, make the following settings:

-   This is to let the model know that the input prompt should be
    formatted like this. Our application is designed to determine
    whether fire is present, so our prompt is written as follows: \"
    \<reset\>\<image\>\<image\>\<image\>Does the image show any fire
    ?Return \"Yes\" if you see any, or \"No\" if there is no visible
    fire .\" The \<reset\> is a special token that allows us to
    visualize the VILA model\'s output later, ensuring that only the
    results from the current frame are retained without preserving any
    historical results. The \<image\> is the location of the image being
    fed to the model, and the text following it is the question we want
    to ask.

-   seq\_replace\_mode set to true: When this feature is enabled, each
    new input will replace older ones step by step; if disabled, the
    input buffer will clear itself automatically when full.

5.  Llama-3-VILA-1.5-8B

In the \"LLM\" section of the toolbar, find and select the \"NanoLLM
Node.\" Then, make the following settings:

-   Model Selection: We chose Efficient-Large-Model/Llama-3-VILA-1.5-8B.

-   API Selection: We opted for MLC as the API, which enhances inference
    speed, allowing the system to respond more quickly.

-   Quantization Setting: We used the default q4f16\_ft quantization, a
    compression technique that reduces computational load while
    maintaining model performance.

-   MaxContextLength use the default setting, by default, inherited from
    the model.

-   Drop inputs set to True: This ensures that only the latest frame is
    processed by the model, discarding older frames to ensure the model
    always processes the most recent information.

-   Chat Template selected as llama-3: This is the foundational setting
    for the model\'s chat system. Choosing a chat template that matches
    the model\'s retraining process is crucial for maintaining model
    performance; otherwise, it can severely impact system performance.

-   System Prompt use the following description: You are a helpful and
    friendly AI assistant.

6.  VideoOverlay

In the \"Video\" section of the toolbar, find and select the
\"VideoOverlay Node.\" Then, make the following settings:

This part can use the default settings. This Node is intended to
influence the font, typeface, and layout of the text output from the
model when rendered on an image.

7.  VideoOutput

In the \" Video\" section of the toolbar, find and select the
\"VideoOutput Node.\" Then, make the following settings:

This part can use the default settings. This node is intended to
determine where the output image should be projected and whether any
related settings need adjustment, such as encoding the image into a
specific format.

8.  One\_Step\_Alert

-   Check Time: Deafultset is 5 seconds. The number of seconds it takes
    to determine the status.

-   Alert: Should be set to true to enable the alert functionality.

-   Alert Keyword: This parameter indicates which keyword should be
    treated as an ambiguious status, we set it to yes.

-   Normal Keyword: The opposite parameter of the alert\_keyword. We set
    it to no.

-   Warning Message Text: As the title, Warning message text. The period
    \".\" should be in the last. So for smoke we set it to \" Warning:
    The smoke is rising." ; for fire we set it to "Warning: The fire is
    rising."

-   Drop inputs set to True: This ensures that only the latest frame is
    processed by the model, discarding older frames to ensure the model
    always processes the most recent information.

9.  PiperTTS

![](./images//media/image6.png)

The nodes used are the ones listed above.

For this part, right-click and select presets, then choose PiperTTS to
bring up the text-to-speech system.

10. TextStream

In the \"LLM\" section of the toolbar, find and select the \" TextStream
Node\" .

Here is how to connect the nodes as described:

1.  **Connect the VideoSource node:**

    -   Connect the right interface of VideoSource to the left interface
        of RateLimit.

2.  **Connect the RateLimit node:**

    -   Connect the right interface of RateLimit to the left interface
        of Autoprompt.

3.  **Connect the AutoPrompt node:**

    -   Connect the right interface of Autoprompt to the left interface
        of Llama-3-VILA1.5-8B.

4.  **Connect the Llama-3-VILA1.5-8B node:**

    -   Connect the right interface\'s **partial** output of
        Llama-3-VILA1.5-8B to the left interface of VideoOverlay.

    -   Connect the right interface\'s **final** output of
        Llama-3-VILA1.5-8B to the left interface of One\_Step\_Alert and
        the left interface of the TextStream.

5.  **Connect the One\_Step\_Alert Node:**

    -   Connect the right interface of One\_Step\_Alert to the left
        interface of PiperTTS module.

Forbidden zone (Load Forbidden\_zone­\_alert\_webrtc\_advan in presets)
-----------------------------------------------------------------------

This application pertains to areas marked with red lines where placing
items is prohibited. The model continuously monitors these areas, and if
any items are placed there for a specified duration, an alarm will be
triggered.

![](./images//media/image12.png)

The nodes used are the ones listed above.

1.  VideoSource

In the \"Video\" section of the toolbar, find and select the
\"VideoSoruce Node.\" Then, make the following settings:

-   Input: Select the location of your video (e.g., input
    /data/videos/123.MOV). This video should be located under the same
    path in the jetson-containers folder.

-   Loops: Set to -1 to enable continuous playback of the video.

2.  RateLimit

In the \"Video\" section of the toolbar, find and select the \"RateLimit
Node.\" Then, make the following settings:

-   Set Rate to 10 This means 10frames are processed per second.

-   Leave Chunk empty: Use the default value, no special configuration
    is needed.

-   Set Drop inputs to True: This means only the latest frame is kept,
    while older frames are discarded and not saved.

3.  AutoPrompt\_ICL

In the \"LLM\" section of the toolbar, find and select the
\"Autoprompt\_ICL Node.\" Then, make the following settings:

-   This is to let the model know that the input prompt should be
    formatted like this. This application is designed to monitor the
    red-lined area in the image where stacking is prohibited, checking
    for any objects placed above this area. We use In-Context Learning
    (ICL) by providing an initial reference image showing the red-lined
    area without any clutter. Additionally, we use the ROI method to
    ensure the model focuses specifically on the prohibited red-lined
    area., so our prompt is written as follows: \"
    \<reset\>\'\'\'/data/images/forbidden\_zone/forbidden\_zone2.png\'\'\'
    In the above image, there is a red X-shaped area marked with tape on
    the ground. In the following image, check if any part of the red X
    shape is obstructed by an object, even partially. In below image:
    \<image\> Can you see the entire X shape pattern?\" Inside the
    triple quotes \'\'\' is the photo path we provide to the model for
    In-Context Learning (ICL). The \<reset\> is a special token that
    allows us to visualize the VILA model\'s output later, ensuring that
    only the results from the current frame are retained without
    preserving any historical results. The \<image\> is the location of
    the image being fed to the model, and the text following it is the
    question we want to ask.

-   seq\_replace\_mode set to true: When this feature is enabled, each
    new input will replace older ones step by step; if disabled, the
    input buffer will clear itself automatically when full.

4.  VILA-1.5-13B

In the \"LLM\" section of the toolbar, find and select the NanoLLM\_ICL
Node.\" Then, make the following settings:

-   Model Selection: We chose Efficient-Large-Model/VILA-1.5-13B.

-   API Selection: We opted for MLC as the API, which enhances inference
    speed, allowing the system to respond more quickly.

-   Quantization Setting: We used the default q4f16\_ft quantization, a
    compression technique that reduces computational load while
    maintaining model performance.

-   MaxContextLength use the default setting, by default, inherited from
    the model.

-   Drop inputs set to True: This ensures that only the latest frame is
    processed by the model, discarding older frames to ensure the model
    always processes the most recent information.

-   Chat Template selected as llava-v1: This is the foundational setting
    for the model\'s chat system. Choosing a chat template that matches
    the model\'s retraining process is crucial for maintaining model
    performance; otherwise, it can severely impact system performance.

-   System Prompt use the following description: A chat between a
    curious human and an artificial intelligence assistant. The
    assistant gives helpful, detailed, and polite answers to the
    human\'s questions.

5.  VideoOverlay

In the \"Video\" section of the toolbar, find and select the
\"VideoOverlay Node.\" Then, make the following settings:

This part can use the default settings. This Node is intended to
influence the font, typeface, and layout of the text output from the
model when rendered on an image.

6.  VideoOutput

In the \" Video\" section of the toolbar, find and select the
\"VideoOutput Node.\" Then, make the following settings:

-   ROI: Set it to true. We need to use ROI in this case.

-   ROI Coordinates: In this case, we set it to 0.75, 0.25, 1, 0.73 to
    focus the model on the red-lined prohibited area.

7.  One\_Step\_Alert

-   Check Time: Deafultset is 5 seconds. The number of seconds it takes
    to determine the status.

-   Alert: Should be set to true to enable the alert functionality.

-   Alert Keyword: This parameter indicates which keyword should be
    treated as an door open, we set it to no.

-   Normal Keyword: The opposite parameter of the alert\_keyword. We set
    it to yes.

-   Warning Message Text: As the title, Warning message text. The period
    \".\" should be in the last. So in this case, we set it to \"
    Warning: Stacking things in forbidden zone.".

-   Drop inputs set to True: This ensures that only the latest frame is
    processed by the model, discarding older frames to ensure the model
    always processes the most recent information.

8.  PiperTTS

![](./images//media/image6.png)

The nodes used are the ones listed above.

For this part, right-click and select presets, then choose PiperTTS to
bring up the text-to-speech system.

Here is how to connect the nodes as described:

1.  **Connect the VideoSource node:**

    -   Connect the right interface of VideoSource to the left interface
        of RateLimit.

2.  **Connect the RateLimit node:**

    -   Connect the right interface of RateLimit to the left interface
        of Autoprompt\_ICL.

3.  **Connect the AutoPrompt\_ICL node:**

    -   Connect the right interface of Autoprompt to the left interface
        of VILA-1.5-13B.

4.  **Connect the VILA-1.5-13B node:**

    -   Connect the right interface\'s **partial** output of VILA1.5-13B
        to the left interface of VideoOverlay.

    -   Connect the right interface\'s **final** output of VILA1.5-13B
        to the left interface of One\_Step\_Alert.

5.  **Connect the One\_Step\_Alert Node:**

    -   Connect the right interface of One\_Step\_Alert to the left
        interface of PiperTTS module.

Reference {#reference .list-paragraph}
=========

\[1\] NVIDIA Jetson Generative AI Lab,
<https://www.jetson-ai-lab.com/index.html>

\[2\] Containers for NVIDIA Jetson,
<https://github.com/dusty-nv/jetson-containers>

\[3\] Docker, <https://docker.com/>

\[4\] Python, <https://www.python.org/>

\[5\] Agent studio, <https://www.jetson-ai-lab.com/agent_studio.html>

Terms and Defintion {#terms-and-defintion .list-paragraph}
===================

-   VLM: Vision Language Model

-   MLC: Machine Learning Compilation

-   V4L2: Video4Linux Version 2
