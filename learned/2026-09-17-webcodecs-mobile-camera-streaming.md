# WebCodecs API - Mobile Browser Camera Streaming Research
**Date**: 2026-09-17
**Topic**: Complete WebCodecs research for mobile camera streaming

---

## 1. WebCodecs Mobile Browser Support (2025-2026)

### Android Chrome
- **Full support from Chrome 94+** (desktop and Android)
- Chrome for Android 151+: Full WebCodecs API support
- Samsung Internet 17.0+: Supported
- UC Browser Android 15.5+: Supported
- **Global usage: 93.6%** (as of 2026)
- Hardware acceleration available on Android via MediaCodec

### iOS Safari
- **Safari 16.4 - 18.7**: Partial support (encoder + decoder for VPx, H.264 via MediaSource backend)
- **Safari 26.0+ (iOS 26)**: FULL support - AudioEncoder + AudioDecoder added
- Safari Technology Preview 157+: Encoder bitrate params, flush, VideoFrame allocation
- Safari TP 247 (July 2026): Hardware video decoder color space override for WebCodecs

### Firefox
- **Firefox 130+**: Supported (desktop)
- Firefox Android 153: NOT supported yet (as of search data)

### Key Limitation
- WebCodecs requires **secure context (HTTPS)** - detection fails if `self.isSecureContext` is false
- Safari 16.4-18.7 partial support means some features work but not all

### Feature Detection Code
```javascript
if ('VideoEncoder' in window) {
  // WebCodecs API is supported
  console.log('WebCodecs available');
} else {
  // Fallback needed
  console.log('WebCodecs NOT available');
}
```

---

## 2. WebCodecs + Canvas Pipeline

### Core Architecture
```
Canvas/ImageBitmap → VideoFrame → VideoEncoder → EncodedVideoChunk → Network/Storage
```

### Three Ways to Create VideoFrame
1. **From Canvas/ImageBitmap/Video element** (most common for camera streaming):
```javascript
const frame = new VideoFrame(canvas, { timestamp: performance.now() * 1000 });
```

2. **From MediaStreamTrackProcessor** (camera stream):
```javascript
const stream = await navigator.mediaDevices.getUserMedia({ video: true });
const track = stream.getVideoTracks()[0];
const processor = new MediaStreamTrackProcessor(track);
const reader = processor.readable.getReader();
```

3. **From raw pixel data (ArrayBuffer)**:
```javascript
const frame = new VideoFrame({
  format: 'RGBA',
  codedWidth: 640,
  codedHeight: 480,
  timestamp: 0,
  data: pixelBuffer
});
```

### Complete Camera Streaming Pipeline
```javascript
// 1. Setup encoder
const encoder = new VideoEncoder({
  output: (chunk, metadata) => {
    // Send chunk to server via WebSocket/HTTP
    sendEncodedChunk(chunk);
  },
  error: (e) => {
    console.error('Encoder error:', e);
    // Re-create encoder if closed
    if (encoder.state === 'closed') {
      setupNewEncoder();
    }
  }
});

// 2. Configure with mobile-friendly codec
encoder.configure({
  codec: 'avc1.42001f',  // H.264 Baseline 720p (99.6% support)
  width: 1280,
  height: 720,
  bitrate: 2_000_000,  // 2 Mbps
  framerate: 30,
  hardwareAcceleration: 'prefer-hardware',  // Use HW on mobile
  latencyMode: 'realtime'  // Optimize for low latency
});

// 3. Capture from camera and encode
async function encodeCameraFrame() {
  const stream = await navigator.mediaDevices.getUserMedia({
    video: { width: 1280, height: 720 }
  });
  const track = stream.getVideoTracks()[0];
  const processor = new MediaStreamTrackProcessor(track);
  const reader = processor.readable.getReader();
  
  let frameCount = 0;
  while (true) {
    const { value: frame, done } = await reader.read();
    if (done) break;
    
    // Backpressure management
    if (encoder.encodeQueueSize > 2) {
      frame.close();  // Drop frame to prevent OOM
      continue;
    }
    
    encoder.encode(frame, {
      keyFrame: frameCount % 60 === 0  // Keyframe every 2 seconds
    });
    frame.close();
    frameCount++;
  }
}
```

