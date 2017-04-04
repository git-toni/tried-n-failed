## Compute Waveform of Audio track from frontend

Given a remote `src`, make the browser read the file, and incrementally "draw" the waveform.

I've got:
- Audiofile duration. 
- WebAudio's `AudioContent` processing pipeline

Problems:

- `CORS`
  - Cannot receive header field `Content-Length` unless server allows it
  - Cannot receive file chunk by chunk via `Range: bytes=100-1800` header(req) unless server allows via `Allow-Ranges`


Code:

```javascript 
// Get contentlength
//var xmlhttp = new XMLHttpRequest();
  //xmlhttp.overrideMimeType('text/xml');

  xmlhttp.onreadystatechange = function () {
    if (xmlhttp.readyState == 4 && xmlhttp.status == 200) {
      console.log(xmlhttp.getAllResponseHeaders())
      console.log('clen',xmlhttp.getResponseHeader('Content-Length'))
      //var size = xmlhttp.getResponseHeader('Content-Length');//get file size
      //fileSize = size
      //var kbit=size/128;//calculate bytes to kbit
      //var kbps= Math.ceil(Math.round(kbit/duration)/16)*16;
      //console.log(kbps);
    }
  };
  xmlhttp.open("HEAD", audioUrl, true);
  //xmlhttp.setRequestHeader("Access-Control-Expose-Headers","Content-Length")

  xmlhttp.send();
  
//OR
axios.head(audioUrl).then((res)=> res.getAllResponseHeaders())
```
Processing Pipeline:

```javascript
let url ='https://archive.org/download/foot004/foot004_01-zucchini-snow.mp3'
let aud = new Audio(url);
aud.preload='auto'

let ctx = new AudioContext()
var analyser = ctx.createAnalyser();

function onLoad(){
  var sourceNode = ctx.createMediaElementSource(aud)
  sourceNode.connect(analyser);
  analyser.connect(ctx.destination);
  getData()
  
}
var canvas  = document.createElement('canvas')
function getData(){
  //var dataArray = new Uint8Array(2048);
  //analyser.getByteTimeDomainData(dataArray);
  var freqByteData = new Uint8Array(analyser.frequencyBinCount);
  analyser.getByteFrequencyData(freqByteData);
  console.log(freqByteData)
  //analyser.getByteTimeDomainData(freqByteData);

  //console.log(dataArray)
  //setTimeout(()=> getData(),1000)
}
window.addEventListener('load', onLoad, false)
//aud.addEventListener('progress',()=> console.log('progress'),false)
///console.log(aud.buffered.start(0)))

aud.addEventListener('loadedmetadata', ()=>{
  console.log('METADATA DONE',aud.duration)
})

aud.addEventListener('canplay',()=>{
  //console.log('i can play')
  //aud.play()
})
```
