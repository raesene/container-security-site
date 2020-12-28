# Container Image Hardening

Improving the security of container images, generally focuses on removing unecessary software to reduce the attack surface. In addition to this, avoiding risky software installation practices is a good idea if you're building production container images and for all images, avoiding using the root user will be important.

## Attack surface reduction

There's a number of options for reducing your container image attack surface.

### "Scratch" base image

This is essentially an almost empty base image with no package management or other operating system libraries. Whether this is a practical option for a given image largely depends on how the application you want to run in the container works. For a scratch image to be usuable, your application needs to be able to run without any supporting operating system libraries.

Things like statically compiled Golang or ASP.Net Core applications can often work in a scratch containers, where others which use a lot of supporting libraries, are unlikely to have an easy time using this approach.

### Google Distroless