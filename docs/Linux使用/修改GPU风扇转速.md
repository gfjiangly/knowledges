有界面

I found the bulk of my answer [on Ubuntu Forums](http://ubuntuforums.org/showthread.php?t=2114764) but the command to set the fan speed given was wrong. I also wrote the shell script myself (and I'm a noob), so feel free to comment how I did everything wrong, as long as you tell me how to fix it :) I added excessive comments so people who don't understand bash can get an idea of what I'm doing. This only works for 1 GPU, and will need modifications for multiple. By the way, the fan curve I have set is `speed%=0.028*(degreesC^2)`.

## Set-up

1. Run your file browser of choice as root (in my case `sudo konqueror`).

2. Go to `/etc/X11` and allow write access to all groups (but keep the window open).

3. Open up the Nvidia X server settings window (`nvidia-settings` in the terminal).

4. Go to the *nvidia-settings configuration*.

5. Click *Save Current Configuration* and then click save on the pop-up window.

6. Close *Nvidia Settings* and run `sudo nvidia-xconfig`, then again with `--cool-bits=4`.

7. Reboot. （我试验的时候通过界面修改转速似乎不需要重启，但是通过命令行的话需要）

8. 通过界面NVIDIA X Server Settings

   或者命令：nvidia-settings -a "[gpu:0]/GPUFanControlState=1" -a "[fan:0]/GPUTargetFanSpeed=100"（如果失败，错误类型是Error: cannot open display: :0的话export DISPLAY=:0，是ERROR: The control display is undefined的话说明没开启图形界面）

   设置转速

## Script

```sh
#!/bin/bash
# Put "sleep 30" here if you run it at start-up
# to make sure this starts after the Nvidia driver does.

fan="0"
gpu="0"
echo "GPU fan controller service started."
nvidia-settings -a "[gpu:$gpu]/GPUFanControlState=1" > /dev/null
check="$(nvidia-settings -a \"[fan:$fan]/GPUTargetFanSpeed=30\" | tr -d '[[:space:]]')"
working="Attribute'GPUTargetFanSpeed'($(hostname):0fan:$fan)assignedvalue30."

if [[ "$check" != "$working" ]]; then
    echo "error on fan speed assignment: $check"
    echo "Should be: $working"
    exit 1
fi
while true
do
    degreesC="$(nvidia-smi -i $gpu | grep -owEe '[0-9]+C')"
    fanSpeed=$(($degreesC ** 2))
    fanSpeed=$(($fanSpeed / 50))
    if [[ $fanSpeed -gt 100 ]]
    then
        fanSpeed=100
    fi
    nvidia-settings -a "[fan:$fan]/GPUTargetFanSpeed=$fanSpeed" > /dev/null
    sleep 8
done
```

这个脚本我运行一直报错，修改了一下

运行：sudo bash cool_gpu.sh > controller.log 2>&1 &

或者sudo bash cool_gpu.sh &

注意不能关掉图形界面否则脚本报ERROR: The control display is undefined; please run `nvidia-settings

无法修改风扇转速，此时用的驱动程序默认设置