### MediaStreamTrackProcessor + Web Worker (Off-Main-Thread)
```javascript
// Main thread
const worker = new Worker('encoder-worker.js');
const stream = await navigator.mediaDevices.getUserMedia({ video: true });
const track = stream.getVideoTracks()[0];

// Transfer track to worker
const generator = new MediaStreamTrackGenerator({ kind: 'video' });
worker.postMessage({ track }, [track]);

// encoder-worker.js
self.onmessage = async (e) => {
  const { track } = e.data;
  const processor = new MediaStreamTrackProcessor(track);
  const reader = processor.readable.getReader();
  
  // Encoder runs entirely in worker - no main thread blocking
  const encoder = new VideoEncoder({
    output: (chunk) => {
      self.postMessage({ chunk, type: 'encoded' });
    },
    error: console.error
  });
  
  encoder.configure({ /* ... */ });
  // ... encoding loop
};
```

---

## 3. H.264 vs VP8 vs VP9 - Codec Selection for Mobile

### Codec Support Chart (Encoding)
| Codec | Chrome | Safari | Firefox | Mobile HW Accel | Best For |
|-------|--------|--------|---------|-----------------|----------|
| H.264 Baseline | 99.6% | 98.9% | 98.9% | Excellent | Maximum compatibility |
| H.264 Main | 98.9% | 98.9% | 98.9% | Excellent | Balance quality/compat |
| VP9 | 99.97% | Limited | 99.97% | Good (Qualcomm) | Better compression |
| AV1 | 87.8% | Limited | 87.8% | Limited (Snapdragon 8 Gen 3+) | Future-proof |
| HEVC | 73.6% | Good | Poor | Apple devices only | iOS-native apps |

### Recommended Codec Strings for Mobile
```javascript
// Maximum compatibility (Android + iOS)
'avc1.42001f'  // H.264 Baseline, 720p max - 99.6% support

// Better quality, still great compatibility
'avc1.4d0034'  // H.264 Main, 4K max - 98.9% support

// Better compression (Android-focused)
'vp09.00.40.08.00'  // VP9 Level 4, 2K max - 99.96% support
```

### Performance Benchmarks (Research Data)
- **H.264 encoding speed**: Superior to VP8 at most resolutions
- **H.264 quality**: Better than VP8 up to 720p
- **VP8**: Better decoding speed, but worse encoding speed than H.264
- **VP9**: Better compression than H.264 (20-30% smaller files at same quality)
- **WebCodecs HW encoding on mobile**: ~25fps at 4K, ~65fps at 1080p (tested on desktop)
- **Mobile HW encoding**: Modern Qualcomm Snapdragon SoCs support H.264/VP8/VP9/AV1 hardware encoding

### Mobile-Specific Recommendations
1. **For streaming/recording**: Use `avc1.42001f` (H.264 Baseline 720p)
2. **For quality on Android**: Use `vp09.00.40.08.00` (VP9 Level 4)
3. **For iOS Safari**: Stick to H.264 (`avc1.4d0034`)
4. **Always test**: `VideoEncoder.isConfigSupported()` before configuring

---

## 4. WebCodecs Limitations & Backpressure

### Critical Limitation: encodeQueueSize
```javascript
// PROBLEM: If you encode faster than the encoder can process
// the queue grows until OOM crash

// SOLUTION: Monitor encodeQueueSize
if (encoder.encodeQueueSize > 2) {
  // Too many frames in flight - DROP THIS FRAME
  frame.close();
  return;
}

// OR use dequeue event for pull-based backpressure
encoder.addEventListener('dequeue', () => {
  // Encoder ready for more frames
  encodeNextFrame();
});
```

### Frame Dropping Strategy
```javascript
class AdaptiveEncoder {
  constructor() {
    this.droppedFrames = 0;
    this.totalFrames = 0;
  }
  
  async encodeFrame(frame) {
    this.totalFrames++;
    
    if (encoder.encodeQueueSize > 2) {
      // Strategy 1: Drop frame
      frame.close();
      this.droppedFrames++;
      
      // Strategy 2: If too many drops, reduce quality
      if (this.droppedFrames / this.totalFrames > 0.1) {
        this.reduceBitrate();
      }
      return;
    }
    
    encoder.encode(frame, {
      keyFrame: this.totalFrames % 60 === 0
    });
    frame.close();
  }
  
  reduceBitrate() {
    // Reconfigure with lower bitrate
    encoder.configure({
      ...currentConfig,
      bitrate: currentConfig.bitrate * 0.7  // Reduce by 30%
    });
  }
}
```

