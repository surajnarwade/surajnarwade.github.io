+++
title = "How to Check Temperature on Raspberry Pi"
slug = "how-to-check-temp-raspberry-pi"
author = "Suraj Narwade"
date = "2026-02-25"
category = "Blog"
+++

Keeping an eye on your Raspberry Pi temperature is important, especially if you are running heavy tasks like media servers, coding projects, or home automation. Overheating can cause throttling and reduce performance.

## Why Temperature Matters

The Raspberry Pi automatically reduces CPU speed if it gets too hot, usually above 80°C. Monitoring the temperature helps you:

- Prevent performance drops  
- Avoid unexpected shutdowns  
- Decide if you need a heatsink or fan  

## Quick Way to Check Temperature

Open the Terminal and run:

```bash
vcgencmd measure_temp
```

you will see an output like:

```
temp=48.5'C
```

That is your current CPU temperature.

Alternative Method

You can also run:

```
cat /sys/class/thermal/thermal_zone0/temp
```

This shows the temperature in millidegrees. For example:

```
48532
```

Divide by 1000 to get the temperature in Celsius.

```
48.5°C
```

simplified command can be:

```
echo "$(cat /sys/class/thermal/thermal_zone0/temp) / 1000" | bc -l
```

## What Is a Safe Temperature?

* 40 to 60°C → Normal

* 60 to 80°C → Warm but safe

* 80°C and above → Throttling may begin

If your Pi runs hot, consider adding:

* A heatsink

* A cooling fan

* Better airflow in your case

That is it. Simple and quick.
