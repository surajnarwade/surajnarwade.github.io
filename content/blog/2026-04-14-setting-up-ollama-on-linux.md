+++
title = "Setting Up Ollama on Linux"
slug = "setting-up-ollama-on-linux"
description = "A practical guide to installing and running Ollama on Linux to serve local LLMs."
author = "Suraj Narwade"
date = "2026-04-14"
category = "Blog"
tags = ["linux", "ollama", "llm", "ai"]
+++

Running LLMs (large language models) locally is becoming more practical every month. Ollama makes it straightforward to pull, run, and manage models on your own hardware without needing a cloud API key. In this post, I will walk through getting Ollama up and running on a Linux machine.

## What is Ollama?

[Ollama](https://ollama.com/) is an open-source tool that lets you run LLMs locally. It wraps model weights, configuration, and a serving layer into a single workflow. You can think of it like Docker but for language models. You pull a model, run it, and interact with it through the terminal or an HTTP API.

## Installing Ollama

The quickest way to install Ollama on Linux is the official install script:

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

This downloads the binary, sets up a systemd service, and gets everything ready.

| Note: If you prefer not to pipe scripts into your shell, you can grab the binary manually from the [Ollama GitHub releases](https://github.com/ollama/ollama/releases) page and place it in your PATH. you will have to set up the systemd unit manually.

Once installed, verify it:

```bash
ollama --version
```

You should see the version number printed to your terminal.

We can see all the available commands using regular help flag,

```
ollama -h
```

## Starting the Ollama Service

The install script sets up a systemd service that starts automatically. You can check its status with:

```bash
sudo systemctl status ollama
```

If it is not running, start it:

```bash
sudo systemctl start ollama
```

To make sure it starts on boot:

```bash
sudo systemctl enable ollama
```

Let's quickly look at systemd unit, it's basically running a server with `ollama serve` command,

```
❯ sudo systemctl cat ollama
# /etc/systemd/system/ollama.service
[Unit]
Description=Ollama Service
After=network-online.target

[Service]
ExecStart=/usr/local/bin/ollama serve
User=ollama
Group=ollama
Restart=always
RestartSec=3
Environment="PATH=/usr/local/bin:/usr/bin:/usr/bin/site_perl:/usr/bin/vendor_perl>

[Install]
WantedBy=default.target
```

## Pulling a Model

Now for the fun part. Let us pull a model and start chatting with it.

```bash
ollama pull gemma4:e2b
```

This downloads the model weights. Depending on the model size and your internet speed, this might take a few minutes.


## Listing Available Models

To see which models you have pulled locally:

```bash
ollama list
```

This shows the model name, size, and when it was last modified.

Here's the sample output,

```
❯ ollama list
NAME              ID              SIZE      MODIFIED
qwen3.5:latest    6488c96fa5fa    6.6 GB    2 days ago
gemma4:e2b        7fbdbf8f5e45    7.2 GB    6 days ago
```

To browse all available models, check the [Ollama model library](https://ollama.com/library).

## Running the model

Once the pull is complete, you can run the model:

```
ollama run gemma4:e2b
```

You will get an interactive prompt where you can start typing messages and the model responds directly in your terminal. Type `/bye` or `/exit` to exit the session.

You can also run ad-hoc prompt,

```
ollama run gemma4:e2b "hi, how are you?"
```

It will answer and close the session automatically.

Some models have thinking capability by-default, you can disable it by following flag,

```
ollama run gemma4:e2b --think=false
```

## Using the HTTP API

Ollama also exposes a local HTTP API on port 11434. This is useful if you want to integrate it into your own tools or scripts.

```bash
curl http://localhost:11434/api/generate -d '{
  "model": "gemma4:e2b",
  "prompt": "What is the capital of France?",
  "stream": false
}'
```

The response comes back as JSON with the model's output in the `response` field. Setting `stream` to `false` returns the full response at once instead of streaming tokens.

## Managing Models

Remove a model you no longer need:

```bash
ollama rm llama3.2
```

## Checking Resource Usage

Running models locally uses a fair amount of RAM. A rough guide:

- 7B parameter models need around 8 GB of RAM
- 13B parameter models need around 16 GB of RAM
- 70B parameter models need 64 GB or more

Keep an eye on memory usage with:

```
ollama ps
```

This shows which models are currently loaded in memory along with their resource usage.

## Troubleshooting/Upgrading

### Upgrading ollama

On Linux, you simply need to re-run the installation script and it will upgrade ollama for you.

### Accessing ollama from other machine

You may have Linux miniPC or a server on which you have deployed ollama and you want to access it from your laptop, then we need to change the host setting in ollama which can be easily done by tweaking systemd unit.

* Run `systemctl edit ollama.service`this will open your editor.
* Add following config, it this section already present then only add OLLAMA_HOST variable

```
[Service]
Environment="OLLAMA_HOST=0.0.0.0:11434"
```

* you can now save the file and reload systemd and restart ollama,

```
sudo systemd daemon-reload
systemctl restart ollama
```

## Wrapping Up

Ollama makes running LLMs on Linux simple. Install it, pull a model, and you are up and running in minutes. The local HTTP API is handy for building tools on top of it. If you have a decent Memory & GPU, the inference speeds are surprisingly good for local use. In upcoming blog posts, we will see more interesting things around running local LLMs.