### Memory Management (CRITICAL)
```javascript
// VideoFrame objects are GPU-backed - they consume significant memory
// Applications CRASH with < 100 active frames in memory

// RULE: Always close frames immediately after encoding
const frame = new VideoFrame(canvas, { timestamp });
encoder.encode(frame, { keyFrame: false });
frame.close();  // IMMEDIATELY release GPU memory

// For decoder output:
decoder.output = (frame) => {
  ctx.drawImage(frame, 0, 0);  // Render to canvas
  frame.close();  // Release immediately
};
```

### Error Recovery
```javascript
encoder.addEventListener('error', (e) => {
  console.error('Encoder error:', e);
  
  // Encoder transitions to "closed" state permanently
  // Must create new encoder instance
  if (encoder.state === 'closed') {
    encoder = new VideoEncoder({ /* new config */ });
    encoder.configure({ /* ... */ });
    // First frame MUST be keyframe
    encoder.encode(frame, { keyFrame: true });
  }
});
```

---

## 5. Alternatives to WebCodecs

### MediaRecorder API
```javascript
// Simpler but less control
const stream = canvas.captureStream(25);  // 25 FPS
const recorder = new MediaRecorder(stream, {
  mimeType: 'video/webm; codecs=vp9',
  videoBitsPerSecond: 2_500_000
});

recorder.ondataavailable = (e) => {
  if (e.data.size > 0) {
    // Send chunk to server
    sendChunk(e.data);
  }
};

recorder.start(1000);  // 1-second chunks
```

### Performance Comparison
| Method | Control | Latency | Mobile Support | Use Case |
|--------|---------|---------|----------------|----------|
| WebCodecs VideoEncoder | Frame-level | Lowest | Chrome 94+, Safari 26+ | Professional streaming |
| MediaRecorder | Chunk-level | Medium | Wide support | Simple recording |
| canvas.toBlob() | Per-frame | High (~100-500ms) | Wide support | Image snapshots |
| OffscreenCanvas.convertToBlob() | Per-frame | High (~500-800ms Chrome) | Chrome 69+ | Off-thread snapshots |

