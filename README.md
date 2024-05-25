## This image is no longer maintained

Last version: 1.6 (mid May 2024)

## Why?

A few months ago, int2001 (aka. DJ7NT) from the Wavelog team started to work on an official Docker image. This image was initially based on his Dockerfile. Since then, quite a bit changed. The key feature of my image (and why I made my own) was the built in support for Cronjobs to automate certain tasks (such as uploads to services like QRZ, ClubLog and what not).

Since then, Cronjobs have now been a part of the official image. Thanks to Jörg for implementing my hack. Wavelog also has a built-in Cronjob manager now.

Due to that, what else can I add? The official image now does all I ever wanted and needed.

Thank you very much, dear Wavelog team. You've done a great job!

To the few people who seem to have used my image, thank you.

## What now?

It should be no problem to simply exchange my image with the official image. If you used the Docker compose I provided, you just have to replace the image line with

``` yaml
    image: ghcr.io/wavelog/wavelog
```

## Sources

- This Docker:

    [jk13xyz/wavelog_-_docker](https://github.com/jk13xyz/wavelog_docker)

- Wavelog:

    [wavelog/wavelog](https://github.com/wavelog/wavelog)

- wavelog_docker

    [int2001/wavelog_docker](https://github.com/int2001/wavelog_docker)