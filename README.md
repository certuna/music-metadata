[![Build Status][travis-image]][travis-url] [![NPM version][npm-image]][npm-url] [![npm downloads][npm-downloads-image]][npm-url]

Streaming music metadata parser for node and the browser.

Installation
------------
Install via [npm](http://npmjs.org):

```
npm install music-metadata
```

You can also download a pre packaged browser release from `dist/music-metadata.js`.
See `example/index.html` for usage.


Supports
-----------------
* mp3 (1.1, 2.2, 2.3, 2.4)
* m4a (mp4)
* vorbis (ogg, flac)
* asf (wma, wmv)
* MonkeyAudio, APEv2 (ape)


API
-----------------
```javascript
var fs = require('fs');
var mm = require('music-metadata');

// create a new parser from a node ReadStream
var parser = mm(fs.createReadStream('sample.mp3'), {native=true, duration=true}function (err, metadata) {
  if (err) throw err;
  console.log(metadata);
});
```

This will output the standard music metadata:

```javascript
{
  format:
  {
     duration : 302.41 // in seconds,
     bitrate: 44100,
     bitsPerSample: 16,
     tagType, 'id3v2.4',
     numberOfChannels: 2
  }
  common:
  {
     artist : ['Spor'],
     album : 'Nightlife, Vol 5.',
     albumartist : [ 'Andy C', 'Spor' ],
     title : 'Stronger',
     year : '2010',
     track : { no : 1, of : 44 },
     disk : { no : 1, of : 2 },
     genre : ['Drum & Bass'],
     label: 'RAM Records',
     musicbrainz_albumid: 'a6da0420-f7ec-4b47-9e5a-9b0f33eeb8f2',
     picture : [ { format : 'jpg', data : <Buffer> } ]
  },
  'id3v2.4': // as a result of 'native=true'
  {
     TPE1 : ['Spor'],
     TALB : 'Nightlife, Vol 5.',
     TPE2 : [ 'Andy C', 'Spor' ],
     TIT2 : 'Stronger',
     TYER : '2010',
     TRCK : '1/44',
     TPOS : '1/2',
     TCON : ['Drum & Bass'],
     TPUB : 'RAM Records',
     'TXXX:MusicBrainz Album Id': 'a6da0420-f7ec-4b47-9e5a-9b0f33eeb8f2'
     ...
  }
}
```

Note, the stream is not closed by default. To prevent leaks, you must close it yourself:
```javascript
var readableStream = fs.createReadStream('sample.mp3');
var parser = mm(readableStream, function (err, metadata) {
  if (err) throw err;
  readableStream.close();
});
```

`music-metadata` also emits all metadata it discovers during parsing. For example if you wanted to read the `TLEN` frame from an id3v2.x file you can do this:

```javascript
parser.on('TLEN', function (result) {
  console.log(result);
});
```

You can also read the duration; to calculate the duration `music-metadata` may need to parse the entire file
so only enable this if you need the functionality.
```javascript
mm(fs.createReadStream('sample.mp3'), { duration: true }, function (err, metadata) {

});
```

Note that in order to read the duration for streams that are not file streams, you must also pass the size of the file in bytes.
```javascript
mm(fs.createReadStream('sample.mp3'), { duration: true, fileSize: 26838 }, function (err, metadata) {

});
```

Licence
-----------------

(The MIT License)

Copyright (c) 2016 Borewit

Based on [musicmetadata] (https://github.com/leetreveil/musicmetadata/) written by Lee Treveil <leetreveil@gmail.com> and many others.

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the 'Software'), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED 'AS IS', WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

[npm-url]: https://npmjs.org/package/music-metadata
[npm-image]: https://badge.fury.io/js/music-metadata.svg
[npm-downloads-image]: http://img.shields.io/npm/dm/music-metadata.svg

[travis-url]: https://travis-ci.org/profile/Borewit/music-metadata
[travis-image]: https://api.travis-ci.org/Borewit/music-metadata.svg?branch=master
