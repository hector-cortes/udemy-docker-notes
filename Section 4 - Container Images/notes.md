# Section 36: What's in an Image (and what isn't) 
* App binaries and dependencies
* Metadata about the image data and how to run the image
* Not a complete OS. No kernel, kernel modules (e.g. drivers)
  * small as a single file (app binary) like a golang static binary
  * large as an ubuntu distro with apt, Apache, PHP, more installed

# Section 37: Using Docker Hub Registry Images
https://github.com/docker-library/official-images/tree/master/library

# Section 38: Images and their Layers: Discover the Image Cache
* Images are made up of file system changes and metadata
* Each layer is uniquely identified and only stored once on host
  * saves storage space on host and transfer time on pull / push
* a container is just a single read/write layer on top of image
* Resources
  * https://docs.docker.com/storage/storagedriver/