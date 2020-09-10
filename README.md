# `node-libpd`

> Node.js binding for [`lib-pd`](https://github.com/libpd/libpd) that uses [`portaudio`](http://www.portaudio.com/) as audio backend.

## Table of Content

<!-- toc -->

- [Notes / Caveats:](#notes--caveats)
- [Install](#install)
  * [Install on Mac OSX](#install-on-mac-osx)
  * [Install on Raspberry Pi](#install-on-raspberry-pi)
- [Usage](#usage)
- [API](#api)
  * [pd : object](#pd--object)
- [Tests](#tests)
- [Todos](#todos)
- [Credits](#credits)
- [License](#license)

<!-- tocstop -->

## Notes / Caveats:

- The library is meant to be used in a _Node.js_ environment, it cannot run in a browser and never will.
- The library can only be used with pd-vanilla objects, it does not, and maybe will never, support externals.
- The bindings are created with N-API, therefore v1 should work on Node.js > 10.x.x, for previous version of Node.js you should install node-libpd v0.2.6 that was created with Nan (be aware that this version won't receive support).

_Tested on MAC OSX 10 and Raspbian Stretch Lite version 9 (raspberry pi 3) - for other platforms, dynamic libraries for libpd and portaudio should probably be built._

## Install

```
npm install [--save] node-libpd
```

### Install on Mac OSX

```
xcode-select --install
```

### Install on Raspberry Pi

```
apt-get install -y ... ???
```

## Usage

```js
import pd from 'node-libpd';

// init pd
const initialized = pd.init({
  numInputChannels: 0,
  numOutputChannels: 2,
  sampleRate: 48000,
  ticks: 1, // a pd block (or tick) is 64 samples, be aware that increasing this value will throttle messages
});

// 
const patchesPath = path.join(process.cwd(), 'my-patches');
const patch = pd.openPatch('my-patch.pd', patchesPath);

// subscribe to messages from the patchd
pd.subscribe(`${patch.$0}-output`, function(msg) {
  console.log(msg);
});

// send message to the patch
pd.send(`${patch.$0}-input`, 1234);

// send a scheduled message
const now = pd.currentTime; // time in sec.
// send message to the patch in two seconds from `now`, this accuracy of the
// scheduling will depend on the number of ticks defined at initialization
pd.send(`${patch.$0}-input`, 1234, now + 2);

// close the patch
pd.close(`${patch.$0}-input`, 1234);
```

## API

<!-- api -->

<a name="pd"></a>

### pd : <code>object</code>
**Kind**: global namespace  

<!-- apistop -->

## Tests

To run the tests

```sh
# cf. test/index.js
npm run test
```

## Todos

- support pd externals (see if only it's possible...)
- rebuild libpd without jack

## Credits

The library has received support from the Ircam's project BeCoMe.

## License

BSD-3-Clause
