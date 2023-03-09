`libuvc` is a cross-platform library for USB video devices, built atop `libusb`.
It enables fine-grained control over USB video devices exporting the standard USB Video Class
(UVC) interface, enabling developers to write drivers for previously unsupported devices,
or just access UVC devices in a generic fashion.

## Getting and Building libuvc

Prerequisites: You will need `libusb` and [CMake](http://www.cmake.org/) installed.

To build, you can just run these shell commands:

    git clone https://github.com/libuvc/libuvc
    cd libuvc
    mkdir build
    cd build
    cmake ..
    make && sudo make install

and you're set! If you want to change the build configuration, you can edit `CMakeCache.txt`
in the build directory, or use a CMake GUI to make the desired changes.

There is also `BUILD_EXAMPLE` and `BUILD_TEST` options to enable the compilation of `example` and `uvc_test` programs. To use them, replace the `cmake ..` command above with `cmake .. -DBUILD_TEST=ON -DBUILD_EXAMPLE=ON`.
Then you can start them with `./example` and `./uvc_test` respectively. Note that you need OpenCV to build the later (for displaying image).

## Developing with libuvc

The documentation for `libuvc` can currently be found at https://int80k.com/libuvc/doc/.

Happy hacking!



## Lucien
1. 修改了get_mode: Invalid mode (-51) ：https://github.com/libuvc/libuvc/issues/242
2. master中为了避免overflow，stream.c把比frame多余的字节舍掉了，这样读到的datasize = width*height*2，imu的数据就丢掉了
```
  if (data_len > 0) {
    // 注释掉下面
    //if (strmh->got_bytes + data_len > strmh->cur_ctrl.dwMaxVideoFrameSize)
    //  data_len = strmh->cur_ctrl.dwMaxVideoFrameSize - strmh->got_bytes; /* Avoid overflow. */
    memcpy(strmh->outbuf + strmh->got_bytes, payload + header_len, data_len);
    strmh->got_bytes += data_len;
    if (header_info & (1 << 1) || strmh->got_bytes == strmh->cur_ctrl.dwMaxVideoFrameSize) {
      /* The EOF bit is set, so publish the complete frame */
      _uvc_swap_buffers(strmh);
    }
  }
```