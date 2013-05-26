heroku-buildpack-libraries
===========================
A generic builpack to deploy libraries on Heroku

This buildpack is built to be used through [heroku-buildpack-multi](https://github.com/ddollar/heroku-buildpack-multi).
In your app you need to:
```
heroku config:set BUILDPACK_URL=https://github.com/ddollar/heroku-buildpack-multi
```

Then, create a `.buildpacks` file inside your app:
```
https://github.com/heroku/_YOUR_MAIN_BUILDPACK
https://github.com/marcolinux/heroku-buildpack-libraries
```
See the documentation of heroku-build-multi for a detailed explanation how to use it.

## Decrease the slug size
It is possible to make some corrections to the original heroku buildpack. In particular, add check for a `.buildpack-ignore` and remove files not needed, decreasing the slug size. This is useful in particular fro removing temporary or test directories from Ruby Gems.

## Build on Vulcan
Scripts to build on Vulcan is available under the `/support` folder.
it is necessary to create beforehands a [vulcan](https://github.com/heroku/vulcan) build server.
```
gem install vulcan
vulcan create _your_vulcan_server_
```
The binaries are available at the moment are:

1. Tesseract-OCR (3.02)

2. OpenCV (2.4.5)

3. CMake (2.8.11)

4. ImageMagick (6.8.5-8)

## Configuration
A file called `.buildpacks_bin_download` need to be added containing the full path of the tar.gz file with the binaries compiled with Vulcan, and options if necessary (options specified below).
```
binary binary_path binary_options
```

`binary` is the name of the binary/library to install, binary_path is the path were the executables/libraries are stored,.
Example:
  `https://s3.amazonaws.com/_your_builded_bins.tar.gz`

It is possible to access the binaries compiled with Vulcan and hosted on Heroku Vulcan server, with a similar configuration.
As path it is necessary to specify the result of the vulcan build process, a path similar to  `http://_your_vulcan_server_.herokuapp.com/output/_compiled_hash`

## Build on the fly
A file called `.buildpacks_bin_build` need to be added containing the name of the binaries to be compiled, and options if necessary (options specified below).
```
binary binary_options
```
Note that the on the fly compile might take long time.

### Special config for the binaries
#### Tesseract-OCR
It is necessary to specify an additional version number (necessary for getting the training datas)
An optional comma-separated string can be added for download additional languages than English
```
tesseract-ocr https://s3.amazonaws.com/tesseract-ocr/heroku/tesseract-ocr-3.02.02.tar.gz 3.02 eng,jpn
```

#### OpenCV
A patch is made to correctly export the pkgconfig file for production enviroments.
It is possible to build OpenCV with a pre-build version of CMake as follows:
```
vulcanBuild opencv --cmake cmake_prebuilt_lib
```

For using OpenCV with Nodejs, a dedicated buildpack-multi is available [here](https://github.com/marcolinux/heroku-buildpack-multi).

In your app you need to set the buildpack to the customized buildpack:
```
heroku config:set BUILDPACK_URL=https://github.com/marcolinux/heroku-buildpack-multi
```
Then, create a `.buildpacks` file inside your app:
```
https://github.com/marcolinux/heroku-buildpack-libraries
https://github.com/heroku/heroku-buildpack-nodejs
```
The order is important, the library need to be installed before calling the Nodejs buildpack
Finally, a file called `.buildpacks_bin_download` need to be added containing the full path of the tar.gz file with the libraries compile with Vulcan
```
opencv _vulcan_or_amazon_s3_path
```

## License
MIT License. Copyright 2013 Marco Azimonti.
