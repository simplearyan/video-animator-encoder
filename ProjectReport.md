Canvas Pro Animator - Export Pipeline

A high-performance, serverless video export engine running entirely in the browser using WebCodecs, Web Workers, and WASM.

Project Overview

This project demonstrates how to generate professional 1080p/60fps video files client-side without expensive cloud rendering servers. It solves common problems like browser freezing, codec incompatibility, and CORS restrictions using a multi-method fallback system.

Export Methods Compared

Method

Name

Tech Stack

Pros

Cons

Best For

A

MP4 (Smart)

WebCodecs + mp4-muxer

Hardware Accelerated (Fastest), Small File Size

H.264 support varies by OS (Linux issues)

Mobile Sharing, Social Media

B

WebM (Native)

WebCodecs + webm-muxer

100% Browser Support, Alpha Channel

Files can be large, VP9 encoding can be slow

Web backgrounds, Transparent Video

C

MP4 (Software)

FFmpeg.wasm

100% Reliable (Software Encode)

Slowest (2-5x slower than native), High Memory

Fallback when Method A fails

D

Media Bunny

MediaRecorder (Slice Mode)

Very Fast, Low Memory

Variable Frame Rate (VFR), Lower Quality

Quick Drafts, Previews

E

Real-Time

canvas.captureStream()

WYSIWYG (Records what you see)

Requires powerful GPU to maintain FPS

Live Interactions, Gaming

Libraries Used

mp4-muxer / webm-muxer: Lightweight JS libraries to package raw video chunks into containers.

FFmpeg.wasm: A WebAssembly port of FFmpeg for robust software encoding.

Tailwind CSS: For the responsive, glassmorphism UI.

Key Technical Solutions

1. The "15-Second Freeze" Fix

Encoding 1080p video generates massive amounts of data. If pushed to the main thread, the UI locks up.
Solution: We moved all encoding logic to a Web Worker. Inside the worker, we use yieldToMain() (a zero-delay Promise) every few frames to let the Javascript Event Loop breathe, preventing the browser from marking the tab as "Unresponsive."

2. The CORS Worker Fix

Loading FFmpeg in a worker usually triggers security errors.
Solution: We fetch the FFmpeg library text manually and inject it via a local Blob URL, completely bypassing strict CORS checks on CDN scripts.

3. H.264 Fallback

Not all browsers support Main/High profile H.264.
Solution: Method A automatically iterates through 6 different H.264 profiles (including Baseline and Constrained) to find one your device supports. If all fail, it auto-switches to Method C (FFmpeg).