### canvas.toBlob Performance Issues (Chrome)
- Chrome: `toBlob()` takes ~1144ms for 1080p canvas (much slower than Safari's ~41ms)
- Chrome: `toDataURL()` + convert takes ~120ms (faster than toBlob!)
- Safari: `toBlob()` is fastest at ~41ms
- **Conclusion**: `toBlob()` is NOT suitable for real-time encoding on Chrome Android

### OffscreenCanvas.convertToBlob
- Chrome: ~500-800ms (too slow for real-time)
- Android WebView: ~8500ms (catastrophically slow!)
- **NOT recommended for real-time streaming**

---

## 6. Hybrid Approach - Feature Detection + Graceful Degradation

### Progressive Enhancement Strategy
```javascript
class VideoStreamManager {
  constructor() {
    this.encoder = null;
    this.mode = null;
    this.init();
  }
  
  async init() {
    // Priority order: WebCodecs > MediaRecorder > Image fallback
    if (this.supportsWebCodecs()) {
      this.mode = 'webcodecs';
      await this.initWebCodecs();
    } else if (this.supportsMediaRecorder()) {
      this.mode = 'mediarecorder';
      await this.initMediaRecorder();
    } else {
      this.mode = 'image-blob';
      this.initImageFallback();
    }
    
    console.log(`Streaming mode: ${this.mode}`);
  }
  
  supportsWebCodecs() {
    return (
      'VideoEncoder' in window &&
      'VideoFrame' in window &&
      self.isSecureContext
    );
  }
  
  supportsMediaRecorder() {
    return 'MediaRecorder' in window;
  }
  
  async initWebCodecs() {
    const supported = await VideoEncoder.isConfigSupported({
      codec: 'avc1.42001f',
      width: 1280,
      height: 720,
      bitrate: 2_000_000,
      framerate: 30
    });
    
    if (!supported) {
      console.warn('H.264 not supported, falling back');
      this.mode = 'mediarecorder';
      await this.initMediaRecorder();
      return;
    }
    
    this.encoder = new VideoEncoder({
      output: (chunk) => this.onEncodedChunk(chunk),
      error: (e) => this.onEncoderError(e)
    });
    
    this.encoder.configure({
      codec: 'avc1.42001f',
      width: 1280,
      height: 720,
      bitrate: 2_000_000,
      framerate: 30,
      hardwareAcceleration: 'prefer-hardware',
      latencyMode: 'realtime'
    });
  }
  
  async initMediaRecorder() {
    const stream = await navigator.mediaDevices.getUserMedia({
      video: { width: 1280, height: 720 }
    });
    
    const mimeType = MediaRecorder.isTypeSupported('video/webm; codecs=vp9')
      ? 'video/webm; codecs=vp9'
      : 'video/webm';
    
    this.recorder = new MediaRecorder(stream, {
      mimeType,
      videoBitsPerSecond: 2_000_000
    });
    
    this.recorder.ondataavailable = (e) => {
      if (e.data.size > 0) {
        this.sendToServer(e.data);
      }
    };
    
    this.recorder.start(1000);  // 1-second chunks
  }
  
  initImageFallback() {
    // Lowest quality fallback - periodic screenshots
    setInterval(() => {
      this.canvas.toBlob((blob) => {
        this.sendToServer(blob);
      }, 'image/jpeg', 0.8);
    }, 500);  // 2 FPS
  }
  
  onEncodedChunk(chunk) {
    const data = new Uint8Array(chunk.byteLength);
    chunk.copyTo(data);
    this.sendToServer(data);
  }
  
  onEncoderError(e) {
    console.error('Encoder error, falling back:', e);
    this.mode = 'mediarecorder';
    this.initMediaRecorder();
  }
}
```

### Feature Detection Utility
```javascript
const VideoCodecSupport = {
  async check() {
    const results = {
      webCodecs: 'VideoEncoder' in window,
      mediaRecorder: 'MediaRecorder' in window,
      secureContext: self.isSecureContext,
      webgl: !!document.createElement('canvas').getContext('webgl2'),
      offscreenCanvas: 'OffscreenCanvas' in window
    };
    
    // Test specific codec support
    if (results.webCodecs) {
      results.h264Baseline = await VideoEncoder.isConfigSupported({
        codec: 'avc1.42001f',
        width: 1280,
        height: 720
      });
      
      results.h264Main = await VideoEncoder.isConfigSupported({
        codec: 'avc1.4d0034',
        width: 1920,
        height: 1080
      });
      
      results.vp9 = await VideoEncoder.isConfigSupported({
        codec: 'vp09.00.40.08.00',
        width: 1920,
        height: 1080
      });
      
      // Test hardware acceleration
      results.hwAccel = await VideoEncoder.isConfigSupported({
        codec: 'avc1.42001f',
        width: 1920,
        height: 1080,
        hardwareAcceleration: 'require'
      });
    }
    
    return results;
  }
};
```

---

## 7. Recommended Architecture for Mobile Camera Streaming

### Best Practice Stack
```
┌─────────────────────────────────────────┐
│           Camera (getUserMedia)          │
├─────────────────────────────────────────┤
│     MediaStreamTrackProcessor           │
│   (converts track to ReadableStream)    │
├─────────────────────────────────────────┤
│         Web Worker (optional)           │
│   (encode off main thread)              │
├─────────────────────────────────────────┤
│   VideoEncoder (WebCodecs)              │
│   - H.264 Baseline for compatibility    │
│   - HW acceleration on mobile           │
│   - Backpressure management             │
├─────────────────────────────────────────┤
│   EncodedVideoChunk → WebSocket/HTTP    │
├─────────────────────────────────────────┤
│   Server → Decode → Display             │
└─────────────────────────────────────────┘
```

### Key Takeaways
1. **WebCodecs is THE way** for real-time camera streaming on mobile
2. **H.264 Baseline** (`avc1.42001f`) for maximum mobile compatibility
3. **Always manage encodeQueueSize** - drop frames when queue > 2
4. **Always close VideoFrame** immediately after encoding
5. **Use hardwareAcceleration: 'prefer-hardware'** on mobile
6. **Feature detect** and fall back to MediaRecorder
7. **HTTPS required** for WebCodecs
8. **iOS Safari 26+** now has full WebCodecs support (game changer!)
9. **MediaRecorder** is the safe fallback for older browsers
10. **Never use canvas.toBlob()** for real-time on Chrome - too slow
