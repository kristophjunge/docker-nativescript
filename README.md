# Docker NativeScript

[![DockerHub Pulls](https://img.shields.io/docker/pulls/kristophjunge/nativescript.svg)](https://hub.docker.com/r/kristophjunge/nativescript/) [![DockerHub Stars](https://img.shields.io/docker/stars/kristophjunge/nativescript.svg)](https://hub.docker.com/r/kristophjunge/nativescript/) [![GitHub Stars](https://img.shields.io/github/stars/kristophjunge/docker-nativescript.svg?label=github%20stars)](https://github.com/kristophjunge/docker-nativescript) [![GitHub Forks](https://img.shields.io/github/forks/kristophjunge/docker-nativescript.svg?label=github%20forks)](https://github.com/kristophjunge/docker-nativescript) [![GitHub License](https://img.shields.io/github/license/kristophjunge/docker-nativescript.svg)](https://github.com/kristophjunge/docker-nativescript)

[![NativeScript](https://raw.githubusercontent.com/kristophjunge/docker-nativescript/master/nativescript.png)](https://www.nativescript.org/) [![MediaWiki](https://raw.githubusercontent.com/kristophjunge/docker-nativescript/master/android.png)](https://developer.android.com/)

Docker container with NativeScript and Android SDK.
Can be used for build automation and to run a docker based development environment.
The container is based on the official Ubuntu [image](https://hub.docker.com/_/ubuntu/).

- [NativeScript](https://www.nativescript.org/) (2.5.0)
- [Android SDK Tools](https://developer.android.com/) (25.2.5)
- [Android SDK Platform](https://developer.android.com/) (25)
- [NodeJS](https://nodejs.org/) (7.5.0)
- [Java](https://www.java.com/) (1.8.0)
- [Ubuntu](https://www.ubuntu.com/) (16.04 LTS)


## Usage


### Development

The `--privileged` flag will make the USB devices of your host available to the container.
That way you can run your NativeScript app on a real Android device from a docker container.
Please note that you need to have USB debugging enabled.
The usage of an emulator is currently not possible.

Change `my-app` to your app folder location.
Connect an Android device and use the following command to create and run a new NativeScript application:

```
docker run -it --rm --privilged \
-v /my-app/:/app \
kristophjunge/nativescript \
bash -c "tns create my-app && mv ./my-app/* ./ && rm -r ./my-app && tns platform add android && tns run android"
```

Note that `tns create` will create a subdirectory for the application.
In the example above the app folder contents will be moved up.


### Development with Live Sync

You can have your changes live synchronized on your device while you are developing.

Connect an Android device and run the command:

```bash
docker run -it --rm --privileged \
-v /my-app:/app \
kristophjunge/nativescript \
tns livesync android --watch
```

To setup your development environment with `docker-compose` you can use the following snippet in a `docker-compose.yml` file:

```
version: '2'
services:
  app:
    image: kristophjunge/nativescript
    privileged: true
    volumes:
      - "./:/app"
    command: tns livesync android --watch
```


### Build Automation

You don't need a connected Android device or running emulator to build an APK.
You can build an APK with only NativeScript and the Android SDK or with this docker image.

Please note that you need a release key to build a signed Android APK package with NativeScript.
Look [here](https://developer.android.com/studio/publish/app-signing.html#signing-manually) to find out how to generate a key.
Read the [NativeScript documentation](https://docs.nativescript.org/angular/publishing/publishing-android-apps) to learn more about building for Android.

Mount your NativeScript application root directory, the directory where the final APK should be deployed and your release key.

Execute the build:

```bash
docker run --rm -it \
-v /my-app:/app \
-v /my-target-folder:/dist \
-v /my-release-key.jks:/key.jks \
kristophjunge/nativescript \
tns build android --release \
--key-store-path /key.jks \
--key-store-password SECRET_KEY_STORE_PASSWORD \
--key-store-alias KEY_ALIAS \
--key-store-alias-password SECRET_KEY_ALIAS_PASSWORD \
--copy-to /dist/release.apk
```

Replace `SECRET_KEY_STORE_PASSWORD`,`KEY_ALIAS` and `SECRET_KEY_ALIAS_PASSWORD` with the values of your release key.

To suppress the usage statistics dialog of the `tns` command you can prefix the command with the answer like that:

```
echo "n" | tns build android # ...
```


## License

This project is licensed under the MIT license by Kristoph Junge.


## Known issues

- You currently can not run your app in an Android emulator. You can only use a real device for testing with this docker image. However building for Android does't require a device or emulator.
