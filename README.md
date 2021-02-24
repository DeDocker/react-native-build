# About
Docker images used to build mobile apps on Android and iOS

## Contains
- based on Ubuntu 18.04
- android SDK
- ruby 2.5.1 and gem with some preinstalled packages (fastlane)
- gradle
- node 14.15.5 and yarn

## Usage

### Android

- build aab using fastlane
`docker run --rm -it -v "$PWD:/usr/app/src" -w /usr/app/src/ react-native-build:android /bin/sh -c "bundle install; bundle exec fastlane build_aab"`
