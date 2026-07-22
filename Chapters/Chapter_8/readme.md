Layered Architecture of Docker 
Layer 1: Base Ubuntu Layer             120MB
Layer 2: Changes in apt packages       306MB
Layer 3: Changes in pip packages       6.3MB
Layer 4: Source code                   229 B
Layer 5: Update Entrypoint             0 B

- There are 2 applications they are very similar and one application now has image and so the docker reuses the layers of already built image for the new image of second application . It also applies in versioning if u r deplying new version of the application.  

-Images are  read only you can modify them only building new images .


docker run mumshad/my-custom-app
-When u run this command it creates container/writable layer on top of the image layer. it is used to store data created by the container layer. Life of this layer is as long as container is alive. any new file i want to create it will create in temp.txt

-COPY ON WRITE : Docker creates a copy of application in container layer there we do any modification , but it will not modified in the image layers , image layers will be the same . You to re build the image after modification. If we get rid of the container all the date save in the container also get deleted so to persist this data we use volumes


There are two types mounting : 1-Volume Mounting 2-Bind Mounting

Volume Mounting:
it mounts the data to the container from /var/lib/docker/volumes    it is a default location
-docker volume create data_volume
it creates the volume under /var/lib/docker
then we can mount this volume into the container layer    docker run -v volume_name:location_of_the_store_data image_name  -> docker run -v data_volume:/var/lib/mysql mysql
now even if the container is destroyed the data will still active 

BIND MOUNTING
What if we have data on another locatiion like database we have to mount data on differet location not on default location
  docker run -v /data/mysql"/var/lib/mysql mysql 
It will create the container and bind that data into that container  

-v is deprecated now u can write this command 
docker run --mount type=bind, source=/data/mysql,target=/var/lib/mysql mysql

-----------------------------------------

who is responsible maintaining the layer architecture ,making writable layer , moving files across the layers its is "Storage Drivers". Storage Drivers are responsible to enable the layer architecture. Some of the common storage drivers are AUFS ZFS BTRFS Device Mapper Overlay2 . Selection of the storage base on the underline OS like for ubuntu the default storage driver is AUFS . Docker choose best storage driver based on the operating system . sTORAGE DRIVERS help in managing storage in different containers 


------------VOLUME DRIVER PLUGINS IN DOCKER -----------------------------
STORAGE DRIVERS help in managing storage in different containers . Storage Drivers does not help in managing volume drivers. volume is handled by volume driver plugins  The default volume driver plugin is "local" it helps in store date in /var/lib/docker/volume. There are many other volume driver plugin that allow you to create volumes on third party solutins like azure file storage ,canvoy , digital ocean, bloxkstorage flocker ,gce-docker , GlusterFS ,NetApp, RexRay, PortWorx, VMware, vsphere storage. while running the container u can choose ur desired third party storage driver to crate volume on third party .

docker run -it --name mysql  --mount src=ebs-vol,target=/var/lib/mysql mysql 

it will create the volume on AWS EBS(third party volume mounted in container)


------------------------------VOLUMES IN KUBERNETES---------------------------------
Container are tansiant in nature ,containers  are required to finish as they completed their task

----------------------------Persistent Volumes---------------------------------------

