# Docker Trusted Registry (DTR)

When you install the UCP, you have access to install the DTR from the Admin Settings of the UCP.

## Installation Requirements

- Minimum 16Gb memory, 2 CPUs, 10Gb disk
- Recommended 16Gb memory, 4 CPUs, 25-100Gb disk

To install the DTR, you must have at least two nodes in your cluster. It won't work if you try to install it on the same node as the UCP - they both use port 443, so you'll be unable to access the DTR.

## Installing the DTR

1. On the left menu, click admin ➤ Admin Settings ➤ Docker Trusted Registry.
2. Write the IP of the second node in the DTR external URL.
3. Choose the second node in UCP node.    
4. Select PEM-CA.
5. Copy the generated code.
6. docker-machine ssh `<second node for DTR>`
7. Then paste the generated code.
8. The DTR should be installed with TLS for the `<second node URL>`.

When the DTR is successfully installed, go again to admin ➤ Admin Settings ➤ Docker Trusted Registry. It will display the IP address of the second node. Copy and paste in the browser. The DTR IP will be displayed.

## Pushing an Image to the DTR Repository
To try pushing an image to the DTR, you can first pull an image using `docker pull <image name>` and re-tag it to have the DTR URL or IP address using `docker tag <image name> <IP/user/image name>`. 

Log in to the DTR using the CLI using `docker login <DTR IP address>`. 

The final step is to push the image using `docker push <IP/user/image name>`.

```
docker pull busybox

docker tag busybox 172.19.126.247/admin/firstimage

docker login 172.19.126.247

docker push 172.19.126.247/admin/firstimage
```

Verify that the image has been submitted correctly by clicking on the left menu Repositories ➤ Tags or Images.

## Immutable Images
Setting the image as immutable prevents it from being overwritten and deleted. This option is usually selected for any image after being scanned and promoted. If any user tried to delete this image, an error would be generated. To set the immutability on, select from the left menu Repositories ➤ Settings ➤ Immutability tab.

## Image Pruning

Pruning removes the unused images automatically by setting the pruning policies. Select it from the left menu Repositories ➤ Pruning tab.