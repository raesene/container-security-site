# Defenders - Container Image Hardening

Improving the security of container images, generally focuses on removing unecessary software to reduce the attack surface. In addition to this, avoiding risky software installation practices is a good idea if you're building production container images and for all images, avoiding using the root user will be important.

## Attack surface reduction

There's a number of options for reducing your container image attack surface.

### "Scratch" base image

This is essentially an almost empty base image with no package management or other operating system libraries. Whether this is a practical option for a given image largely depends on how the application you want to run in the container works. For a scratch image to be usuable, your application needs to be able to run without any supporting operating system libraries.

Things like statically compiled Golang or ASP.Net Core applications can often work in a scratch containers, where others which use a lot of supporting libraries, are unlikely to have an easy time using this approach.

### Google Distroless

[Google Distroless images](https://github.com/GoogleContainerTools/distroless) provide a very small but more functional image than scratch. They include some important files like timezone files and SSL certificates. These files mean they have a wider/easier compatibility than scratch images, but they are still very small and have a very limited attack surface.

### Alpine

[Alpine Linux](https://www.alpinelinux.org/) is a popular option for smaller Container images. It has the advantage of small base image, but keeps the option of easily adding new operating system packages where needed. There can be some compatibility challenges as they default to using `musl` rather than `gcc` for C libraries, but alpine based images have pretty wide compatibility.

### Wolfi 

[Wolfi OS](https://github.com/wolfi-dev) is a project dedicated to producing minimal container images. The package manager is based on Alpine Linux's APK and there's support for `glibc` and `musl`. It's a newer option than the alternatives mentioned here but worth considering to see if it's a good fit for your use case.