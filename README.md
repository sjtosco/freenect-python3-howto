# freenect-python3-howto
How to compile freenect in debian 11 and python3.This is valid for Kinect v1 (XBOX 360), the old one ;-)

## Prerequisites

```
sudo apt install freeglut3-dev libusb-1.0-0-dev libopengl-dev cmake -y
```

## Build and install
``` bash
git clone https://github.com/OpenKinect/libfreenect
cd libfreenect
mkdir build
cd build
cmake .. -DBUILD_PYTHON3=ON -DCYTHON_EXECUTABLE=/usr/bin/cython3 -DPython3_EXACTVERSION=3.9.2
make -j4
sudo make install
```

### Fixes

For use freenect in global python3 interpreter:

```
sudo ln -s /usr/local/lib/python3/dist-packages/freenect.so /usr/local/lib/python3.9/dist-packages/
```

If trows "ImportError: libfreenect_sync.so.0: cannot open shared object file: No such file or directory"

```
sudo cp /usr/local/lib/libfreenect.* /usr/lib/ 
```

For use in virtualenv, when use virtualenvwrapper:

```
workon my-venv
add2virtualenv /usr/local/lib/python3/dist-packages/
```

## Uninstall

`sudo make uninstall`

Check for orphans files in `/usr/local/lib`, `/usr/local/lib/python3` and `/usr/local/lib/python3.9` folders.


## Demo script

```
import freenect
import cv2
import numpy as np

"""
Grabs a depth map from the Kinect sensor and creates an image from it.
"""
def getDepthMap():	
    depth, timestamp = freenect.sync_get_depth()
    np.clip(depth, 0, 2**10 - 1, depth)
    depth >>= 2
    depth = depth.astype(np.uint8)
    return depth

print("Press <ESC> key to exit...")
while True:
    depth = getDepthMap()
    blur = cv2.GaussianBlur(depth, (5, 5), 0)
    cv2.imshow('image (<ESC> to exit)', blur)
    if cv2.waitKey(10) == 27:
        break
print("Bye!")
```
